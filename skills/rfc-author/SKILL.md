---
name: rfc-author
description: Write an RFC — the design document that sits between a problem and the change that implements it. Use when a change is large enough to need its shape agreed before code, when asked to "write an RFC" or "design this", or when a recorded decision needs a worked design to land it. Covers the seam between a brief (what and why) and a spec (exactly what will exist).
version: 1.0.0
---

# RFC author

An RFC is the **worked design**: given a problem and a decision, here is the
shape of the thing, the alternatives weighed, and what it costs.

## When not to use

- A bounded change whose design fits coherently in its own change notes keeps it
  there.
- Use a decision record (ADR) when one durable cross-cutting decision is the
  whole subject — `adr-author` if installed.
- Do not write an RFC to restate a proposal, a task list, or an existing spec.

## Where it sits

| Artifact | Answers |
|---|---|
| brief / ticket | **what** and **why** — the problem, in the customer's terms |
| **RFC** | **how**, at design level — the shape, the seams, the migration |
| ADR | the **one cross-cutting decision** an RFC may depend on or produce |
| spec / rule | what will be **true** afterwards |
| plan | the ordered steps to build it |

⚠️ **An RFC that decides one thing should be an ADR.** An ADR that carries a
whole design should be an RFC with an ADR extracted. If you cannot say which
you are writing, you are not ready to write either.

---

## Body

```markdown
## Summary            one paragraph a busy reader can stop after
## Problem            the forces, with evidence — not "it would be nice"
## Goals              what success looks like, testably
## Non-goals          ⛔ do not skip this
## Design             the shape: entities, seams, flows, states
## Migration          how we get from here to there, INCLUDING the half-done state
## Alternatives       what else was considered, with why-not
## Risks & unknowns   what could make this wrong
## Open questions     with owners
```

### The three that get skipped and shouldn't

🔶 **`## Non-goals`.** Most RFC arguments are really scope disputes. Naming what
this deliberately does not do ends them before they start.

🔶 **`## Migration` must describe the half-done state.** Every real change has a
window where both shapes exist. A strangler migration rarely fails on the new
design — it fails because nobody wrote down how long the overlap was meant to
last, so it became permanent. Say what the overlap looks like and **what ends
it**.

🔶 **`## Risks & unknowns`.** An RFC with no risks has not been thought about.
Mark genuine unknowns as unknown rather than guessing — `[UNVERIFIED]` is a
legitimate thing to ship in a design document.

---

## Status and immutability

`draft` → `accepted` → `superseded`.

⛔ **An accepted RFC is not rewritten.** Supersede it, or append a **dated
correction block**. A correction recording *"the runtime does not do what this
says"* is the highest-value content an RFC can carry — it is exactly what a
reader cannot derive from the code.

⚠️ **Prefer a dated correction over a headline status line.** A status can
assert something a correction below it disproves; a reader who trusts the
headline and stops there can build an entire implementation on a design that
was already known to be wrong.

---

## Before you finish

- [ ] Could a reader stop after `## Summary` and still act correctly?
- [ ] `## Non-goals` is populated
- [ ] `## Migration` describes the overlap **and what ends it**
- [ ] Alternatives have real why-nots, not strawmen
- [ ] Any cross-cutting decision inside it extracted to an ADR
- [ ] Unknowns marked, not guessed
