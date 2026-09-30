# Glossary

**RAG (retrieval-augmented generation)** — generating an answer conditioned on documents retrieved at query time, grounding the model in external knowledge instead of relying only on parametric memory.

**Chunking** — splitting documents into retrievable pieces. Chunk size, overlap, and boundary strategy directly affect retrieval quality.

**Embedding** — a dense vector representation of text; similarity in vector space approximates semantic similarity.

**ANN (approximate nearest neighbor)** — index structures (HNSW, IVF, PQ) that trade a little recall for orders-of-magnitude faster vector search. Exact search doesn't scale.

**Dense retrieval** — search over embedding vectors. **Sparse retrieval** — search over term statistics (BM25). **Hybrid retrieval** — combining both.

**Reranking** — a second, more expensive scoring pass (usually a cross-encoder) over the top candidates from a fast first-stage retriever.

**Cross-encoder** — a model that scores a (query, passage) pair jointly; more accurate and slower than a bi-encoder (which embeds query and passage separately).

**Late interaction (ColBERT-style)** — token-level query–document scoring computed at query time; finer-grained than pooled bi-encoder similarity.

**Knowledge graph RAG / GraphRAG** — indexing entities and relationships into a graph and retrieving subgraphs/communities instead of (or in addition to) flat chunks; strong for multi-hop and "global" questions.

**Agentic RAG** — the model decides when, what, and how often to retrieve (possibly multiple rounds with tools) rather than following a fixed retrieve-then-generate pipeline.

**Self-RAG / corrective RAG** — patterns where the model critiques retrieved passages (relevance, support) and re-retrieves or abstains when evidence is insufficient.

**Faithfulness (groundedness)** — the degree to which generated claims are supported by the retrieved context. The core RAG quality metric.

**Hallucination (in RAG)** — generating claims unsupported by (or contradicting) the retrieved passages — distinct from the model inventing facts from nothing.

**Lost in the middle** — the observed degradation of LLM use of context placed in the middle of long inputs; one reason retrieval precision beats stuffing context.

**Golden set** — a labeled set of (question, answer, relevant passages) used to evaluate retrieval and generation reproducibly.

**recall@k / MRR / nDCG** — standard retrieval metrics: fraction of relevant docs in the top-k, mean reciprocal rank of the first relevant doc, and normalized discounted cumulative gain (rank-aware relevance).
