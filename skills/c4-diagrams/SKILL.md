---
name: c4-diagrams
description: Use when writing or reviewing a design document, RFC or architecture change — draw the smallest useful C4-style Mermaid view showing system boundaries, deployable units, component ownership, cross-service flows, or deployment topology. A design without a diagram is prose about one.
---

# C4 diagrams

Clarify boundaries and relationships, not file trees. Use plain Mermaid so the
diagram renders in repository Markdown and most docs sites.

## Every design carries at least one view

A design without a diagram is not a design; it is prose about one. Pick the
smallest view that answers a real review question and draw it.

⚠️ **"The structure is obvious" is the claim a diagram exists to test.** Drawing
a container view routinely puts things on one page that the prose never showed
together — a cache nobody mentioned, a lock taken in a different order on two
paths, two handlers the text assumed already existed.

**Scale the view to the change; do not skip it.** A local change that genuinely
moves no boundary still gets the one view that shows *what it sits inside* — a
Component view of three nodes. The escape hatch below records that a specific
LEVEL does not apply; it is not a way out of drawing.

## Workflow

1. Read the relevant requirements and inspect the existing runtime boundaries in the code.
2. State the question the diagram must answer.
3. Choose the smallest view from the table below.
4. Draw concrete actors, systems, containers, components, stores, queues, and labelled relationships.
5. **Mark what is PLANNED versus what EXISTS.** A diagram that draws an unbuilt
   handler like a shipped one leads the next reader to specify building
   something that already exists — or to rely on something that does not.
6. Add assumptions and open questions immediately below the diagram — not in a
   distant section. A reader who stops at the picture must not stop at a false one.

| View | Use for |
|---|---|
| Context | Actors, external systems, and scope are unclear |
| Container | Deployable/runnable units, APIs, stores, and queues change |
| Component | Ownership or collaboration inside one container changes |
| Dynamic | A multi-step request/event workflow is the design risk |
| Deployment | Runtime placement, networks, or rollout topology changes |

Use [references/templates.md](references/templates.md) only when a starting shape
is useful. Prefer one diagram; add another only if it answers a different review
question.

## Rules

- Use `flowchart` or `sequenceDiagram`, not C4-specific Mermaid extensions — they render in more places.
- Do not mix levels: containers deploy; components live inside one container.
- Label relationships with the protocol, event, action, or data exchanged.
- Match names to real domain terms and real deployables.
- Mark planned or uncertain boundaries explicitly (for example a dashed edge or a `(planned)` suffix).

## When not to use

- Do not draw every C4 level by default — one view, and a second only when it
  answers a *different* review question.
- Do not use a diagram as decoration or to duplicate prose that is already clearer.
- **Do not omit the diagram entirely.** For a level that does not apply, write
  `C4 <level>: not applicable — <reason>` beside the view you did draw.
- A copy-only UI change, dependency bump, or data correction with unchanged
  boundaries normally has no design document at all, so this skill never fires.
