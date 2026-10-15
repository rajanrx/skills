---
name: characterise
description: Establish what is ACTUALLY TRUE of already-built code, as a durable spec, before changing it. Use when asked to "characterise <subsystem>", "document what this actually does" or "write the spec for existing code", or before any change to a subsystem whose behaviour nobody has written down. Encodes verification rules — read every schema migration, never infer absence, cite every claim — that stop confident, wrong claims about existing systems.
version: 1.0.0
---

# Characterise

Turn built code into spec you can trust. Not design — **archaeology**.

The output is a claim someone will act on. A wrong claim here is worse than no
claim, because it looks like knowledge.

---

## ⛔ THE ONE RULE THAT MATTERS

**READ THE MIGRATIONS, NOT JUST THE MODELS.**

A struct tag, a decorator, an ORM annotation — these describe what the
application *believes*. The migration describes what the database *enforces*.
When they disagree, the database wins, and **they disagree more often than
you expect**.

Measured on one sweep of sixteen subsystems: **five** confident, wrong,
load-bearing claims. All five came from one habit — reading a model, or a
single `CREATE TABLE`, and inferring the rest.

| Claimed | Actually | Missed because |
|---|---|---|
| "provider phone is `NOT NULL`" | a later migration **dropped** it | never read past the first migration |
| "customer email is not unique" | a later migration **added** a partial unique index | never read past the first migration |
| "`idx_..._user` is a unique index" | plain `CREATE INDEX`; the unique tag was **only on the struct** | trusted the ORM tag |
| "no webhook idempotency exists" | a whole dedup table exists **one layer up** | inferred absence from *this* table |
| "promotion codes are not unique" | partial unique index **+** a non-empty CHECK added after a production incident | read the `CREATE TABLE` only |

### The corollaries

⛔ **Never infer absence from one `CREATE TABLE`.** "Nothing enforces X" is a
claim about the *whole schema and codebase*, not about the table you happened
to open. Before writing it, search for the mechanism by name.

⛔ **Never infer absence from the absence of one mechanism.** "No events are
published" does **not** mean "no durable record exists" — audit rows are a
different mechanism. Check each independently.

⛔ **An ORM tag is not a constraint.** Many ORMs cannot convert an existing
same-named index, and auto-migration usually does not run in production. If a
tag claims uniqueness, **find the migration that created it** or the invariant
does not hold.

---

## The verification ladder

For every claim, in order. Stop when you have primary evidence.

1. **Code navigation** — find the symbol with your best tool (a code graph or
   LSP first) and read it.
2. **Migrations** — text-search the *table name* across **all** migration
   files, not just the creating one. Read every hit.
3. **Model / struct** — last, and only to learn intent.
4. **Tests** — a test named for a behaviour is evidence the behaviour exists.
   `TestX_RecordsAuditEvent` existing is stronger than your reading of the code.

⚠️ Plain text search is the right tool for step 2. It is a **string-literal
search over migration files**, which code graphs and symbol indexes do not cover.

---

## What a characterisation must contain

**Every claim cites a `file:line` or a named constraint.** No exceptions.

**Mark inferences `[UNVERIFIED]`.** An unchecked claim is not a negative
finding. Write *"whether X happens was not traced"*, never *"X does not
happen"* — unless you looked.

**Scale depth to evidence.** A subsystem with two tables and no constraints gets
a short spec that says so. Uniform depth over uneven evidence produces ceremony,
not knowledge. State it outright:

> ⚠️ **LOW EVIDENCE.** Absence of rules here means **uncharacterised, not
> clean.**

**Record what is exemplary, not only what is broken.** A defect list is
demoralising and, worse, misleading — if the codebase already solves a problem
somewhere, that is the argument the gap is fixable *and* the template for
fixing it. Name the table that gets it right.

**State the enforcement ratio.** "One of five rules is structurally enforced"
is the honest summary of a subsystem. The rule count is not.

---

## Known debt — the format

Consequences recorded **rather than discovered later**. One per finding:

```markdown
**GD-1 · <the finding as a sentence>.** `open:`
<what is true, with file:line>

⚠️ **<why it matters>** — the failure mode, concretely.

**Owned by nobody.** / **Owned jointly by X and Y; neither has taken it.**
```

Status markers: `open:` · `deferred:` · `allocated-to: <id>` · `CLOSED (date)`.
**Every item names an owner, even if the owner is nobody.** An unowned item that
says so is tracked; an unowned item that stays silent is lost.

State the **blast radius** where it is not obvious:
> *Blast radius: every query joining assignments to booking data.*

---

## Corrections — strike through, never delete

You **will** be wrong. The corpus is more trustworthy for showing it.

```markdown
> ⚠️ **Corrected <date>.** ~~Superseded wording, do not implement: "<the old
> claim>"~~ **Wrong.** <what is actually true, with evidence>. <one line on how
> the mistake happened, so the next author avoids it.>
```

A reader who remembers the old rule finds it marked, rather than wondering
whether they imagined it. **Deleting a wrong claim destroys that.**

⚠️ When you correct a claim, **search for it everywhere**. A finding propagates:
one wrong claim was once repeated in five files. Fix the entry *and* every
reference.

---

## Sequence

1. **Size the evidence first.** Count migrations and models per subsystem in one
   sweep. Decide depth **before** writing anything.
2. **Read the migrations for the tables in scope** — all of them, in order.
3. **Read the models** for intent and naming.
4. **Write the data model first.** It is where the facts live; everything else
   cites it.
5. **Then the behaviour: rules and journeys**, in whatever spec format the
   project uses (`spec-writing` if installed).
6. **Sweep your own absence claims.** Before finishing, search your output for
   "nothing", "never", "no X exists" and re-verify each against the schema.

---

## Red flags

| Thought | Reality |
|---|---|
| "The struct says `NOT NULL`, so it is" | The struct says what the app believes. Read the migration. |
| "I read the `CREATE TABLE`, I know this schema" | Later migrations alter it. Read **every** hit for that table. |
| "There's no idempotency key on this table" | Check whether a dedup table exists elsewhere first. |
| "No events are published, so nothing is recorded" | Audit rows are a different mechanism. Check both. |
| "This subsystem is small, I'll write it up like the others" | Scale depth to evidence, and say the evidence is thin. |
| "I'll fix the wrong claim by editing it" | Strike it through. The correction is the valuable part. |
| "The reviewer said it's clean" | A clean verdict raises confidence; it is not verification. |
