# FAQ

## Why is the implementation private?

Because this repo is currently the public thesis and proof layer for Navigator, not an open-source product release. The goal is to make the technical direction inspectable without overstating what is already being published.

## What is actually real today?

What is real in public today is the architecture, the benchmark rationale, the stack decisions, the representative workflow walkthrough, and the open work tracker. The repo is designed to show why the system is credible, not to claim that every production surface is already open.

## Why use CLaRa instead of standard RAG?

Because Navigator needs workflow traces that are useful for planning, not just text that looks similar to a query. CLaRa is a better fit for retrieving prior actions by planning utility.

## Why use PageIndex for documentation?

Because SaaS documentation is usually hierarchical. Important meaning often lives in section structure, not just local text. Chunk-based retrieval loses that context.

## Why Convex?

Because Navigator already has enough complexity in the browser-agent layer. Convex keeps the early-stage backend surface smaller while still supporting storage, functions, reactivity, and narrow vector-search use cases.

## Why Preact and Shadow DOM?

Because the interface is intended to live inside other software products. Preact keeps the injected UI light, and Shadow DOM keeps it isolated from the host application's styles.

## Is this a copilot?

No. The product thesis is broader than a copilot. Navigator is designed as execution infrastructure: perceive the interface, retrieve the right context, plan the next action, execute it, and verify the outcome.

## What is still early?

Public workflow examples are still limited, retrieval evaluation should expand, and public proof artifacts beyond docs are still being built out. That is why the repo explicitly shows roadmap items and live open work.
