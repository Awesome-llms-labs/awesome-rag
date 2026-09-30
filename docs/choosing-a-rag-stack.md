# Choosing a RAG stack

A RAG system has five decisions that actually matter: the orchestration framework, the vector store, the embedding model, the chunking strategy, and the evaluation loop. Get those right and the rest is tuning.

## 1. Framework: build or buy the pipeline?

- **Start here if you want velocity:** [LlamaIndex](https://github.com/run-llama/llama_index) and [LangChain](https://github.com/langchain-ai/langchain) are the two mature orchestration layers. LlamaIndex is retrieval-first (data connectors, indexes, query engines); LangChain is agent-first (chains, tools, agents) with RAG as one pattern among many.
- **Enterprise document search:** [Haystack](https://github.com/deepset-ai/haystack) (deepset) is the pipeline-oriented choice with strong eval tooling; [RAGFlow](https://github.com/infiniflow/ragflow) is the open-source "RAG engine" with a visual workflow editor and deep-document understanding.
- **Avoid framework lock-in early:** the retrieval core (chunk → embed → store → retrieve → rerank) is ~200 lines of code. Frameworks pay off when you need their connectors, eval harnesses, or agent loops — not for a single PDF chatbot.

## 2. Vector store: managed, self-hosted, or Postgres?

- **You already run Postgres:** [pgvector](https://github.com/pgvector/pgvector) keeps vectors next to your relational data with zero new infrastructure. Good to ~low-millions of vectors.
- **Self-hosted dedicated:** [Qdrant](https://github.com/qdrant/qdrant) and [Weaviate](https://github.com/weaviate/weaviate) are the two most complete open-source vector DBs (filtering, hybrid search, multi-tenancy). [Milvus](https://github.com/milvus-io/milvus) scales furthest (distributed, billion-vector territory).
- **Managed serverless:** [Pinecone](https://www.pinecone.io/) and [Turbopuffer](https://turbopuffer.com/) remove ops entirely; you pay per usage.
- **Embedded / local-first:** [Chroma](https://github.com/chroma-core/chroma), [LanceDB](https://github.com/lancedb/lancedb), and [FAISS](https://github.com/facebookresearch/faiss) run in-process — ideal for prototypes, edge, and eval harnesses.
- **You already run Elasticsearch/OpenSearch:** both have mature vector search; hybrid (BM25 + kNN) is a first-class query, not a bolt-on.

Rule of thumb: pick the store that matches your *existing* infrastructure and your scale (thousands → embedded; millions → pgvector/Qdrant; billions or multi-tenant SaaS → Milvus/managed).

## 3. Embeddings: the quiet quality lever

- Default to a strong open embedding family ([BGE](https://github.com/FlagOpen/FlagEmbedding), [E5](https://github.com/microsoft/unilm)) or a managed API ([Voyage AI](https://www.voyageai.com/), [Cohere Embed](https://cohere.com/), [Jina AI](https://jina.ai/)) matched to your languages and domain.
- Multilingual or long-document workloads change the answer — check the [MTEB leaderboard](https://github.com/embeddings-benchmark/mteb) for your specific task/language rather than trusting a single "best model" claim.
- Fine-tuning embeddings on your own query–passage pairs is the highest-ROI retrieval improvement most teams never do.

## 4. Chunking: where most RAG quality is won or lost

See [chunking-and-retrieval-guide.md](chunking-and-retrieval-guide.md). The short version: start with recursive character splitting (~512 tokens, ~10–20% overlap), measure, then try semantic chunking or document-structure-aware splitting only if retrieval evals say you need it.

## 5. Evaluation: build the harness before you tune

See [rag-evaluation-guide.md](rag-evaluation-guide.md). Minimum viable: a golden set of 50–100 question/answer/context triples, scored for faithfulness and answer relevancy with [Ragas](https://github.com/explodinggradients/ragas) or [DeepEval](https://github.com/confident-ai/deepeval). Without this, every "improvement" is a guess.

## 6. When to go advanced

- **Multi-hop questions** (answers span several documents) → [GraphRAG](https://github.com/microsoft/graphrag)-style knowledge-graph indexing.
- **Retrieval keeps missing** → hybrid search (BM25 + dense) with a reranker ([Cohere Rerank](https://cohere.com/), [bge-reranker](https://github.com/FlagOpen/FlagEmbedding)) before reaching for exotic patterns.
- **The corpus fights back** (contradictions, stale docs) → corrective/self-reflective patterns (CRAG, Self-RAG).
- **The task needs tools, not just lookup** → agentic RAG: let the model decide *when* and *what* to retrieve.

## Anti-patterns

- Stuffing the whole document into a 1M-token context instead of retrieving (cost, latency, and "lost in the middle" degradation).
- Evaluating with vibes instead of a golden set.
- Chunking PDFs with naive text extraction — use a real document parser ([Docling](https://github.com/docling-project/docling), [Unstructured](https://github.com/Unstructured-IO/unstructured), [LlamaParse](https://www.llamaindex.ai/llamaparse)).
