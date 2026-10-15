---
name: context-driven
description: Keep a per-task context folder outside the repo (for example `../context/<TASK-ID>/`, or wherever the project's instructions file says) — read it before searching, write measured findings to it before reporting, and pass its path into every subagent dispatch. Use at the start of any task or ticket and in every subagent dispatch, so verified discovery is never paid for twice.
version: 1.0.0
---

# Context-driven

**Read it before you search. Write it before you report. Pass it to everyone you dispatch.**

⛔ **This is not a restriction. It is the shortcut.** The folder exists so that the
expensive discovery someone already paid for is never paid for again. Treating it
as optional is what makes work slow, not what makes it safe.

## Where the folder lives

One folder per task, **outside the code repo**: by convention `../context/<TASK-ID>/`,
a sibling of the repo and of every worktree. If the project's instructions file
(CLAUDE.md, AGENTS.md or similar) names a different location, use that.
`ticket-plan`, if installed, writes to the same folder.

- Outside the repo means **every worktree and every tool sees the same folder**:
  no per-branch copies to diverge, nothing to merge.
- ⚠️ **Not agent-specific.** No tool-specific prefix such as `.claude/`: every agent
  reads and writes the same folder. If you are writing for one tool only, you are
  writing in the wrong place.
- Version it if you can (a local workspace repo is enough), so corrections have history.

---

## Evidence and decisions

Start with the context folder to reuse verified discovery. Then read applicable
specifications and inspect the affected code. Keep three things distinct: accepted
intent, observed implementation and proposed change.

- A factual claim about today's implementation can be stale in either context or
  a specification. Verify it against code, then correct the document with dated
  evidence and append the correction to context.
- A recorded behavioural or architectural decision remains a constraint until
  explicitly changed. If it conflicts with the request, another decision or the
  implementation, show both sources and ask the human to resolve it. Code does
  not silently ratify a different rule.
- Missing product decisions or unresolved decision conflicts block specification,
  design and task planning. Evidence gathering and recording the question can
  continue; the decision itself goes through the project's change process.

Neither "the spec always wins" nor "the code always wins" settles both kinds of
claim.

### ⚠️ A spec EVOLVING from the context folder is normal — expected, even

This is the ordinary lifecycle, not an exception to be argued for: you measure, you
record, and what you recorded shows the spec is incomplete, ambiguous or wrong. Then
the spec changes. Many of the best spec changes start exactly there.

⛔ **What is forbidden is doing it SILENTLY, or doing it to excuse the code.**
The distinction is the whole rule:

| Legitimate — do this | Forbidden |
|---|---|
| The measurement shows a case the spec never covered → propose the spec change, in the open, through its own process | Editing the spec so a defect stops being a defect |
| The spec contradicts itself → report it, raise it as its own item | Quietly picking whichever half the code already does |
| A dated correction annotating an accepted decision record | Rewriting an accepted decision record in place |
| A spec change committed on its own | A spec change ridden along with the code it judges |

⛔ **Spec and code NEVER move in the same commit.** Commit the spec first, alone, so
it can still be rejected. That one constraint is what keeps "the spec evolved from
what we learned" honest and stops it becoming "the spec moved so the code would pass".

⚠️ **A disagreement you find is a FINDING, not a nuisance.** Report it, and leave a
dated correction in the context folder pointing AT the spec.

Apply the same distinction to instructions files and engineering docs: correct
verified factual drift, and ask the human about conflicting decisions. Context
records evidence; it does not authorise changes to intended behaviour.

---

## The three rules

### 1 — READ BEFORE YOU SEARCH

⛔ **Before the first grep, the first code search, the first file read: open the
task's context folder.**

```bash
ls ../context/<TASK-ID>/ 2>/dev/null && cat ../context/<TASK-ID>/*.md
```

⚠️ Also check the parent and sibling tasks of an epic. A related task's folder often
already holds the answer to the question you are about to ask.

⚠️ **A file there is a MEASUREMENT, not a rumour** — entries carry the query that
produced them. But they were true *when written*: if one names a file, function or
line number, re-verify before relying on it, and **record the correction** (rule 2).
A correction is the highest-value entry in the folder.

### 2 — WRITE BEFORE YOU REPORT

⛔ **Anything you had to measure, and would have to measure again, goes in the
folder BEFORE you report done.** Not after. Not "later".

| Belongs | Does not |
|---|---|
| A query and its result ("13 call sites, three files") | Anything version control already tells you |
| A hazard with its evidence ("an empty period advances the cursor and issues nothing — `foo_test.go:476`") | Review findings on the current diff → wherever the project keeps review findings |
| An ordering constraint ("assign the plan BEFORE starting the cycle") | Restating what an instructions file already says |
| A correction to an earlier entry, dated | Anything secret — see below |
| Why an obvious approach was rejected | Narration of what you did |

⛔ **NEVER write a secret, token, credential, customer name or production data
extract into this folder.** It may be committed, it is read by every agent and tool
on the machine, and it is routinely pasted into subagent prompts — three ways for it
to travel further than you intended. Aggregate counts are fine ("45 of 98 accounts
share one creation day"); an account's name is not.

`decisions.md` and `codemap.md` are **append-only** — add a dated section, never
rewrite history.

### 3 — ⛔ PASS THE FOLDER INTO EVERY SUBAGENT DISPATCH. MANDATORY.

**A subagent cannot read what you did not tell it about.** It starts with no memory
of this session, so an unmentioned context folder does not exist to it — and it will
rediscover, expensively, exactly what the folder already contains.

Every subagent dispatch — implementer, reviewer, explorer, planner, any of them —
includes:

```
## Context — READ THIS FIRST, BEFORE ANY SEARCH
`../context/<TASK-ID>/` — the measured record for this task, shared by every
worktree. Read every file in it before your first search.
Also read: ../context/<RELATED-TASK>/   (name the siblings that matter)

⛔ Before you report, append what you had to measure and would otherwise be
measured again. Corrections to existing entries are the most valuable thing you
can leave. No secrets, no customer names, no production extracts.
```

⚠️ **Paste the load-bearing facts inline as well.** The folder is the durable record;
the dispatch is what the agent reads first. Facts you already hold are *given* — an
agent re-deriving them is pure waste, and in a multi-round review you pay for it
every round.

⛔ **Given FACTS are given; CLAIMS are never given.** Hand over where code lives and
what a query measured. Never hand over "this is correct" or "this is covered" — that
is the thing the agent exists to check.

---

## Creating the folder

⛔ **The controller creates and populates it BEFORE the first dispatch.** Do the
cheap discovery yourself — five to ten lookups — then dispatch. An agent sent out
with an empty folder fills it with rediscovery you could have skipped.

```
../context/<TASK-ID>/
  index.md        what this task is, and the current state of play
  codemap.md      where the relevant code lives (append-only)
  decisions.md    what was decided and why, dated (append-only)
  <topic>.md      one measured investigation per file
```

⛔ **Give the SYMPTOM, not your THEORY.** A confident wrong hypothesis costs more
than none, because the agent must disprove it before it may look anywhere else.
Mark anything unverified `[UNVERIFIED]`.

Delete the folder once the task is merged and closed.

---

## Why this exists

A task's context folder already held a measured list of every write path for the
state being changed, with the query that produced it, plus a decisions file recording
a spec gap and its consequence. Two reviewer dispatches went out without mentioning
the folder. Both re-derived the write paths from scratch. The controller had not
read it either, and re-derived the same thing a third time.

⚠️ **The cost was not only tokens.** The folder recorded that a period with nothing
to charge *advances the cursor and issues nothing* — which meant doing two setup
steps in the wrong order silently lost data and could not be undone. That hazard was
found by reading the folder, late, and would have shipped otherwise. **The
rediscovery cost was visible; the missed-hazard cost was not.**

⛔ **The tell you are about to repeat it: you are writing a dispatch prompt and it
contains a "GIVEN FACTS" section you assembled by hand.** If you had to assemble it,
it belongs in the folder — put it there, and point the agent at it.

---

## Once you have read the context

The context folder tells you what is already known. Look up only the gap, with the
project's preferred navigation tools — and anything that search cost you to
establish belongs back in the folder (rule 2). That is the loop: read what is known,
look up only the gap, write the gap down.
