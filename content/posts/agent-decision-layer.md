---
title: "Agents Don't Need More LLM Calls. They Need a Decision Layer."
slug: "agents-dont-need-more-llm-calls-they-need-a-decision-layer"
date: 2026-10-04
tags: ["AI Systems", "Agentic AI", "LLM Architecture", "System Design"]
summary: "Most LLM calls inside an agent loop aren't reasoning, they're decisions over a known option set. Splitting them into guards, a calibrated decision model, and escalation changes cost, latency, and what the runtime can trust."
---

Most LLM calls inside an agent loop aren't reasoning. They're decisions.

The standard loop is well understood: plan, pick a tool or agent, act, check the result, decide whether to keep going. In most frameworks one model does all of it. The same LLM that writes the plan also routes the request, grades its own output, and decides when to stop.

The step that rarely gets examined is the last three. Routing, judging and continuing are not open ended problems. Each one is a typed answer drawn from a small, known set of options. Call that set of calls the decision layer. It is usually the largest share of LLM calls in a loop, and it is the part least designed for what it actually does.

![One iteration of a typical agent loop: plan, route, act, judge, continue. Three of the four LLM calls are decisions.](/images/agent-decision-layer/fig1-agent-loop-decisions.svg)

## 1. What a decision call actually needs

A decision call has a different contract from a reasoning call. It needs four things:

- **A bounded answer.** One of `continue | retry | switch | complete`, not a paragraph that might contain one of them.
- **A usable uncertainty signal.** The runtime has to know whether to act on the answer or route it somewhere else.
- **Low latency.** It sits on the critical path of every iteration.
- **Independence from the worker.** A step that grades its own output is not a check.

An LLM meets the first only through structured output, misses the third because it generates token by token, and misses the fourth whenever the same model judges what it just produced.

The second is the one that matters most and gets discussed least. An LLM's answer arrives as text. Its stated confidence ("I'm 90% sure") is a generated string, not a measured quantity. Its token probabilities are closer to useful, but post training is known to degrade the calibration of a base model's probabilities, and the probability of the first token of a label is not the probability of the decision. So the runtime receives an answer with no reliable way to tell a 0.95 decision from a 0.55 one. Every answer gets treated the same, which in practice means every answer gets trusted.

## 2. What Jev actually is

Jev is not a rules engine. It is a learned, probabilistic model, and that is the point: most routing and continuation decisions depend on raw text that rules can't read, and an LLM is the expensive way to read it.

TypeSafe AI released Jev 1.13.0 in September 2026 as the first of its "System One" models. The contract, from the documentation:

- **Input is a state.** Text: a string, a JSON object, or an array. No images, audio or video. English is the primary language; others work with lower accuracy.
- **Questions are typed.** Three primitives. `Choice` picks one of named options. `Score` returns a value on an ordered scale. `Noul` returns a yes/no probability.
- **Answers come back typed, with probabilities.** No free text to parse. Multiple questions are evaluated together in one request rather than generated one after another.
- **Probabilities are trained for calibration.** TypeSafe describes the training as reinforcement learning against outcomes, so that a 0.9 means something different from a 0.55.

Confidence is defined per primitive, and the definitions are worth knowing because thresholds depend on them:

```text
Choice:  confidence = (p_max - 1/n) / (1 - 1/n)        n = number of options
Score:   confidence = max(0, 1 - Σ p_i·|i - m| / MAD_uniform)
Noul:    no separate field; use p directly, or |2p - 1| to match the Choice scale
```

`Choice` confidence is normalized, so 0 means a uniform spread and 1 means all mass on one option. That matters later when checking calibration.

Published operating limits for 1.13.0:

```text
Endpoint        POST /v1/systemone
Context         64K tokens per request (state + all questions)
                32K tokens for state + the longest single question
Rate limits     100K tokens/s, 80 requests/s (429 beyond either)
Price           $0.042 per 1M input tokens, output free
Data            not trained on customer requests; enterprise ZDR available
Aliases         jev-latest → jev-1.13.0  (pin the version in production)
```

It is reachable through TypeSafe's own SDKs, through OpenRouter as `typesafe/jev-1.13`, through Vercel's AI Gateway via the AI SDK's experimental `evaluate` API, and through adapters in TanStack AI and LangChain4j.

## 3. Jev as a router

A router built on an `intent` field assumes something upstream already produced that field, usually an LLM call. A router built on Jev reads the raw request directly:

```python
from typesafe_sdk import TypeSafeClient, Choice

client = TypeSafeClient()  # reads TYPESAFE_API_KEY

resp = client.system_one(
    state={"request": user_message, "account_tier": tier},
    questions={
        "agent": Choice(
            instructions="Which agent should handle this request?",
            criteria={
                "crm":   "Lookup or change on an existing customer record",
                "rag":   "Answerable from internal policy and product documents",
                "web":   "Needs public information newer than internal documents",
                "human": "Complaint, legal threat, or anything outside these agents",
            },
        ),
    },
)

route = resp.choices["agent"]
if route.confidence < 0.5:
    send_to_triage(user_message)          # genuine ambiguity, not a guess
else:
    dispatch(route.choice, user_message)
```

Two properties fall out of this that an LLM router doesn't give for free. The output space is closed, so there is no parsing step and no invented fifth agent. And the routing log stops being "the model chose web" and becomes "web, confidence 0.83, state hash `a91f…`", which can be replayed and tested.

The criteria descriptions do the work that a system prompt does for an LLM router. They are the part to iterate on.

## 4. Jev as the loop governor

The most expensive failure in an agent loop is not a wrong answer. It is a loop that doesn't recognize it has finished, or doesn't recognize it has stalled, and keeps spending. `MAX_ITERATIONS = 10` bounds the damage but doesn't fix the cause: an agent that succeeded at iteration 2 still runs to 10 if nothing notices.

The fix is to take the continue/stop decision away from the component doing the work. Each iteration, the runtime runs the cascade below.

![The decision cascade inside one loop step: guards in code, one Jev call answering three questions, then the confidence band decides who acts.](/images/agent-decision-layer/fig3-decision-cascade.svg)

In code:

```python
from typesafe_sdk import Choice, Noul, Score

def govern(state: dict) -> Decision:
    # 1. Guards: exact, cheap, never a model
    if state["iteration"] >= MAX_ITERATIONS:
        return Decision("ESCALATE", reason="iteration cap")
    if state["spend_usd"] >= BUDGET_USD:
        return Decision("FAIL", reason="budget exhausted")
    if not matches_schema(state["last_output"]):
        return Decision("RETRY", reason="schema violation")

    # 2. One Jev call, three questions answered together
    r = client.system_one(
        state=compact(state),   # goal, last two outputs, failures, evidence summary
        questions={
            "done": Noul(instructions="Does the latest output fully satisfy the goal?"),
            "next_step": Choice(
                instructions="What should the agent do next?",
                criteria={
                    "continue": "Current approach is producing new, relevant evidence",
                    "retry":    "Last step failed for a transient or fixable reason",
                    "switch":   "Current approach has stopped producing new evidence",
                    "complete": "The goal is met",
                },
            ),
            "progress": Score(
                instructions="How far did the last iteration move toward the goal?",
                criteria=["none", "some", "substantial"],
            ),
        },
    )
    p_done = r.nouls["done"].noul
    step   = r.choices["next_step"]

    # Stall detection across iterations, not within one
    state["stall"] = state["stall"] + 1 if r.scores["progress"].score < 0.5 else 0

    # 3. Confidence decides who acts
    if p_done >= 0.9 and step.choice == "complete":
        return Decision("COMPLETE")
    if state["stall"] >= 2:
        return Decision("SWITCH", reason="no progress for two iterations")
    if step.confidence < 0.5 or 0.35 < p_done < 0.65:
        return Decision("ESCALATE", reason="low confidence")   # LLM judge or human
    return Decision(step.choice.upper(), confidence=step.confidence)
```

The three questions cross check each other. `done` at 0.31 with `next_step = complete` is a contradiction, and the governor treats it as one instead of picking whichever answer it read first.

The obvious objection: batch the same three questions into one LLM call with structured output. That removes the call count. It doesn't remove the problem, because the three fields come back as generated tokens with no calibrated probability attached. The runtime still can't tell which answers to act on, so it still acts on all of them.

The thresholds above follow TypeSafe's suggested bands (below 0.5 to a human, 0.5 to 0.9 depending on stakes, above 0.9 automatic). They are starting points. Section 7 covers how to set them properly.

## 5. Jev as a judge, and where it stops being one

Judging splits into three kinds, and each belongs to a different mechanism:

- **STRUCTURAL.** Valid JSON, required fields present, record counts above zero, no tool errors. Guards.
- **SHALLOW SEMANTIC.** Does the answer address the question that was asked? Did the extraction pull a value for every field the document contains? Is the tone appropriate for a customer reply? A `Noul` or `Score` on a compact state. Jev.
- **DEEP SEMANTIC.** Is every claim supported by the retrieved evidence, across thirty passages? Is the clinical reasoning sound? This needs the evidence in context and step by step comparison. LLM judge, or a person.

The judge cascade is the same shape as the governor: structural checks first, a cheap calibrated judge second, the expensive judge only on what the cheap one can't settle. The share of traffic that reaches the third tier is the number to watch. If it isn't small, the decision model isn't earning its place.

## 6. From one loop to a graph of loops

Once the control plane owns transitions, the agent stops being one loop with retries and becomes a graph of specialized loops with explicit edges.

![A graph of loops. Planning routes to a RAG loop, stalled progress switches to web and then API loops, and any loop can complete, escalate, or fail.](/images/agent-decision-layer/fig4-graph-of-loops.svg)

The question each step answers changes from "should this loop continue?" to "which state comes next?" Written as a transition system:

```text
S(t)      workflow state: goal, evidence, iteration, spend, failures, stall
A(t)    = LLM(S(t))                       proposed action, open ended
D(t)    = Guards(S(t), A(t))              if any invariant is violated
        | Jev(S(t), A(t)) → (d, c)        otherwise, with confidence c
        | Escalate(S(t), A(t))            if c < θ
S(t+1)  = Transition(S(t), D(t))          applied by the runtime, never the LLM

D(t) ∈ { CONTINUE, RETRY, SWITCH, ROUTE, ESCALATE, COMPLETE, FAIL }
```

The LLM proposes. The control plane decides. The runtime enforces. Every edge in the graph corresponds to a logged decision with an input state, a value, and a confidence, which is what makes the graph debuggable after the fact.

## 7. Where this breaks

**CALIBRATION IS A PROPERTY OF A DISTRIBUTION.** A model calibrated on TypeSafe's evaluation data is not automatically calibrated on a specific domain's state. Thresholds are only meaningful after measuring it on a labeled golden set. Because `Choice` confidence is normalized, convert it back to the chosen option's probability first:

```python
import numpy as np

def p_chosen(confidence: float, n_options: int) -> float:
    # invert confidence = (p_max - 1/n) / (1 - 1/n)
    return confidence * (1 - 1 / n_options) + 1 / n_options

def expected_calibration_error(p, correct, bins=10):
    p, correct = np.asarray(p), np.asarray(correct, dtype=float)
    edges = np.linspace(0, 1, bins + 1)
    ece = 0.0
    for lo, hi in zip(edges[:-1], edges[1:]):
        m = (p > lo) & (p <= hi)
        if m.any():
            ece += m.mean() * abs(correct[m].mean() - p[m].mean())
    return ece
```

If decisions reported at 0.9 are right 70% of the time, the threshold has to move, or that decision type isn't ready to automate.

**THE STATE IS THE NEW BOTTLENECK.** The 32K limit on state plus the longest question means a long running agent's history has to be compacted before every decision. A decision made on a badly summarized state is confidently wrong in a way no threshold catches. Designing `compact(state)` is real engineering work, not a utility function.

**NO REASONING IS RETURNED.** Jev returns answers and probabilities, not an explanation. The audit trail has to be built around it: state snapshot, questions, criteria, answers, probabilities, model version. In regulated environments, that record is the explanation.

**A NEW DEPENDENCY ON THE CRITICAL PATH.** Every iteration now makes a network call to a second provider. A timeout needs a defined fallback, usually guards plus escalation, never "assume continue." Pin `jev-1.13.0`, since aliases can move to a new version at any time.

**TEXT ONLY, ENGLISH FIRST.** Decisions that depend on a screenshot, a scanned page, or non English input still need a reasoning model or a preprocessing step.

**THE GAP MAY NARROW.** As general models get faster, cheaper and better at structured output, the cost argument weakens. The calibration argument only weakens if general models start returning probabilities that can be trusted, which is a separate problem from getting cheaper.

## 8. The design rule

The decision layer was always there. Every agent loop already routes, judges and decides when to stop. The only question is whether those decisions are designed or left to the same model that does the work.

Put invariants in code. Put routine decisions in a model that returns a typed answer and an honest probability. Send only the uncertain residue to an LLM or a person. The LLM proposes, the control plane decides, and the runtime enforces.

The single variable that separates a governed agent from an expensive loop isn't intelligence. It's whether the system knows when it's unsure.

---

**Sources**

- [TypeSafe docs: System One models](https://docs.typesafe.ai/concepts/system-one)
- [TypeSafe docs: Confidence](https://docs.typesafe.ai/confidence)
- [TypeSafe docs: Models, limits and pricing](https://docs.typesafe.ai/models)
- [TypeSafe Python SDK](https://docs.typesafe.ai/sdk/python)
- [OpenRouter: Jev 1.13](https://openrouter.ai/typesafe/jev-1.13)
- [Vercel changelog: TypeSafe AI's Jev on AI Gateway](https://vercel.com/changelog/typesafe-ai-jev-now-available-on-ai-gateway)
- [LangChain4j: TypeSafe decision models](https://docs.langchain4j.dev/integrations/decision-models/typesafe)
- [The Neuron: TypeSafe's JEV explained](https://www.theneuron.ai/explainer-articles/typesafe-jev-system-one-models-explained/)
- [OpenAI, GPT-4 Technical Report (calibration before and after post training)](https://arxiv.org/abs/2303.08774)
