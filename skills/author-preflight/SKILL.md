---
name: author-preflight
description: The author's own adversarial pass before handing work to any reviewer — a plan, design or spec before review, or a code branch before a pull request. Use when a plan or an implementation is about to be called done, and after fixing any review finding. Verify every claim about existing code, trace every caller, run a pre-mortem, say each rule once, and walk every fix through its own failing case.
---

# Author preflight

**Getting it right first time is cheaper than any review.** In one team's review
records (50 design reviews), 70% needed three or more rounds. Round 1 found real
design gaps; rounds 2 and 3 were mostly the document contradicting itself, or a fix
creating the next blocker. Every one of those was findable by the author in minutes.
This skill moves the finding to the author's desk.

Five failure causes, and the check that removes each. Do all five, in order, and
paste the evidence into the handoff. **An unticked step is not "probably fine".**

## 1 · Verify — no claim about existing code without a line you opened

The failure: fluent, confident text about what the code does, never checked.
*"The column default marks invited users as verified"* — but invites insert
through the ORM, which writes NULL over the database default. "False premise
about existing code" was the single most common round-1 finding.

- Every sentence that says the system **does, never does, always, only, already**
  cites a `file:line` **you opened in this session**. Use your best code-navigation
  tool (a code graph or LSP first, grep last), then read the lines.
- Cannot cite it → mark it `[UNVERIFIED]`. Never state it as fact.
- ORM versus database: for every new column default or constraint, find the real
  insert/update path (raw SQL or ORM?) and say which one writes it.
- **A doc is a claim too, and it can be months out of date.** Specs, design docs
  and ADRs were written against the code of their day. Before building on one,
  check what it says against the code. Then:
  - **The doc states a fact the code settles** (a column exists, a function does X):
    the code wins. Fix the doc with a dated correction (see `spec-writing`) and cite the line.
  - **The doc records a decision that conflicts with the code, another doc, or a
    later decision** (a rule, a scope, who may do what): **stop and ask the human**,
    with the evidence both ways and your recommendation first. Record the answer
    where it binds. Never pick silently: an agent choosing between two decisions is
    making a decision nobody took.
  - Example: a domain doc said a table has "no `deleted_at`". The code and the
    first migration both have it, and a reviewer filed a blocker built on the doc.

## 2 · Trace — every caller, every writer, every door

The failure: fixing the thing in front of you and missing the third caller.

- For every function, column, route or event you change, list its callers in both
  directions (a code graph's call trace, or LSP find-references). Paste the caller
  table. Mark each row **covered by task N** or **out of scope because …**.
- For every table or column: every writer (INSERT, UPDATE, ORM create/save/update).
- **Every file the plan says it affects is named in a task** — the task that
  changes it, or its test. A path that appears in the plan with no task is a gap.

## 3 · Attack — a 10-minute pre-mortem before anyone else sees it

The failure: stopping when it looks done. The reviewer's first hostile reading is
the first one the work gets, so the reviewer always finds something.

Set 10 minutes. Write **ten specific past-tense failure stories** — "it failed
because…" — concrete, not categories. Then walk each against the plan or code and
fix what it exposes. Always include these, because they recur:

| Ask | How often it recurred |
|---|---|
| Two of these run at once. What does each read, and is it judged **after** the lock? Idempotent on retry? | most common by far |
| Every requirement or scenario: which named test fails if it breaks? | very common |
| A term used in two files — defined once, same meaning? | common |
| Deploy order: old code + new schema, new code + old schema, old client + new API. Walk each. | common |
| A limit, guard or refusal sits on a path that is **required to succeed** — which? Fail open or closed? | common |
| An unauthenticated caller floods it. Who is locked out? (a global bucket locks out everyone) | occasional |
| A secret or token: where is it logged, cached, or put in a URL? | occasional |
| For every gate or condition: **who else can satisfy it, and can the person it blocks create or promote someone who does?** Walk gaining a role, not only losing one. Two decisions that are fine alone can compose into a bypass. | rare, severe |

Skip the pre-mortem only for a one-line, instantly reversible change.

⚠️ **Why the last row exists.** One pre-mortem asked what happens when an
organisation *loses* its only owner, and never what happens when one is *gained*.
"Invited users are verified by default" plus "any verified owner lifts the block"
let an unverified owner promote an invitee and go live without ever receiving an
email. Two reviewers found it independently. Ten minutes of this question would
have found it first.

## 4 · Say it once — decide the *what*; prove the *how* in code

The failure: the same rule restated in eight files, so every fix must land eight
times, and one miss is next round's finding. "A fix leaves a stale sentence
elsewhere" was among the most common later-round findings.

- Each rule, limit, number or decision has **one home**. Every other file **points
  at it** (`see Email verification → Redeem rules`) and never restates it.
- A plan decides **behaviour, decisions and examples**. It does not script locks,
  SQL, ORM tags, header config or goroutines — those are proven in code, by a
  failing test, and checked in code review. Mechanics go in the plan only when they
  **are** the decision (e.g. "the migration ships a release before the code",
  because someone must accept the window).
- If the plan is long, that is a prompt to cut, not to justify.

## 5 · Fix discipline — every fix gets its own failing case

The failure: under pressure to close findings, edit the sentence the reviewer
quoted and move on. That fix often becomes the next round's blocker (a "fallback"
that logged the token; a stale line elsewhere still stating the old rule).

For **every** finding you fix:
1. Write the failing case in one line. Walk the fix through it — and through the
   previous findings' cases (the regression matrix).
2. Give the fix a threat line: *what can now reach this path that could not
   before?* Trace every code location the fix newly names and record each as
   handled or out of scope.
3. Prove the retired rule is gone everywhere. Search for the old wording and its
   word forms ("proved" finds "proves") across every file, not only the one quoted.
4. A fix that only edits the sentence the reviewer quoted is probably not a fix.
   Real fixes usually change a structure: what is stored, locked, counted or bounded.

## The gate

Run this before requesting review and before reporting any work done. In the
handoff, paste — do not summarise:

- the caller/writer table from step 2;
- the ten pre-mortem stories, each with *handled by …* or *accepted because …*;
- for a code branch: the test command and its output (evidence, not "all tests pass").

**The author is not the grader.** This pass makes the reviewer's job small; it does
not replace the reviewer. A fresh-context review still runs.

## Sources

- GitHub Spec Kit `/analyze` — a read-only cross-artifact consistency and coverage
  pass before implementation.
- Pre-mortem (Gary Klein) — concrete past-tense failure narratives in a short time box.
- Anthropic, *Best practices for Claude Code* — give the agent a way to verify its
  own work; show evidence rather than asserting success; the agent doing the work
  is not the one grading it.
