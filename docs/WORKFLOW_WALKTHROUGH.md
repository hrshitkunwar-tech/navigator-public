# Workflow Walkthrough

This document shows one representative Navigator workflow end to end:

**Goal:** Add a teammate to a Jira project.

The purpose is not to claim full public product coverage. The purpose is to make the five-layer system concrete enough that a reviewer can understand how Navigator is meant to work in practice.

---

## Why This Workflow

This is a good representative example because it requires:

- understanding the current UI state
- retrieving role and permissions context
- planning across multiple steps
- verifying that a role assignment actually succeeded

It is more realistic than a one-click demo, but still simple enough to explain in public.

---

## Step 1: Perception

Navigator first reads the current Jira page and converts it into a structured screen state.

What it needs to know:

- current page or modal
- visible buttons and form controls
- semantic labels such as "Project settings" or "People"
- whether the user is already inside a permissions-related section

Expected output:

- a normalized machine-readable representation of the current interface

If WebMCP metadata exists, Navigator should use it first. If not, it falls back to DOM and accessibility-tree signals.

---

## Step 2: Knowledge Retrieval

Navigator then retrieves two kinds of context:

- the most useful prior workflow trace for adding someone to a project
- the relevant documentation section about project roles or required permissions

This is where the system differs from generic RAG:

- workflow traces are ranked by planning utility with CLaRa
- documentation is navigated structurally with PageIndex

The planner should get context that helps it act, not just text that happens to sound related.

---

## Step 3: Reasoning

Using screen state plus retrieved context, Navigator produces a structured plan.

Example high-level plan:

1. Open project settings
2. Navigate to the membership or people screen
3. Search for the teammate
4. Choose the correct project role
5. Confirm the addition
6. Verify the teammate now appears with the intended access level

The important behavior here is explicit planning with confidence and stopping conditions, not a vague freeform response.

---

## Step 4: Execution

Navigator executes the plan step by step.

For each step, it should:

- match the intended target element
- perform the action
- wait for the UI to stabilize
- verify the postcondition before proceeding

Example verification:

- after clicking "Add people", a search input becomes visible
- after selecting a role, the expected role appears in the confirmation state
- after submitting, the teammate appears in the project membership list

If the UI diverges or confidence drops too low, the system should switch to guided mode rather than continuing blindly.

---

## Step 5: Memory

Once the workflow completes, Navigator stores the resulting trace.

That trace is useful because it preserves:

- what page state the system started from
- what context was retrieved
- which decisions were made
- what verification conditions actually worked

Over time, repeated workflows should improve retrieval quality and planning speed.

---

## Why This Matters

This walkthrough captures the product thesis in one example:

- Perception makes the interface legible
- Retrieval provides the right operational context
- Reasoning turns that into a plan
- Execution verifies actions against the live UI
- Memory makes the next run better

The result is not just "help text for software." It is a system designed to use software correctly.
