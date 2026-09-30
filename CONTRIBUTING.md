# Contributing

Thanks for helping keep this the most current directory of retrieval-augmented generation (RAG) resources!

## Adding an entry

1. **Check it fits:** a framework, library, vector database, embedding/chunking tool, retrieval model, reranker, advanced RAG pattern, RAG evaluation tool, benchmark/dataset, or paper/guide whose *primary subject is retrieval-augmented generation*. General-purpose LLM frameworks, chat APIs, and model leaderboards belong in the sibling lists (see Related in the README).
2. **Add to the right section** of `README.md`:
   - Frameworks & libraries → end-to-end RAG frameworks and orchestration libraries
   - Vector databases & retrieval infrastructure → vector DBs, search engines with vector support, ANN libraries
   - Embeddings, chunking & document processing → embedding models/APIs, document parsers, chunking tools
   - Hybrid search, reranking & retrieval models → sparse/dense retrieval models, rerankers, hybrid pipelines
   - Advanced RAG patterns → agentic RAG, GraphRAG, corrective/self-reflective RAG, multi-hop RAG
   - Evaluation, benchmarks & datasets → RAG eval frameworks, retrieval benchmarks, QA datasets
   - Papers & guides → foundational papers, surveys, field guides
3. **One entry = one bullet.** Format:
   `- [Name](https://official-site-or-repo) — ` one-line description + 2–4 key facts inline.
   Tag verification honestly: write `✅ verified 2026-09-30` only when you read the claim on the official page/repo/arXiv yourself; otherwise mark it `⚠️ unverified`.
4. **Add the matching record** to `data/rag.json` with these exact fields:

| field | type | values |
|---|---|---|
| `name` | string | framework, tool, paper, or guide title |
| `vendor` | string | vendor / organization / authors |
| `url` | string | official https:// URL (docs, repo, or arXiv abstract) |
| `description` | string | one sentence |
| `verified` | bool | `true` only if you verified the entry on an official source |
| `source_url` | string | the official source you verified against, or `""` |
| `verified_date` | string | `YYYY-MM-DD` of verification, or `""` |
| `status` | string | `active` / `maintenance` / `archived` / `commercial` / `paper` |
| `category` | string | `framework` / `vector-db` / `embedding` / `chunking` / `retrieval` / `pattern` / `evaluation` / `benchmark` / `paper` / `guide` |
| `features` | string[] | 2–4 key capabilities or facts (only what the official source states) |

5. **Status changes:** if an entry is retired, a repo is archived, or a paper is superseded, update its README entry *and* add a row (newest-first) to `docs/status-changes.md`.

## Style rules

- Link the **official site** (vendor docs, project repo, arXiv abstract page), never a blog post or aggregator.
- Facts that can change (scores, model versions, repo activity) get "as of" context in prose or are omitted — link the source instead of hard-coding numbers that rot.
- Benchmark scores stay labeled by who reported them ("vendor-reported" vs "independent").
- **Scores, specs, and dates are never guessed.** If you can't verify it on an official source, mark it `⚠️ unverified` or leave it out.
- Keep README descriptions to one entry per bullet; put depth in `docs/`.

## CI

Every PR runs:
- **Link check** (lychee) over all markdown files — no dead links.
- **JSON validation** — `data/rag.json` must parse, every record must have the required fields, and `status`/`category` must be from the allowed sets above. Verified entries require an https `source_url` and a `verified_date`.

Run locally before pushing:

```bash
python3 -c "import json; d=json.load(open('data/rag.json')); print(len(d), 'entries')"
```
