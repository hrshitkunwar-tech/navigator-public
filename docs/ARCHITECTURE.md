# Navigator Architecture

Navigator is organized into five independent layers. Each layer has one responsibility, one primary interface, and one clear fallback path. That separation matters because browser agents fail in different ways than chat products do: they can misunderstand the interface, retrieve the wrong context, plan the wrong action, or execute the right action against the wrong element.

This architecture is designed so those failures are attributable instead of ambiguous.

---

## System Overview

```text
[User or agent intent]
        |
        v
Layer 1: Perception
  Read the live interface
        |
        v
Layer 2: Knowledge
  Retrieve traces + docs
        |
        v
Layer 3: Reasoning
  Produce an action plan
        |
        v
Layer 4: Execution
  Act and verify
        |
        v
Layer 5: Memory
  Store traces and improve future retrieval
```

At the center of the system is a simple loop:

1. Observe the UI.
2. Retrieve the most useful context.
3. Plan the next action.
4. Execute with verification.
5. Save the resulting trace for future planning.

---

## Layer 1: Perception

The perception layer turns the live browser state into a structured representation of what is currently on screen.

Primary responsibilities:

- Read visible UI elements
- Capture semantics such as text, role, label, and state
- Normalize the page into a machine-usable screen representation

Primary inputs:

- DOM structure
- Accessibility tree
- WebMCP metadata when available

Primary output:

- A structured screen state the planner can reason over

Fallback behavior:

- Prefer WebMCP metadata when the page exposes it
- Fall back to DOM and accessibility tree serialization when it does not

Why it exists:

LLMs are bad at reliable execution if the interface is represented only as screenshots or vague text summaries. The planner needs a stable, structured description of the actual page state before it can act safely.

---

## Layer 2: Knowledge

The knowledge layer retrieves two kinds of context:

- Past workflow traces
- Relevant documentation

These are separate retrieval problems and are treated differently.

Workflow traces:

- Retrieved with CLaRa 7B
- Ranked by planning utility instead of semantic similarity

Documentation:

- Retrieved with PageIndex
- Navigated as a hierarchy instead of chunked into flat passages

Fallback behavior:

- Convex vector search is used for narrow cold-start cases before enough trace history exists

Why it exists:

Standard RAG is optimized for "find text that sounds like the query." Navigator needs "find the trace that helped solve a similar task" and "navigate the right doc section without losing hierarchy." Those are not the same problem.

---

## Layer 3: Reasoning

The reasoning layer takes:

- The current screen state
- Retrieved traces
- Retrieved documentation
- The user or agent intent

It produces:

- A structured action plan
- Confidence per step
- Recovery paths
- Clear stopping conditions

Fallback behavior:

- If confidence is too low, Navigator should stop auto-execution and switch to guided mode

Why it exists:

Planning and execution need to be separate. If the same opaque model both chooses and performs actions, failures are hard to debug. A structured plan makes the system inspectable and safer.

---

## Layer 4: Execution

The execution layer follows the plan step by step and verifies whether each step actually succeeded.

Primary responsibilities:

- Match the intended element
- Perform the action
- Wait for the UI to stabilize
- Verify the success condition before continuing

Element targeting priority:

1. Exact text
2. ARIA label
3. Role
4. WebMCP metadata
5. Position as a last resort

Fallback behavior:

- Prefer machine-readable metadata and semantic attributes
- Fall back to lower-confidence DOM targeting only when needed

Why it exists:

Browser agents break when they rely too heavily on brittle selectors or when they assume a click worked without checking the outcome. Navigator treats execution as a verified loop, not a blind action stream.

---

## Layer 5: Memory

Every execution creates a decision trace. Those traces are stored and later compressed into reusable planning artifacts.

Primary responsibilities:

- Store successful and failed execution traces
- Preserve enough structure to understand what worked
- Improve future retrieval quality over time

Fallback behavior:

- New tools rely more on generic retrieval and docs
- Mature tools rely more on accumulated workflow traces

Why it exists:

The product should compound. Navigator should get better at repeated workflows because it has seen similar interfaces, similar decisions, and similar failure modes before.

---

## End-to-End Data Flow

Example: "Add a teammate to a Jira project."

1. Perception reads the Jira page and outputs a structured screen state.
2. Knowledge retrieves the most useful prior Jira membership trace and the doc section about project permissions.
3. Reasoning turns those inputs into a stepwise plan with confidence scores.
4. Execution opens the right settings panel, applies the role assignment, and verifies the outcome.
5. Memory stores the full trace so the next similar request is easier to plan.

That loop is the product.

---

## Design Principles

- Keep layers narrow and replaceable
- Prefer semantic interface understanding over brittle selectors
- Prefer planning utility over similarity
- Prefer verified execution over optimistic execution
- Prefer compounding traces over stateless one-shot automation

The result is not "chat with software." It is a system that can understand software well enough to use it correctly.
