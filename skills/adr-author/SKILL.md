---
name: adr-author
description: Write an architecture decision record (ADR), or decide that something is NOT one. Use when a cross-cutting technical decision is being made or discovered, when asked to "write an ADR" or "record this decision", or when a change touches an entry in the project's list of candidate decisions. Also covers the classification test (ADR vs standard vs domain rule vs skill), which is the part most often got wrong.
version: 1.0.0
---

# ADR author

An ADR records **a cross-cutting decision and the reasoning behind it**, so the
next person can tell a deliberate choice from an accident.

## When not to use

- Keep a local, reversible implementation choice in the change's own design notes.
- Put a business invariant in the project's domain rules.
- Put an enforced technical rule in a standard and link its enforcer (use
  `standard-author` if installed).
- Put agent operating guidance in a skill.
- Record what the system currently *is* in the project's architecture document
  (use `architecture-author` if installed).
- Do not create an ADR merely because the project's change process has a
  decision-record step.

---

## ⛔ An ADR exists only if the decision is CROSS-CUTTING

**The test: does this decision constrain more than one domain or component?**

- **Yes** → ADR. It is a system-wide constraint and every part must honour it.
- **No** → it belongs in that domain's or component's own spec or design. Not an ADR.

🔶 **Worked example.** Choosing a particular identity-provider vendor looks like a
major architectural decision, and it is, but **only the identity component needs
to know it**. Every other part cares that an identity component exists and what
its contract is, never which vendor sits behind it. So the vendor choice is
recorded in the identity component's design, **not** in an ADR.

⚠️ **Why this matters more than it looks.** A corpus of ADRs is read by agents as
a set of constraints to satisfy. Local decisions mixed in make that set larger
and less applicable, and an agent given twenty constraints of which twelve do not
apply to its work starts ignoring constraints *as a class*. Precision about what
an ADR is for is what keeps the ones that remain load-bearing.

## ⛔ An ADR is accepted by a NAMED human, not by consensus

`status: accepted` requires a person. Record who in `owner:`, and do not move an
ADR out of `proposed` without them. An architectural decision nobody is
accountable for is a suggestion with better formatting.

## ⛔ FIRST: is it actually an ADR?

Four artifacts, four different homes. Getting this wrong is the most common
failure: most things people call ADRs are standards or domain rules.

| It is a… | When | Lives in |
|---|---|---|
| **ADR** | a **cross-cutting architecture** decision, with alternatives that were genuinely available | the project's decision-record folder |
| **Standard** | an enforceable **rule** with a real enforcer (a linter, a hook, a CI check) | the project's standards folder |
| **Domain rule** | a business invariant that **survives a rewrite**, true regardless of stack | the project's domain rules |
| **Skill** | guidance with **no enforcer**: how to do something well | a skill |

**The discriminating questions:**

- *Could it have gone another way, and did that matter?* → ADR. If there was
  only ever one option, it is documentation, not a decision.
- *Does something mechanically enforce it?* → **standard**, not ADR. (The ADR
  may still exist *behind* the standard, recording why.)
- *Would it still be true if we rewrote the system in another language?* →
  **domain rule**.
- *Is it advice?* → **skill**.

⚠️ **An ADR and a standard often pair up**: the ADR is the *decision*, the
standard is the *enforced rule* that follows from it. Write both, and link them.

**If it is not an ADR, say so and file it in the right place.** Recording that
reclassification, with the reason, is as valuable as the ADR would have been;
the project's list of candidate decisions should have a section for it.

---

## Title grammar: **verb + object**

> `Adopt an event bus for inter-service messaging`
> `Persist enumerations as application-owned codes`
> `Store money as integer minor units`

⚠️ **A title that is not a decision is not an ADR.** "Messaging" is a topic.
"Adopt X for messaging" is a decision.

Filename: `NNNN-<title-in-kebab-case>.md`, zero-padded to 4, unless the project
already uses another scheme.

---

## Body

```markdown
## Context            the forces; why a decision is needed AT ALL
## Decision           what was chosen, in ACTIVE VOICE
## Scope              OPTIONAL — carve-outs and exceptions, with reasoning
## Considered options each with Pros / Cons; the chosen one marked
## Consequences       positive / negative / NEUTRAL
```

Plus frontmatter, and, if the project keeps one, an editable metadata table
(the table is what humans actually edit). The two must agree:

```markdown
| | |
|---|---|
| **Status** | `proposed` |
| **Version** | `0.1.0` |
| **Date** | YYYY-MM-DD |
| **Owner** | @role |
| **File** | `NNNN-....md` |
| **Relies on** | other ADRs or design documents this builds on |
| **Supersedes** / **Superseded by** | — |
```

### The three that carry the weight

🔶 **`## Considered options` must include options you did not take, with real
cons for the one you did.** An ADR listing one option is a press release. If the
chosen option has no downsides, you have not finished thinking.

🔶 **`## Consequences` must contain negatives.** Name what gets worse. A decision
with only positives was not a decision.

🔶 **`## Scope`** is where you say what this deliberately does *not* govern.
Carve-outs stop the ADR being cited for things it never decided.

---

## Status is enforcement, not intent

`proposed` → `accepted` → `superseded`. **A `proposed` ADR binds nothing.**
Do not cite one as though it constrains a change.

### ⛔ Accepted ADRs are immutable

Do **not** rewrite an accepted ADR. Either:

- **supersede** it with a new ADR, linking both ways; or
- **annotate** it with a **dated correction block** appended at the end.

```markdown
## ⚠️ Correction, YYYY-MM-DD — <what is wrong with this ADR>

**This ADR is Accepted and remains the decision. The code does not implement it.**

<evidence, with file:line>

**Neither side has been chosen.** The honest options are: <enforce> or <amend>.
```

🔶 **A correction that records "the runtime violates this" is more valuable than
the original ADR**, because it is the thing a reader cannot derive from the code.
Prefer a dated correction block over a headline status line: a status can
assert something a correction below it disproves.

---

## Discovered decisions go in the list of candidate decisions

Park a decision **the moment you notice it**, even in passing, in the project's
list of candidate decisions (create one in the decision-record folder if none
exists). Adding a row costs nothing; rediscovering the decision in two years
costs a lot.

- ⚠️ marks a **live inconsistency**: two answers to one question, coexisting.
- Remove a row only when the ADR is written, **or** when it is reclassified with
  a reason.
- Cross-cutting patterns (one question answered differently across several
  domains) are the highest-value candidates. They are invisible from inside any
  single domain.

---

## Before you finish

- [ ] Title is **verb + object**
- [ ] It is genuinely an ADR (ran the four questions above)
- [ ] `## Considered options` has ≥ 2, with real cons on the chosen one
- [ ] `## Consequences` names at least one negative
- [ ] Frontmatter **and** metadata table agree
- [ ] Registered in whatever index or lint the project runs, and that check passes
- [ ] Any candidate-list row it closes is marked promoted, **not deleted**
- [ ] If it supersedes something, both files link each other
