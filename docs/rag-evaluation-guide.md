# RAG evaluation guide

Evaluate retrieval and generation *separately*. A fluent, confident, wrong answer means generation failed; a correct answer built on the wrong documents means retrieval failed. Conflating the two makes every fix a guess.

## The two layers

**1. Retrieval eval** — did we fetch the right chunks?
- Metrics: recall@k, precision@k, MRR, nDCG on labeled query → relevant-passage pairs.
- Benchmarks: [BEIR](https://github.com/beir-cellar/beir) (heterogeneous retrieval tasks), [MS MARCO](https://microsoft.github.io/msmarco/) (large-scale passage ranking), [MTEB](https://github.com/embeddings-benchmark/mteb) (embedding quality across tasks).

**2. Generation eval** — did we answer faithfully from the retrieved context?
- **Faithfulness / groundedness:** is every claim in the answer supported by the retrieved passages? (The single most important RAG metric.)
- **Answer relevancy:** does the answer address the question?
- **Context relevancy / precision:** was the retrieved context actually useful, or noise the model had to ignore?

## Frameworks

| Framework | Approach | Notes |
|---|---|---|
| [Ragas](https://github.com/vibrantlabsai/ragas) | Reference-free LLM-judged metrics (faithfulness, relevancy) + test-set synthesis | The standard starting point; generates its own eval data |
| [TruLens](https://github.com/truera/trulens) | Feedback functions over live traces (groundedness, relevance, harm) | Best for continuous eval of a running app |
| [DeepEval](https://github.com/confident-ai/deepeval) | Pytest-style evals with 14+ LLM-judged metrics, CI integration | Best if you want evals as unit tests |
| [Phoenix](https://github.com/Arize-AI/phoenix) | Tracing + LLM evals + experiments | Pairs observability with evaluation |
| [BERGEN](https://github.com/facebookresearch/BERGEN) | Reproducible RAG benchmarking library (Meta) | Standardized retrieval+generation pipelines for research |

## Building a golden set

Minimum viable eval: 50–100 triples of (question, expected answer, relevant passages). Sources:
- Sample real user queries (anonymized) — highest signal.
- [Ragas test-set synthesis](https://docs.ragas.io/) — generate from your own corpus.
- Public QA datasets: [Natural Questions](https://ai.google.com/research/NaturalQuestions), [HotpotQA](https://hotpotqa.github.io/) (multi-hop), [KILT](https://github.com/facebookresearch/KILT) (knowledge-intensive tasks with provenance).

## Failure-mode checklist (the "seven failure points" pattern)

When scores drop, check in this order: missing content (answer isn't in the corpus at all) → missed top ranks (chunking/embedding) → fragmented context (chunks split mid-idea) → unused context (retrieved but ignored) → wrong format → wrong specificity → incomplete answers. Most "model" problems are retrieval problems.

## LLM-as-judge hygiene

- Judge with a *different, stronger* model than the generator when possible.
- Pin judge prompts and model versions; re-baseline when either changes.
- Spot-check judge verdicts against humans on a sample — judges have biases (verbosity, position) just like generators.
