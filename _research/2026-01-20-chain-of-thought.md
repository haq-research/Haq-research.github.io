---
layout: research
title: "Chain-of-Thought Prompting: When and Why It Works"
date: 2026-01-20
tags: [Prompting, Reasoning, LLM]
research_type: "Paper Review"
paper_title: "Chain-of-Thought Prompting Elicits Reasoning in Large Language Models"
paper_authors: "Wei et al., 2022"
paper_url: "https://arxiv.org/abs/2201.11903"
---

Chain-of-thought (CoT) prompting has become a standard technique for improving LLM reasoning. But when does it actually help, and why?

## The Core Idea

Instead of asking for direct answers, prompt the model to show its reasoning:

**Standard prompting:**
```
Q: Roger has 5 tennis balls. He buys 2 cans of 3. How many does he have?
A: 11
```

**Chain-of-thought:**
```
Q: Roger has 5 tennis balls. He buys 2 cans of 3. How many does he have?
A: Roger starts with 5 balls. 2 cans × 3 balls = 6 balls. 5 + 6 = 11 balls.
```

## When CoT Helps

The paper shows CoT primarily helps on:

1. **Multi-step arithmetic**: Problems requiring sequential calculations
2. **Symbolic reasoning**: Logic puzzles, constraint satisfaction
3. **Commonsense reasoning**: Tasks requiring world knowledge integration

CoT shows **minimal improvement** on:

- Simple factual recall
- Single-step problems
- Tasks without clear reasoning chains

## Why It Works

Several hypotheses:

### Decomposition

Breaking complex problems into steps reduces cognitive load at each step. The model solves easier subproblems.

### Working Memory

The chain of thought serves as external working memory. Intermediate results are explicitly stored in context.

### Error Localization

Mistakes become visible and potentially correctable. Useful for debugging and self-correction.

## Practical Variants

### Zero-shot CoT

Simply add "Let's think step by step" to prompts. Surprisingly effective without examples.

### Self-Consistency

Generate multiple reasoning chains, take majority vote on final answer. Improves accuracy but increases cost.

### Tree of Thoughts

Explore multiple reasoning branches, backtrack when stuck. More complex but handles harder problems.

## Implementation Considerations

For production systems:

1. **Cost**: CoT uses more tokens. Budget for 2-5x output length.
2. **Latency**: Longer outputs = higher latency. Stream if possible.
3. **Parsing**: Extract final answers from reasoning chains reliably.
4. **Verification**: Reasoning chains can be wrong even with correct answers.

## When to Use CoT

**Use CoT when:**
- Problem requires multiple steps
- Accuracy is more important than speed
- You want interpretable reasoning

**Skip CoT when:**
- Simple factual queries
- Latency is critical
- Output parsing is difficult

Chain-of-thought is a powerful technique, but it's not universally applicable. Match the prompting strategy to your task requirements.
