# Public Status

This document makes the public/private split explicit so the repo feels trustworthy rather than incomplete.

---

## What Is Public

Public today:

- System architecture
- Benchmark notes and retrieval rationale
- Core technical decisions
- Representative workflow walkthrough
- Public issue tracker and roadmap

This public surface is meant to show the thesis clearly and give reviewers enough evidence to evaluate the direction.

---

## What Is Private

Private today:

- Production implementation details
- Full extension/runtime code
- Internal evaluation corpus
- Private traces and internal instrumentation
- Internal deployment details

The implementation is private because the current goal of this repo is not open-source distribution. It is public technical legibility.

---

## What Is Implemented vs Exploratory

Implemented or concretely defined:

- Five-layer system shape
- Retrieval strategy: CLaRa for traces, PageIndex for docs, Convex for narrow vector cases
- Public benchmark framing
- Extension-facing UI strategy: Preact + Shadow DOM
- TypeScript-first boundary design

Still early or exploratory:

- Broader public workflow examples
- Expanded retrieval evaluation across more tools and baselines
- Public demo artifacts
- Additional public surfaces beyond docs and issues

---

## Why Keep This Split Explicit

A public repo gets weaker when it implies more than it can prove. This document exists so the repo can be judged on what is actually visible and real today:

- a clear architecture
- a coherent technical thesis
- benchmark-backed retrieval decisions
- visible public next steps

That is enough for the repo to be credible without pretending the entire product is already open.
