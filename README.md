# Navigator

AI execution layer for SaaS interfaces — so anyone, human or agent, can operate any software tool without needing to know it.

**Status:** Early Access — building in public. Architecture and benchmarks are open; implementation is private.

---

## What Navigator Is

Navigator is a five-layer system that reads any live SaaS interface, retrieves the right procedural context, plans multi-step actions, and executes them with verification. It is not a co-pilot that explains what to click — it is execution infrastructure that either guides users step-by-step or acts autonomously on their behalf. The core bet: AI should not stop at suggestions when the interface itself is observable and actionable. Built for the people who operate software the most — customer success managers, account managers, solutions engineers — and for the AI agents that will eventually do that work entirely.

---

## Architecture

```
                    ┌──────────────────────────────────────┐
                    │             Navigator                  │
                    │    AI Execution Layer for SaaS        │
                    └──────────────┬───────────────────────┘
                                   │
         ┌─────────────────────────▼──────────────────────────┐
         │  Layer 1 — Perception (ScreenSense)                  │
         │  WebMCP first, DOM + accessibility tree fallback     │
         │  Output: structured ScreenState JSON                 │
         └─────────────────────────┬──────────────────────────┘
                                   │
         ┌─────────────────────────▼──────────────────────────┐
         │  Layer 2 — Knowledge                                 │
         │  Convex vector search (~20ms)                        │
         │  CLaRa 7B re-rank by planning utility (~200ms)      │
         │  PageIndex doc tree navigation (~150ms)              │
         └─────────────────────────┬──────────────────────────┘
                                   │
         ┌─────────────────────────▼──────────────────────────┐
         │  Layer 3 — Reasoning (Claude Sonnet)                 │
         │  screen state + traces + docs → action plan          │
         │  confidence < 0.80 → guided mode (no auto-execute)  │
         └─────────────────────────┬──────────────────────────┘
                                   │
         ┌─────────────────────────▼──────────────────────────┐
         │  Layer 4 — Execution (client-side)                   │
         │  act → observe → verify loop                         │
         │  WebMCP tool call or DOM click fallback              │
         └─────────────────────────┬──────────────────────────┘
                                   │
         ┌─────────────────────────▼──────────────────────────┐
         │  Layer 5 — Memory (Convex)                           │
         │  CLaRa compression runs async after each execution  │
         │  Same query: 5.2s (week 1) → 2.1s (month 3)        │
         └────────────────────────────────────────────────────┘
```

---

## Benchmarks

These numbers drove the architectural decisions — not vendor marketing claims.

| Retrieval method | Task | Accuracy (Top-1) | Latency (p50) |
|---|---|---|---|
| CLaRa 7B | Workflow trace retrieval | **61%** | **43ms** |
| Classic RAG (BM25 + dense, no re-rank) | Workflow trace retrieval | 51% | 180ms |
| PageIndex | Documentation retrieval (FinanceBench) | **98.7%** | — |
| Vector RAG (512-token chunks, cosine) | Documentation retrieval (FinanceBench) | ~50% | — |

CLaRa is 4.2x faster than classic RAG at p50, with 10pp better accuracy. The gap exists because CLaRa was trained to optimize for planning utility, not semantic similarity — it finds the trace that helped plan a similar task, not the one that sounds most like the query.

PageIndex beats vector RAG by ~49pp on structured documentation because SaaS help centers are hierarchical — chunking destroys the structural context that answers the question.

---

## Tech Stack

| Layer | Technology | Decision |
|---|---|---|
| Language | TypeScript (everywhere) | No polyglot tax; Zod at all boundaries |
| Extension UI | Preact + Shadow DOM | 3KB vs React 40KB; full style isolation |
| Backend | Convex | Replaces Postgres + Redis + queue in one runtime |
| Trace retrieval | CLaRa 7B | Planning utility over semantic similarity |
| Doc retrieval | PageIndex | Hierarchical structure awareness |
| Reasoning | Claude Sonnet | Tool-use structured output; confidence scoring |
| Interface layer | WebMCP | W3C standard; 89% token efficiency vs screenshots |
| Build | esbuild / tsup | Fast, minimal config |
| Testing | Vitest + Zod | Type-safe assertions; schema validation |

---

## System Layers

| # | Layer | What it does |
|---|---|---|
| 1 | Perception | Checks `navigator.modelContext` (WebMCP) first; falls back to DOM serialization + accessibility tree. MutationObserver tracks changes. Nothing leaves the browser until the user queries. |
| 2 | Knowledge | Convex vector search finds candidates; CLaRa re-ranks by planning utility; PageIndex navigates the doc tree. Total: ~380ms cold. |
| 3 | Reasoning | Single Claude Sonnet call. Returns structured action plan with confidence scores per step. Below 0.80 triggers guided mode. ~1.2s, ~$0.003/plan. |
| 4 | Execution | Multi-signal element matching: text > aria-label > role > position. WebMCP tool call or DOM click. MutationObserver for DOM stabilization between steps. |
| 5 | Memory | Full decision trace stored after every execution. CLaRa compression is an async background job. Compounding effect: traces improve retrieval quality over time. |

---

## Why Vector-Light

Navigator does not default to `embed → vector DB → cosine similarity → rerank` for every retrieval problem. That pipeline is optimized for semantic search on unstructured text. Navigator's retrieval problems are structural:

- Workflow traces need to be ranked by planning utility, not semantic similarity
- Documentation needs to be navigated as a tree, not chunked into flat passages

CLaRa and PageIndex are purpose-built for these shapes. Convex handles the narrow cold-start cases where vector search is still useful.

---

## Links

- Full architecture docs: [github.com/hrshitkunwar-tech/navigator](https://github.com/hrshitkunwar-tech/navigator)
- Perception layer experiment: [VisionGuide](https://github.com/hrshitkunwar-tech/VisionGuide) — screenshot → Gemini → real-time UI guidance overlay
- Applied execution experiment: [job](https://github.com/hrshitkunwar-tech/job) — CareerAgent: score → tailor → apply, local-first
