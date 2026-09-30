# Chunking & retrieval guide

Retrieval quality decides RAG quality. This guide covers the two halves: getting text into the index well (chunking) and getting the right chunks back out (retrieval).

## Chunking strategies

| Strategy | How it works | When to use |
|---|---|---|
| Fixed-size | Split every N tokens/characters | Baseline; rarely the best answer |
| Recursive | Split on separators (paragraph → sentence → word) down to size | Good default (~512 tokens, 10–20% overlap) |
| Semantic | Embed sentences, split where similarity drops | Heterogeneous docs with topic shifts |
| Structure-aware | Split on headings, tables, document layout | Technical docs, PDFs with real structure |
| Proposition-level | One atomic fact per chunk | Dense factual corpora, eval-heavy setups |

Guidelines:
- **Overlap** (10–20%) preserves context at boundaries; it costs index size, not quality.
- **Chunk size is a retrieval trade-off:** small chunks → precise matches, fragmented context; large chunks → more context per hit, noisier ranking. 256–512 tokens is the usual sweet spot.
- **Metadata is a first-class citizen:** page numbers, section titles, timestamps, and source URLs enable filtering and citation — store them at chunk time.
- **Tables and figures** need special handling: a real document parser ([Docling](https://github.com/docling-project/docling), [Unstructured](https://github.com/Unstructured-IO/unstructured), [LlamaParse](https://www.llamaindex.ai/llamaparse)) beats regex extraction.

Tooling: [Chonkie](https://github.com/feyninc/chonkie) is a dedicated chunking library (recursive, semantic, code-aware splitters); most frameworks also ship splitters (LangChain text splitters, LlamaIndex node parsers).

## Retrieval: dense, sparse, and hybrid

- **Dense retrieval** (embeddings + ANN): great semantic recall, weak on exact terms, acronyms, and rare entities.
- **Sparse retrieval** (BM25): great exact-match precision, no semantics.
- **Hybrid** = both, fused with [reciprocal rank fusion](https://learn.microsoft.com/en-us/azure/search/hybrid-search-ranking) or a learned combiner. In practice hybrid beats either alone on mixed corpora — most production vector DBs (Elasticsearch, OpenSearch, Qdrant, Weaviate) support it natively.

Advanced retrieval models:
- **[ColBERT](https://github.com/stanford-futuredata/ColBERT)** — late-interaction (token-level) scoring; stronger than bi-encoder pooling, heavier at query time.
- **[ColPali](https://github.com/illuin-tech/colpali)** — applies the ColBERT idea to document *page images*: embed rendered pages directly, skip OCR/parsing entirely.
- **[SPLADE](https://github.com/naver/splade)** — learned sparse expansion: neural model, sparse index, best of both worlds.

## Reranking: the cheapest big win

Retrieve 50–100 candidates with a fast first stage, then rerank to the top 5–10 with a cross-encoder:

- **Managed:** [Cohere Rerank](https://cohere.com/), [Voyage Rerank](https://www.voyageai.com/)
- **Open:** [bge-reranker](https://github.com/FlagOpen/FlagEmbedding), [FlashRank](https://github.com/PrithivirajDamodaran/FlashRank) (lightweight, fast)
- **LLM-as-reranker:** [RankGPT](https://arxiv.org/abs/2304.09542)-style permutation prompting — strong but expensive per query

Reranking typically buys more quality per dollar than a bigger embedding model.

## Query-side tricks

- **HyDE** (Hypothetical Document Embeddings): generate a fake answer, embed *it*, and search with that — bridges the query/document wording gap.
- **Query rewriting / expansion:** rewrite the user query (or generate sub-questions) before retrieval; essential for multi-hop questions.
- **RAPTOR**: build a tree of summaries over chunks and retrieve at multiple abstraction levels.
- **Contextual retrieval** (Anthropic): prepend chunk-explaining context before embedding — measurably improves retrieval on fragmented chunks.

## What to measure

Track retrieval in isolation, not just end-to-end answers: **recall@k** and **MRR** on a labeled query–passage set (see [rag-evaluation-guide.md](rag-evaluation-guide.md)). If recall@20 is bad, no generator prompt will save you.
