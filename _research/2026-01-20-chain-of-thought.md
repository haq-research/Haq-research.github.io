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

Chain-of-thought (CoT) prompting has become a standard technique for improving LLM reasoning. But when does it actually help?

## The Core Idea

Instead of asking for direct answers, prompt the model to show its reasoning step by step.

## When CoT Helps

The paper shows CoT primarily helps on:

- **Multi-step arithmetic**: Problems requiring sequential calculations
- **Symbolic reasoning**: Logic puzzles, constraint satisfaction
- **Commonsense reasoning**: Tasks requiring world knowledge integration

CoT shows minimal improvement on simple factual recall or single-step problems.

## Why It Works

- **Decomposition**: Breaking complex problems into easier subproblems
- **Working Memory**: The chain serves as external working memory
- **Error Localization**: Mistakes become visible and correctable

## Practical Variants

- **Zero-shot CoT**: Just add "Let's think step by step"
- **Self-Consistency**: Generate multiple chains, majority vote
- **Tree of Thoughts**: Explore multiple reasoning branches

## When to Use CoT

**Use CoT when:**
- Problem requires multiple steps
- Accuracy is more important than speed
- You want interpretable reasoning

**Skip CoT when:**
- Simple factual queries
- Latency is critical

Chain-of-thought is powerful but not universally applicable. Match the strategy to your task.
