# Navigator

AI execution layer for SaaS interfaces — so anyone, human or agent, can operate software without already knowing how the tool works.

**Status:** Early Access — building in public.  
**Implementation:** Private for now. Architecture, benchmarks, and technical decisions are public.  
**Last updated:** April 6, 2026

| Proof Surface | Current State |
|---|---|
| Public docs | **6 docs** in [`docs/`](./docs/) |
| Retrieval benchmark | **CLaRa 0.61** vs classic RAG **0.51** |
| License | **MIT** |
| Public work tracker | **4 open issues** |
| Repo purpose | Public thesis + proof layer |

---

## Updates

- **April 6, 2026:** Published public architecture, benchmark, and technical decision docs.
- **April 6, 2026:** Added a public workflow walkthrough, FAQ, and public/private status docs.
- **April 6, 2026:** Enabled GitHub issues and opened concrete next-step work in public.
- **April 6, 2026:** Rebuilt the README around proof, navigation, and first-time reviewer trust.

---

## Contents

- [What Navigator Is](#what-navigator-is)
- [Why This Repo Exists](#why-this-repo-exists)
- [Start Here](#start-here)
- [What Is Public In This Repo](#what-is-public-in-this-repo)
- [Architecture At A Glance](#architecture-at-a-glance)
- [Benchmarks At A Glance](#benchmarks-at-a-glance)
- [Tech Stack](#tech-stack)
- [Public Proof](#public-proof)
- [Open Work](#open-work)
- [Roadmap](#roadmap)
- [Repository Structure](#repository-structure)
- [Related Repos](#related-repos)

---

## What Navigator Is

Navigator is a five-layer system that reads a live SaaS interface, retrieves the right procedural context, plans multi-step actions, and executes them with verification. It is not a copilot that merely explains what to click. It is execution infrastructure designed to either guide a user step by step or act autonomously on their behalf.

The core bet is simple: AI should not stop at suggestions when the interface itself is observable and actionable.

---

## Why This Repo Exists

This repository exists to make the technical thesis inspectable in public without pretending the entire implementation is open. It is the proof layer for Navigator:

- system shape
- retrieval choices
- benchmark rationale
- workflow examples
- public progress surface

The goal is that a reviewer can understand what Navigator is, why the architecture is unusual, and why the direction is credible within one or two clicks.

---

## Start Here

If this is your first time in the repo, read in this order:

1. [Workflow walkthrough](./docs/WORKFLOW_WALKTHROUGH.md)
2. [Benchmarks](./docs/BENCHMARKS.md)
3. [Architecture](./docs/ARCHITECTURE.md)
4. [Technical decisions](./docs/TECHNICAL_DECISIONS.md)
5. [Public status](./docs/PUBLIC_STATUS.md)

---

## What Is Public In This Repo

Public in this repo:

- Architecture
- Benchmark notes
- Technical decisions
- A representative end-to-end workflow walkthrough
- FAQ and public/private status
- Active public issue tracker

Not public in this repo:

- Full production implementation
- Internal evaluation corpus
- Private execution traces
- Internal extension/runtime code

That split is intentional. The repo is meant to show the thesis clearly without overclaiming that every production surface is already public.

---

## Architecture At A Glance

Navigator is organized into five layers:

1. **Perception** reads the live interface.
2. **Knowledge** retrieves traces and documentation.
3. **Reasoning** turns intent into a structured plan.
4. **Execution** acts and verifies outcomes.
5. **Memory** stores traces so future planning improves over time.

```text
[Intent]
   |
   v
Perception -> Knowledge -> Reasoning -> Execution -> Memory
```

The important design constraint is separation: planning and execution should not be one opaque step.

Full detail: [Architecture](./docs/ARCHITECTURE.md)

---

## Benchmarks At A Glance

These are architectural decision benchmarks, not vendor-style marketing claims.

| Retrieval task | Better approach | Result |
|---|---|---|
| Workflow trace retrieval | **CLaRa 7B** | **0.61 Top-1** vs 0.51 for classic RAG |
| Documentation retrieval | **PageIndex** | **98.7%** vs ~50% for vector RAG |

Why this matters:

- Workflow traces should be retrieved by planning utility, not similarity alone.
- Structured documentation should be navigated as a hierarchy, not flattened into chunks.

Full detail: [Benchmarks](./docs/BENCHMARKS.md)

---

## Tech Stack

| Layer | Technology | Why it is here |
|---|---|---|
| Language | TypeScript | Shared types across interface, planner, and backend boundaries |
| Backend | Convex | Simpler real-time backend surface for an early-stage agent system |
| Extension UI | Preact + Shadow DOM | Lightweight injected UI with strong style isolation |
| Trace retrieval | CLaRa 7B | Planning utility over similarity |
| Docs retrieval | PageIndex | Hierarchical retrieval for structured help centers |
| Reasoning | Claude Sonnet | Structured planning and tool-oriented output |

Full detail: [Technical decisions](./docs/TECHNICAL_DECISIONS.md)

---

## Public Proof

Core docs:

- [Architecture](./docs/ARCHITECTURE.md)
- [Benchmarks](./docs/BENCHMARKS.md)
- [Technical decisions](./docs/TECHNICAL_DECISIONS.md)
- [Workflow walkthrough](./docs/WORKFLOW_WALKTHROUGH.md)
- [Public status](./docs/PUBLIC_STATUS.md)
- [FAQ](./docs/FAQ.md)

External proof surfaces:

- [Issue #1: public workflow walkthrough](https://github.com/hrshitkunwar-tech/navigator-public/issues/1)
- [Issue #2: expand retrieval evaluation](https://github.com/hrshitkunwar-tech/navigator-public/issues/2)
- [Issue #3: lightweight public demo artifact](https://github.com/hrshitkunwar-tech/navigator-public/issues/3)
- [Issue #4: more public workflow examples](https://github.com/hrshitkunwar-tech/navigator-public/issues/4)

---

## Open Work

Current public next steps:

- [#1 Add a public end-to-end workflow walkthrough](https://github.com/hrshitkunwar-tech/navigator-public/issues/1)
- [#2 Expand retrieval evaluation beyond the initial benchmark](https://github.com/hrshitkunwar-tech/navigator-public/issues/2)
- [#3 Add a lightweight public demo artifact](https://github.com/hrshitkunwar-tech/navigator-public/issues/3)
- [#4 Publish more public examples of supported workflow shapes](https://github.com/hrshitkunwar-tech/navigator-public/issues/4)

This repo should read like an active public thesis, not a one-shot docs drop.

---

## Roadmap

### Now

- Make the public thesis legible in one or two clicks
- Document the retrieval and execution architecture clearly
- Expose concrete next-step work in public

### Next

- Add a deeper public workflow walkthrough with more screenshots or trace structure
- Publish broader retrieval evaluation notes as the corpus grows
- Add a lightweight demo artifact that shows the perception-to-execution loop

### Later

- Publish more examples of supported software workflow shapes
- Open additional system surfaces as the public/private split stabilizes
- Expand the proof layer beyond docs into richer public artifacts

---

## Repository Structure

```text
navigator-public/
├── README.md
├── LICENSE
└── docs/
    ├── ARCHITECTURE.md
    ├── BENCHMARKS.md
    ├── TECHNICAL_DECISIONS.md
    ├── WORKFLOW_WALKTHROUGH.md
    ├── PUBLIC_STATUS.md
    └── FAQ.md
```

---

## Related Repos

- [VisionGuide](https://github.com/hrshitkunwar-tech/VisionGuide) — perception-layer experiment
- [job](https://github.com/hrshitkunwar-tech/job) — applied execution experiment
