---
name: spec-writing
description: The prose craft shared by every governance document — ADRs, standards, domain specs, RFCs, design docs. Use when writing or revising any of them, or when a document is technically correct but nobody trusts or reads it. Covers how to record a correction, how to state a gap honestly, and the constructions that make a spec survive contact with the next reader.
version: 1.0.0
---

# Spec writing

## Spec work starts with a plan

Before writing or sweeping any spec artifact, read what exists and present a plan
(plan mode, if your agent has one). Do not begin editing and discover the shape as
you go.

**Why it is cheaper, and it is not only cost.** A spec edit fans out: one decision
touches the proposal, the requirements, the design and the task list, and each file
read mid-edit is a file re-read later when the change turns out to reach further.
Planning first reads each once.

⚠️ **The expensive failure.** A mechanism retired in two files can stay normative in
two dozen places across four others — and take three review rounds and a full
re-sweep to find — because the edit began before the blast radius was known. Search
for every reference first; guessing answers the question three reviews later.

**The plan names, before any edit:**

1. every artifact the change touches, and why — the requirements file is never the only one
2. which decisions are already recorded, and where, so none is silently reversed
3. what is genuinely undecided and needs a stakeholder — as questions with recommendations
4. what will NOT be touched, and why

🔶 **A sweep is spec work too.** "Propagate a decision already made" is exactly the
task that looks mechanical and is not: it reverses earlier decisions unless each is
found and recorded.

**Not for a single dated correction or one typo.** Planning is for authoring a
document set, revising one, or sweeping a decision across it.

---

This is **the prose craft** — the habits that decide whether a document is trusted
a year later. The document's *shape* comes from your template.

> 🔶 **The through-line: a spec earns trust by being honest about what it does
> not know.** Every habit below is a way of doing that precisely rather than
> vaguely.

---

## Terminology is load-bearing — a reader takes the word literally

A word that means several things to several people means **all of them at once**
to an agent, and it will pick one without telling you.

🔶 **Worked example.** A domain called *credentials* can mean a password, an API
key, an OAuth token, a professional qualification, or a saved payment method. Name
it that and every consumer builds against a different reading — and they all look
correct in isolation. Or: a one-time-password table stores a `code_hash`, and the
error taxonomy uses `code` for something entirely unrelated. Two `code`s, one word.

**The rules:**

1. **A term used in more than one domain belongs in a glossary**, defined once.
2. **Never introduce a synonym for a term that already exists.** Pick the existing
   word even when the new one reads better; a second word for one concept is a
   second concept as far as any reader is concerned.
3. **Name by what a thing IS to the reader, not by how it is implemented.**
4. **When a word is unavoidably overloaded, qualify it at every use** —
   `error code`, never a bare `code`.

## 1 · ⭐ Dated corrections, inline — never a changelog at the bottom

Put the correction **where the change applies**, not in a log nobody scrolls to.

```markdown
> ⚠️ **Corrected 2026-08-19.** ~~"Neither email nor phone is unique."~~ Wrong —
> derived from the struct tags, which carry only the index. The unique
> constraint added in a later migration is not represented in the model at all.
```

**Strike through, do not delete.** A reader who remembers the old rule finds it
marked rather than wondering whether they imagined it. **Deleting a wrong claim
destroys the most valuable thing in the file** — the record that it was wrong, and why.

Label superseded text explicitly: ~~*do not implement*~~.

⚠️ **When you correct a claim, search for it everywhere.** One wrong claim is
often repeated across several files.

## 2 · ⭐ Name an interim gap as a gap

The failure is describing a half-built thing in the present tense.

> The events are published and nothing reads them, so the history is
> **emitted but not yet queryable — an honest interim gap, not a delivered trail.**

Compare: *"events are published for audit history"* — technically true, and it
will be read as a working audit trail.

## 3 · Distinguish amendment from reversal

> The decision is **amended in its enumeration, not reversed in its substance.**

A reader deciding whether their existing work still holds needs that difference,
and "updated" does not carry it.

## 4 · Scope as "built to extend, not to be complete"

State what is deferred **and name its future home**.

**"Deferred" without a destination is just an omission.** A table of
*deferred → future home* turns an absence into a plan.

## 5 · ⭐ Name a distinction the domain must hold

When two things are easy to collapse, say so outright, then break it down:

> This is a distinction the domain must hold precisely, **because the two halves
> pull in opposite directions and it is easy to collapse them.**

Then: what belongs to each side · how they meet · **how they are discriminated
structurally**.

🔶 Example: **qualification** (*may* this person perform this service) versus
**availability** (*can* they, at this time). Two questions, two owners, endlessly
conflated.

## 6 · ⭐ Structural discrimination over flags

> Trust money versus operating money is discriminated **structurally**, by distinct
> account roles, **rather than by a flag.**

The counter-example: a system-issued release distinguished from a user's own
rejection **only by the string literal `"quote expired"`** in a free-text reason
field. A user typing that phrase becomes indistinguishable from the system.
**A flag or a magic string is a discrimination that can be forged; a distinct
type cannot.**

## 7 · State the blast radius

> **Blast radius:** every query joining assignments to order data.

A finding without one gets triaged by how alarming it sounds. With one, it gets
triaged by what it touches.

## 8 · Open questions carry an owner and a status marker

Inline, not in a separate backlog: `open:` · `deferred:` · `allocated-to: <id>` ·
`CLOSED (date)`.

**Every item names an owner, even if the owner is "nobody".** An unowned item that
*says so* is tracked; an unowned item that stays silent is lost.

> Owned jointly by the finance and reporting teams; **neither has taken it.**

## 9 · Mark what you did not check

`[UNVERIFIED]`, and phrase it as unchecked rather than absent.

**"X does not happen" and "whether X happens was not traced" are different
claims.** Write the second unless you looked. Reasoning from absence is a
reliable source of confidently wrong findings.

## 10 · Decline to fabricate

When the answer genuinely is not known:

> Which mechanism applies is **undecided** (a one-way door);
> **this document deliberately does not fabricate it.**

🔶 **Saying "we do not know" in a governance document is a feature.** It stops the
next reader implementing your guess as though it were a decision.

## 11 · Record what is exemplary, not only what is broken

A defect list is demoralising and, worse, misleading. **If the codebase already
solves a problem somewhere, that is both the argument the gap is fixable and the
template for fixing it.** Name the thing to copy.

> The credit ledger solves the identical at-least-once problem with a unique index
> **plus** a non-blank CHECK. The pattern was available and was not applied here.

## 12 · Give the ratio, not the count

> **One of five rules is structurally enforced.** That ratio, not the rule count,
> is the honest summary of this domain.

## 13 · Scale depth to evidence

A thin area gets a short spec **that says it is thin**:

> ⚠️ **LOW EVIDENCE.** Absence of rules here means **uncharacterised, not clean.**

Uniform depth over uneven evidence produces ceremony, not knowledge.

---

## Before you finish

- [ ] Corrections struck through and dated, in place — not deleted, not in a footer
- [ ] Interim gaps named as gaps
- [ ] Deferred items name a future home
- [ ] Unchecked claims marked `[UNVERIFIED]`, phrased as unchecked
- [ ] Open items name an owner, even if "nobody"
- [ ] Blast radius stated where non-obvious
- [ ] At least one thing recorded that is **done well**
- [ ] Nothing fabricated to fill a gap
