# Navigator Benchmarks

These benchmark notes explain the retrieval choices behind Navigator. They are not intended as paper-style claims. They are decision benchmarks: enough rigor to decide whether a component deserves to exist in the system.

---

## What The Metrics Mean

- **Top-1 hit rate:** How often the best result is already the right one
- **Top-3 hit rate:** How often the right result appears in the first three candidates
- **Latency (p50):** Median response time
- **Latency (p99):** Worst-case tail latency for the slowest one percent of requests

For Navigator, Top-1 matters more than generic retrieval benchmarks because the planner benefits most when the first result is already highly useful.

---

## CLaRa 7B vs Classic RAG

Question being tested:

Can CLaRa retrieve workflow traces more effectively than a standard RAG pipeline for planning browser actions?

Setup:

- Task: retrieve the most useful workflow trace for a given intent
- Corpus: real product support documentation and workflow-style traces across multiple SaaS tools
- Baseline: BM25 + dense retrieval using cosine similarity, no custom re-ranker
- CLaRa variant: CLaRa 7B-E2E

Results:

| Metric | CLaRa 7B | Classic RAG | Delta |
|---|---|---|---|
| Top-1 hit rate | **0.61** | 0.51 | +0.10 |
| Top-3 hit rate | **0.78** | 0.67 | +0.11 |
| Latency (p50) | **43ms** | 180ms | 4.2x faster |
| Latency (p99) | **89ms** | 420ms | 4.7x faster |

Why this matters:

Classic RAG is designed to retrieve text that sounds similar to the query. Navigator needs traces that are useful for planning the next action. Those are different objectives.

Example:

- Query: "Add a teammate to a Jira project"
- Semantic retrieval may surface a GitHub membership trace because the words look similar
- CLaRa is more likely to surface a Jira trace that actually helped solve the same task

Decision:

Navigator uses CLaRa as the primary workflow-trace retrieval mechanism because it is both more accurate and materially faster for this use case.

---

## PageIndex vs Vector RAG

Question being tested:

Should documentation retrieval preserve hierarchy, or is chunk-based vector RAG good enough?

Setup:

- Dataset: FinanceBench, used here as a proxy for dense and hierarchical SaaS documentation
- Baseline: vector retrieval over 512-token chunks with cosine similarity
- Alternative: PageIndex tree navigation over document structure

Results:

| Method | Accuracy |
|---|---|
| **PageIndex** | **98.7%** |
| Vector RAG | ~50% |
| Optimized vector RAG | ~62% |

Why this matters:

SaaS help centers are usually hierarchical. The meaning of a section often depends on its parent section and surrounding context. Chunking breaks that structure.

PageIndex preserves the tree, so the retriever can reason through the document the way a human reviewer would:

- Advanced settings
- Project permissions
- Required roles

Decision:

Navigator uses PageIndex for documentation retrieval because documentation questions are often structural, not purely semantic.

---

## Caveats

- The workflow-trace benchmark uses a limited internal corpus and should keep expanding over time
- "Planning utility" is a product-oriented notion of relevance, not a standard semantic-search benchmark
- FinanceBench is a good structural proxy for SaaS docs, but not a perfect substitute for every help center shape

These numbers are strong enough to justify the current architecture. They should continue to evolve as the trace corpus grows.
