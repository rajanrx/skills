---
name: spec-impact
description: Walk the ten impact categories a change can touch — request/response shapes, persisted data, event payloads, generated artifacts, backward compatibility, access control, money, configuration, deployables and rollback — before any spec, design or plan is presented for approval, so no affected consumer is omitted by improvisation. Record material impacts in the project's existing spec or design artifacts without creating duplicates.
version: 1.0.0
---

# spec-impact — the ten categories, every time

⛔ **A spec written from scratch omits whatever the author did not happen to think
of.** A real spec was once authored with no consideration of a response
data-structure change, so the client regeneration, the drift it caused, and every
in-flight client were all absent from a document that read as complete.

**Improvisation is the defect. The checklist is the fix.**

Discover the project's constraints (decision records, rules, standards) before
drafting. Use `spec-writing`, if installed, for the spec itself; this skill only
makes sure the impact section is complete.

---

## ⛔ Before the ten: write with BOTH lenses at once

A spec is not an engineering document with a business paragraph on top. **Every
claim in it has to survive two different readers, and you are both of them.**

| | The product owner asks | The architect asks |
|---|---|---|
| Purpose | who is affected, and what do they experience? | what breaks, and what is the blast radius? |
| Evidence | what would a user accept as done? | what did you MEASURE, and with what query? |
| Scope | what problem are we NOT solving? | what does this force downstream? |
| Risk | what does the business lose if this is wrong? | what is unrecoverable after deploy? |

⛔ **Asking them in sequence is how the failure happens.** Write the user story,
then hunt for facts to support it, and you will find the facts that agree. The two
lenses must be applied to *the same sentence*, at the same time.

### The worked example — how this went wrong, concretely

A spec claimed: *"zero frontend code reads these error codes, so renaming 50 call
sites is free — there is no consumer to break."* It was the load-bearing premise:
it set the risk level, sized the work, and justified doing the rename now.

**Both halves were false, and both for the same reason.**

- The consumer count came from searching for **`errorCode`** — a token that
  **never appears on the wire.** The field is `code`. Sweeping the real shape found
  **5 production sites branching on a specific code string**, in two critical
  user flows.
- The site count came from matching struct literals, missing the constructor form
  `NewErrorResponse("INVALID_ACCOUNT", …)` — **774 of 817 sites**.

🔶 **The architect's failure was searching for a name instead of tracing the
contract.** Had the question been *"what does a consumer actually receive?"*
rather than *"where does the string `errorCode` appear?"*, both numbers would have
been right the first time.

🔶 **The product owner's failure was accepting an absence as a finding.** *"Nothing
reads this"* is a strong claim about the world, and a strong claim needs the query
that produced it written down beside it — so the next reader can see what was
searched for and notice it was the wrong thing.

### ⛔ Verify the edit applied. Every time.

A scripted string replacement that does not match **fails silently and returns the
original text**. In one session that happened four times and produced:

- a regression check that detected regressions and exited **0**;
- a CI block with no path guard, silently unchanged;
- **a destroyed build target**, left pointing at a production system;
- a **commit message claiming a fix that was not in the commit**.

The last is the worst: it put a false statement into the permanent record, and the
reviewer, not the author, found it.

⛔ **Assert the anchor before, and assert the result after.**

```python
assert old in t, "anchor missing — read the file, do not guess again"
t = t.replace(old, new, 1)
assert "<something only the new text contains>" in t, "edit did not apply"
```

🔶 **Anchor on structure, never on a substring that can appear inside another.**
`"dashboard:"` matched inside `other-dashboard:`. Use `^name:` with a multiline regex.

### The rule this yields

⛔ **A counted claim in a spec carries the command that produced it.** Not the
number alone — the number *and* how it was obtained. A figure with no reproducible
query is an opinion with a digit in it.

⛔ **An absence is the most dangerous kind of claim.** "No consumers", "no tests",
"no writers", "nothing depends on this" — each sets scope and each is one wrong
search term away from being backwards. Prove an absence two different ways, or
mark it `[UNVERIFIED]`.

## The rule

Consider all ten categories so affected consumers are not missed. Record material
impacts in the project's existing spec or design artifacts; group categories with
no impact into one short explanation (`N/A — <reason>`).

---

## 1 · Response & request data structure

⚠️ **The one most often missed, and the most expensive.**

- Does any **response** shape change — a field added, removed, renamed, retyped,
  or moved between nesting levels?
- Does any **request** shape change? ⛔ If clients are generated from an API
  description, a new request field **requires** regenerating them — without it the
  field is silently dropped by the generated client, and nothing errors.
- Does an **enum or string constant** on the wire change value?

**Forces:** API description update → client regeneration → check the regeneration
for unrelated drift → typecheck every consumer.

🔶 A consumer typecheck passing is **not** proof a removed field is gone — some
consumers hand-write their types over raw HTTP calls.

## 2 · Persisted data

- New or changed **columns**, tables, indexes, constraints → a migration, and
  usually a higher risk level and deeper review.
- ⚠️ **Values written into EXISTING columns.** A string constant that is persisted
  is data, not code. Renaming it is a **data migration**, not a rename.
  *Worked example:* a column stores a reserved literal such as `"quote expired"`,
  and a business rule decides reversibility by matching it. Change that string and
  old rows mean something different from new rows.
- Are there **existing rows** that will not self-heal? Say so — QA will otherwise
  retest a stale record and reopen a fixed ticket.

## 3 · Event payloads

- Any message or event topic's payload shape, or the set of topics.
- ⛔ **Messages already queued — in a broker, an outbox table, a retry queue — were
  written by the OLD code and will be consumed by the NEW.** A payload change is a
  compatibility problem the moment it deploys, not when it is written.

## 4 · Generated artifacts

Generated clients and SDKs, API descriptions, generated types. **What regenerates,
and what does that regeneration drag with it?** A no-op regeneration can produce
hundreds of changed files of generator drift.

## 5 · Backward compatibility

- An **older frontend** against the new API, and a **new frontend** against the old
  API — both happen during a rollout.
- Queued work written by the old version.
- Cached values, including identity and session caches.
- ⛔ **Order matters and is not universal**: a constraint that rejects the old
  version's writes must ship *after* the new version; a column the new version
  reads must ship *before*. Decide per change.

## 6 · Multi-tenancy & access control

Any new query path, any predicate change, any role or permission. **Touching this
raises the risk level — say so out loud.**

## 7 · Money & currency

Amounts, currencies, rounding, ledgers, refunds. Zero-decimal currencies exist.
**Raises the risk level.**

## 8 · Configuration & secrets

New environment variables, feature flags, secrets. ⚠️ A missing required key can make
a service fail on boot, and a secret often has to be added in more than one place
(the secret store, the deployment config, every environment).

## 9 · Deployables

⛔ **Which binaries, images or services actually contain the change?** A fix in a
unit you did not release looks deployed — new version, pipeline green, health 200 —
while the unit running the code is unchanged.

Establish it from the build's dependency graph (for example `go list -deps`), not
by file path: shared internal packages link into several deployables.

## 10 · Rollback

**If this is reverted an hour after deploy, what is left behind?** Migrated rows,
consumed events, regenerated clients, renamed constants. A change with no rollback
answer is a change nobody can safely deploy on a Friday.

---

## Output

Write the answers into the project's existing spec or design artifact for the
change, expanding material impacts where the design is described. This checklist
adds no review gate of its own; review follows the project's change process.

## Related

`spec-writing` (authors the spec) · `ticket-plan` (records deployables and checks
per task) · `author-preflight` (the author's pass before review) — each if installed.
