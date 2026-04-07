# Technical Decisions

Navigator is intentionally opinionated. The system is built around a small set of decisions that reduce operational complexity while improving reliability for browser execution.

This document covers the stack choices that matter most in the public repo today.

---

## TypeScript Everywhere

Decision:

Use TypeScript across the extension surface, backend boundaries, and shared schemas.

Why:

- The important system types should stay consistent end to end
- Browser agents already have enough complexity without language-boundary overhead
- Shared types like screen state, action plans, and decision traces benefit from compiler enforcement

What this buys:

- Less serialization drift across layers
- Better tooling and refactoring
- Faster iteration in a product that already crosses perception, retrieval, and execution

---

## Convex

Decision:

Use Convex as the primary backend runtime.

Why:

- It covers the early-stage backend needs without forcing a multi-service stack
- Real-time updates, storage, functions, and vector search can live in one place
- It reduces operational overhead while the product risk is still in the browser-agent layer, not infra scale

What this buys:

- Fewer moving parts than Postgres + Redis + queue + vector DB
- TypeScript-native backend ergonomics
- A clean place to store traces, planning artifacts, and reactive state

---

## Preact

Decision:

Use Preact for the extension UI instead of React.

Why:

- Navigator's extension UI needs a lightweight interface layer
- Injected browser UI should have minimal payload cost
- The component model needed here is simple and does not require React's full surface area

What this buys:

- Smaller extension footprint
- Familiar component ergonomics
- Less overhead on every page where the interface appears

---

## Shadow DOM

Decision:

Use Shadow DOM for the extension surface.

Why:

- The Navigator panel should not inherit host page CSS
- The host page should not be able to accidentally break the extension UI
- Style isolation matters when an interface is injected into arbitrary SaaS products

What this buys:

- Stronger UI isolation
- Lower risk of CSS collisions
- More predictable rendering across very different target applications

---

## CLaRa Over Classic RAG

Decision:

Use CLaRa for workflow-trace retrieval rather than defaulting to semantic vector search.

Why:

- Workflow planning depends on useful prior traces, not just similar wording
- Planning utility is a better retrieval objective than similarity for action-heavy systems
- The current benchmark result is stronger both on accuracy and latency

What this buys:

- Better first-result quality for planning
- Less retrieval latency
- A system that can improve as more execution traces are collected

---

## PageIndex Over Flat Chunk Retrieval

Decision:

Use PageIndex for structured documentation retrieval.

Why:

- SaaS documentation is hierarchical
- Chunking destroys parent-child structure
- Many help-center answers depend on where a section lives, not just what words it contains

What this buys:

- Better retrieval on structured docs
- More faithful navigation through help centers and guides
- Stronger alignment with the kinds of documentation enterprise tools actually expose

---

## Why These Decisions Fit Together

These choices are connected:

- TypeScript keeps interfaces coherent
- Convex keeps the backend simple
- Preact + Shadow DOM keep the browser surface lightweight and isolated
- CLaRa and PageIndex make retrieval fit the actual problem shapes

Together, they produce a system optimized for reliable software use, not generic chatbot behavior.
