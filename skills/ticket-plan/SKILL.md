---
name: ticket-plan
description: Plan a ticket or task before any implementation — reuse discovery once product decisions are resolved, then write the per-task context folder (for example `../context/<TASK-ID>/`, or wherever the project's instructions file says) with the symptom, goal, explicit non-goals, a verified codemap, the deployable units affected, and a per-test mutation contract, so implementer and reviewer work from one agreed target. Use when asked to "start", "plan" or "pick up" a ticket, or before dispatching any implementation agent on one.
version: 1.0.0
---

# Ticket plan

Unresolved product decisions or decision conflicts block planning: take them through
the project's change process first. Gather evidence and questions in the task's
context folder while they are open — the same folder `context-driven` uses, if
installed. Once they are resolved, reuse that context to plan without repeating
discovery. If the project's change process already owns an implementation plan (a
tasks file in a change package, say), link it from context rather than creating a
second plan. This skill adds no approval gate and no fixed review process of its own.

## Why this exists

Tickets dispatched without an agreed target went through up to **ten** review
rounds. The pattern was identical every time:

- The controller briefed a **symptom**, so the agent scoped a fix to the
  symptom's layer. A ticket briefed as "the wrong people get the notification
  email" was really "the edit path never maintains the assignment records". Every
  email-layer patch leaked, and each review found the next leak.
- The agent burned most of its budget **rediscovering** the codebase, arrived at
  the implementation with little left, and self-certified with tests that could
  not fail. One reported "80/80 passing" while the single line protecting the
  commit path was deletable with **zero** test failures. Another's reset test was
  green with the reset deleted.
- The agent reported writing a context folder that **did not exist**.

None of that is caught by reviewing harder. It is caught by agreeing the target
before anyone writes code, and by making "the tests pass" mean something.

## Step 1 — establish the SYMPTOM, never your theory

Write down what a human observed, in their words. If you have a hypothesis,
mark it `[THEORY — unproven]` and put it at the *bottom*.

⛔ A confident wrong hypothesis costs more than none: the implementer must spend
budget disproving it before it is allowed to look elsewhere. Wrong theories are
common; the real cause often falls out in minutes once the plain symptom reaches
the agent.

## Step 2 — discovery, 5–10 targeted lookups, done ONCE

Use the project's preferred navigation tools. Find and record, with `file:line`:

- the entry point the symptom actually goes through
- every **other** writer of the same state (this is where the leaks live)
- the existing tests that claim to cover it
- any comment in the repo that already warns about this code

⛔ **Look for the warning that already exists.** One fix depended on a cached field
that another write path never updated — and a sibling handler already carried a
comment saying that field "can be stale after a change via another path", and
deliberately read the source records instead. The information needed to not write
the bug was in the repo, in a comment, next to the same field.

## Step 3 — name the GOAL and the NON-GOALS

The non-goals matter more. They are what stop the drift.

> **Goal.** After an edit, the people who are actually assigned receive the
> notification and nobody else does.
>
> **Non-goals (do NOT fix on this ticket; file instead).**
> - Assignment *records* are not maintained by this path — added people get no
>   record, removed people keep theirs. That is a separate ticket and a product
>   decision.
> - Email copy accuracy on the removal template.

If a non-goal turns out to be load-bearing — the goal cannot be met without it —
**stop and escalate**. Do not silently widen scope, and do not grind review
rounds against a goal that was never achievable.

## Step 4 — record risk and the applicable review

Follow the project's own rules for spec review depth and independent code review.
Record the affected guarantees and material risks; do not invent extra reviewers,
gates or a fixed review-round limit.

## Step 5 — the mutation contract (the part that ends vacuous tests)

For every behaviour the ticket must guarantee, write one line:

> *To prove X, delete/invert `<file:line>` and test `<name>` must go RED.*

This is a contract the implementer has to satisfy and the reviewer can check
cheaply. It converts "all tests pass" from a claim into evidence. (`tdd`, if
installed, covers writing those tests.)

⛔ **The implementer does not report the work complete until it has reverted its
own fix, shown the test go red, restored it, and shown it go green — and quoted
the red output line.** Not the reviewer's job. The reviewer's job is to find
what the contract missed.

## Step 6 — map the diff to deployable units BEFORE anyone implements

Which binaries, images, services or packages actually contain the changed code?
Establish it from the build's dependency graph (for example `go list -deps`), not
from file paths — shared internal packages link into several deployables.

Record which releases this ticket will need. A fix in another deployable looks
shipped — new version, pipeline green, health check 200 — while the unit running
the code is unchanged, so QA sees the identical bug and reopens the ticket.

Also record which current checks (CI pipeline, pre-commit hooks) cover the affected
surface, and any gaps. Read the actual config rather than relying on what someone
says CI does.

## What gets written

```
../context/<TASK-ID>/
  index.md      # living summary: symptom, goal, NON-GOALS, risk, status, deployables, check gaps
  codemap.md    # APPEND-ONLY verified file:line facts + corrections to earlier claims
  decisions.md  # APPEND-ONLY decisions, scope lines, rejected options + why
  plan.md       # only if the change process has no plan of its own; otherwise link that plan
```

Rules:
- Only **verified** facts. Mark inferences `[UNVERIFIED]`. Later agents skip
  re-checking *because* this folder is trusted — one unverified claim recorded
  as fact poisons every downstream agent.
- **Corrections to earlier claims are the highest-value entries.** Write them.
- Review findings and severities go wherever the project keeps review findings,
  not here. *"What the reviewer found" → review record; "what is true about the
  code" → context.*
- Write as you GO. Agents get killed mid-run; what survives is what was written.
- **Never claim a write you did not make.** Verify the file exists after writing.

## Dispatching after the plan

Name the context folder and include only the load-bearing facts in the dispatch.
Do not paste the whole folder.

> Read `../context/<TASK-ID>/` FIRST. It is populated; cite it, do not
> re-derive it. The goal and non-goals in `index.md` are the scope — do not
> widen them. Satisfy the mutation contract in `plan.md` and quote the red
> output for each. Append what you verify to `codemap.md` before reporting.

## Human decisions

Ask for unresolved product choices and conflicting decisions before writing a plan.
Ordinary implementation choices within agreed constraints need no extra approval.
Record evidence as you discover it; do not delay context notes until approval.
