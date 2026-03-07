---
layout: research
title: "RAG Systems: A Comprehensive Survey"
date: 2026-02-10
tags: [RAG, Retrieval, Survey]
research_type: "Survey Analysis"
paper_title: "Retrieval-Augmented Generation for Large Language Models: A Survey"
paper_authors: "Gao et al., 2024"
paper_url: "https://arxiv.org/abs/2312.10997"
---

This survey covers the RAG landscape comprehensively. Here's what practitioners need to know.

## RAG Fundamentals

Retrieval-Augmented Generation combines a **retriever** (finds relevant documents) with a **generator** (LLM that conditions on retrieved context).

This addresses key LLM limitations: knowledge cutoff, hallucination, and domain specificity.

## Retrieval Strategies

### Dense Retrieval

- **Bi-encoders**: Separate encoders for queries and documents
- **Cross-encoders**: Joint encoding (more accurate, less scalable)
- **Hybrid**: Combine dense and sparse (BM25) retrieval

### Chunking Strategies

- **Fixed-size chunks**: Simple but may break semantic units
- **Semantic chunking**: Split on natural boundaries
- **Hierarchical**: Multiple granularities for different query types

## Advanced Techniques

- **Query expansion**: Add related terms
- **HyDE**: Generate hypothetical document, embed that
- **Reranking**: Two-stage retrieval for better precision
- **Iterative retrieval**: Retrieve-generate-retrieve cycles

## Practical Recommendations

1. Start simple: Basic RAG before advanced techniques
2. Invest in chunking: Often the highest-impact optimization
3. Add reranking: Significant quality improvement
4. Monitor retrieval quality: Many issues trace back here
5. Iterate on prompts: How you present context matters

RAG remains the most practical way to extend LLM capabilities with external knowledge.
