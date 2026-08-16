---
title: "DoorDash: Using LLMs to Build Content Embeddings for Search and Recommendations"
date: 2026-08-09
tags: ["AI Systems", "Search", "Recommendation Systems", "Embeddings"]
summary: "How DoorDash moved from behavioral embeddings to LLM generated content profiles, and what it actually moved in search and recommendation metrics."
---

I came across DoorDash's engineering post on how they rebuilt their search and recommendation embeddings using LLMs, and it's a good example of a team finding that their real bottleneck wasn't model quality, it was what they were feeding the model in the first place.

**The problem with behavioral data alone**

DoorDash's older embeddings leaned on behavioral signals, mainly co-visitation data, what gets ordered together. That works fine for capturing patterns, but it struggles with semantic nuance. Their example: it can't reliably tell a spicy Sichuan noodle soup apart from a delicate Cantonese wonton broth, even though both are technically "noodle soups."

That weakness showed up hardest in three places: cold start situations where a new item or store has no order history yet, fair surfacing for small merchants who simply don't generate enough behavioral data volume, and cross vertical discovery, connecting related items across food, grocery, retail, and gifting.

**Switching to content first embeddings**

Instead of leaning primarily on engagement data, they shifted to generating rich, LLM written narrative profiles as the main input to embeddings. Their reasoning made sense to me: DoorDash is a weekly, transactional ordering business, not an infinite scroll platform, so it never generates the kind of continuous interaction stream that pure behavioral embeddings depend on.

The pipeline looks like this:
1. A daily ETL job pulls order history, ratings, menu metadata, and merchant attributes.
2. LLMs turn that into standardized text profiles describing ingredients, preparation style, cuisine, and dietary attributes.
3. Vision language models generate text descriptions from item photos when available.
4. Profiles get regenerated incrementally, through Metaflow, whenever the underlying content changes.
5. Off the shelf embedding models encode these profiles into 256 dimensional vectors, using Matryoshka Representation Learning to keep them compact and efficient.

They also split embedding tasks into two types: symmetric `SEMANTIC_SIMILARITY` for entity to entity comparisons, and asymmetric `RETRIEVAL_QUERY` and `RETRIEVAL_DOCUMENT` pairs for search.

**How they evaluated it**

Large scale human annotation is expensive, so the team used an LLM as judge approach to build golden evaluation datasets, then measured candidate models on Hit Rate at K, nDCG at K, semantic fidelity, latency, and index efficiency relative to embedding size.

**What the numbers actually showed**

For item to item similarity, swapping in a better model alone, MiniLM to gemini-embedding-001, only got them plus 5.92% on Hit at 5. Swapping raw metadata for LLM generated profiles, keeping the same embedding model, got them plus 31.22%. Doing both together got plus 37.55%. In other words, what you feed the model mattered a lot more than which model you used.

For store to store similarity they ran a clean 2x2 test. A data upgrade alone was plus 161% on Hit at 5. A model upgrade alone was also plus 161%. Combining both pushed it to plus 209%, meaning data quality and model quality were contributing independently rather than overlapping.

For query to entity retrieval, using asymmetric task types improved relevance, and gemini-embedding-001 generalized well across head, torso, and tail query frequency tiers.

**Where it moved the needle in production**

Semantic search, meaning store level embedding based retrieval, gave a plus 0.0724% lift in seven day active customer share, a 3.65% reduction in null search rate, especially helpful for rare and tail queries, and a plus 0.66% increase in core search session conversion rate.

Adding item level embeddings plus a fine tuned Qwen 3 reranker gave a 7.8% ranking quality improvement for dish specific queries and a 1.4% improvement for cuisine queries, and let them show the actual most relevant item photo instead of a generic store image.

On the homepage, co-purchase carousels built from semantic embeddings produced cleaner cuisine clustering than the old behavioral method, giving a plus 0.435% increase in trial merchant visit rate and a plus 0.110% increase in homepage clicks per impression.

They also built generative personalized carousels, where an LLM generates carousel themes from a consumer's profile and context, then retrieves the nearest neighbor stores via embeddings. That gave a plus 2.4% increase in homepage order rate, a plus 0.164% increase in seven day reorder rate, a plus 0.32% increase in variable profit per order, and pushed offline precision at 10 from 68% to 85%.

**Where content embeddings don't work as cleanly: consumers**

Items and stores have a coherent, describable identity that maps naturally to text, ingredients, cuisine, prep style. Consumers don't work the same way. A person's preferences live in their behavior over time, not a static description. Someone who loves both fiery Sichuan food and delicate sushi can't be captured well by a single averaged text profile, because averaging just erases the distinctions that make personalization useful in the first place.

The team's conclusion is that good consumer representations need context conditioning, varying by time, location, and occasion, rather than one fixed profile per person.

**What they're looking at next**

- Semantic IDs, discretizing the embedding space into codes so you can do sequence modeling over meaning instead of relying on brittle entity IDs.
- Generative retrieval, predicting item identifiers token by token instead of doing nearest neighbor search.
- A retriever generator architecture, using embedding retrieval as context for a downstream reranker or generator.
- Continuous learning, treating profiles as living representations that improve from usage feedback instead of static, one time generations.

**The stack**

| Category | Details |
|---|---|
| Embedding models | gemini-embedding-001, OpenAI text-embedding-3 and text-embedding-005, MiniLM, Qwen embeddings |
| Reranker | Qwen3-Reranker-4B, fine tuned |
| Infrastructure | Metaflow for incremental inference |
| Vision language | Gemini VLMs for image to text descriptions |
| Embedding dimensionality | 256 dim, via Matryoshka Representation Learning |
| Evaluation | LLM as judge for golden datasets, Hit at K and nDCG at K metrics |

What stands out to me most is the ordering of their findings: the model upgrade helped, but the data upgrade, turning raw behavioral signals into rich, LLM written content profiles, helped far more, and the two combined more than either alone. It's a good reminder that better representation of your content usually beats a better model sitting on top of bad representation.

*Source: [DoorDash Engineering, Using LLMs to Build Content Embeddings for Search and Recommendations](https://careersatdoordash.com/blog/doordash-llms-to-build-content-embeddings-for-search-and-recommendations/)*
