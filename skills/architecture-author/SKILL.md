---
name: architecture-author
description: Write or update the durable architecture record — what the system IS, the state machines and flows that run it, what is LEGACY, and the plan to remove the legacy. Use when documenting a subsystem's real shape, when asked to "write the architecture docs", "draw the state machine" or "map the flows", and whenever a change reveals that the documented shape is wrong. Exists because proposals keep specifying things that already shipped.
version: 1.0.0
---

# Architecture author

Record what the system **is**, not what it was meant to be.

> ⛔ **This exists because of a measured failure.** In a single session, three separate
> proposals specified building something that already shipped: an authorisation model,
> a completion action, and a concurrency guard. Each cost a full review round to catch.
> The architecture was knowable every time; it just was not written down.

## What this is for

A reader, human or agent, should be able to answer without reading the code:

1. **What exists**, and which component owns it.
2. **What state a thing can be in**, what moves it, and who is allowed to move it.
3. **Which of two similar-looking paths is the live one**, and which is legacy.
4. **What we intend to delete**, and what has to happen first.

If a doc cannot answer 3 and 4, it is a diagram, not an architecture record.

## ⛔ Verify, then write. Absence is the hard part.

Every claim is either **verified** or marked `[UNVERIFIED]`. There is no third category
and no silent inference.

- **Follow a call to its writer before saying nothing writes it.** A session concluded
  "the completion action must be built" after finding an endpoint that set a status
  field; it stopped one call short of the function that sets the field that matters.
  The action had shipped.
- **Measure the column the writer targets, not the one the name suggests.** Two tables
  can carry a field with the same name; one is empty and the other is populated. Measuring
  the wrong one reports "nothing is ever written" about live data.
- **A counted claim carries the query that produced it**, inline, so the next reader can
  re-run it rather than trust it.
- **Two tables can be one row.** Check whether ids are shared before asserting that two
  concepts are different things.

## Where it lives

| Artifact | Where |
|---|---|
| The map: components, boundaries, what is legacy | the project's architecture document |
| One flow or state machine | one document per flow or state machine, alongside it |

Register both in whatever index or lint the project runs, and bind each process document
to what it touches (domain, the ADRs it relies on, the goals it serves) using the same
references the project's other records use, so a reader can find it from the thing it
describes and the lint can check the references resolve. ⚠️ An architecture record outside
the index is a document nobody can find.

## A process document

Sections, in this order. Keep each one short; length is not thoroughness.

**Purpose**: one paragraph. What this flow is for, in the vocabulary of the people who
use it, not the packages that implement it.

**States**: the states a thing can be in, named exactly as the code names them. ⛔ If the
code has three representations of one state, say so and say which is authoritative; do not
quietly pick one.

**Transitions**: a Mermaid `stateDiagram-v2` or `sequenceDiagram`. Label every edge with
**what triggers it** and **who may trigger it**. An unlabelled edge is a guess.

**What is enforced, and what is merely convention**: the highest-value section, and the
one most often missing. A database constraint, a row lock, and a code comment asking
nicely are three different strengths of guarantee. Say which each one is.

**Legacy**: the older path that still exists, why it is still there, and how to tell at a
glance which one you are looking at.

**Deprecation**: what must be true before the legacy path can be deleted, and what
currently blocks it. ⚠️ "Someday" is not a plan. If nothing blocks it, say that: an
unblocked deletion nobody has done is a different problem from a blocked one.

**Open**: what is not known. `[UNVERIFIED]` claims belong here, named.

## Diagrams

Plain Mermaid: `stateDiagram-v2`, `flowchart`, `sequenceDiagram`. No C4-specific
extensions; they must render in repository Markdown and in whatever docs site the project
publishes. For context and container views, use `c4-diagrams` if installed.

⛔ **Draw what is, including the ugly parts.** A diagram showing the intended architecture
while the code does something else is worse than none, because it is trusted. Where a path
is legacy, mark it on the diagram; do not leave it out.

⚠️ Put assumptions and open questions **immediately below** the diagram, not in a distant
section. A reader who stops at the picture must not stop at a false one.

## Growing it

This is a record that improves by increments, not a document to be finished.

- **Start where you have verified knowledge**, usually the subsystem you just worked in.
- **State the coverage honestly** in the architecture document: which subsystems are
  mapped and which are not. ⛔ Absence of a page means *unmapped*, never *simple*.
- **When a change proves a page wrong, fix it in the same session.** A stale architecture
  record is the failure this skill exists to prevent, wearing a different hat.
- Prefer one honest page over five speculative ones.

## When not to use

- Do not use for a decision: use `adr-author` if installed. Architecture records what is;
  an ADR records what was chosen and why.
- Do not use for a rule that must bind: that is a standard (`standard-author` if
  installed) or the project's domain rules.
- Do not use for a design not yet built: use `rfc-author` if installed, or the project's
  change process.
- Do not restate the project's domain rules. Link them.
- Do not draw a diagram whose only content is a file tree.
