---
name: record-craft-review
description: Turn a review into a durable record — the findings, the verdicts, the evidence behind them, and what was actually done about each. Use after any code or design review pass, when asked to "record the review", or when a review's reasoning would otherwise be lost once the branch or pull request it was attached to is gone.
version: 1.0.0
---

# Record craft review

A review produces two things: a set of fixes, and the **reasoning that found
them**. The fixes get committed. The reasoning is usually thrown away.

⛔ **Review notes kept per branch or per pull request become unfindable.** Even
when they are committed, they are named after a branch slug or a PR number, and
branches get deleted. Six months later nobody knows the slug, so *why* a finding
was raised — and why one was rejected — is as good as gone. The next person
re-derives it, or repeats the mistake.

---

## What to record

Per round, not per finding. One file, append-only.

```markdown
# Review — <branch or PR>

**Base sha:** …   **Branch tip at round N:** …
**Depth:** light / standard / multi-reviewer   **Reviewer(s):** …

## Round N — <date>

| # | Finding | Verdict | Status |
|---|---|---|---|
| P1-1 | <one line> | CONFIRMED | ✅ fixed |
| P2-1 | <one line> | PLAUSIBLE | ⏭️ deferred — <why> |
```

Then, for each finding that mattered:

- **the claim**, in one sentence
- **the evidence** — `file:line`, or the constraint, or the test name
- **what was done**, or why nothing was
- ⚠️ **who verified it, and how** — "reviewer said so" is not verification

---

## 🔶 The three things worth more than the findings

**1. Findings that were WRONG.** A reviewer's rejected claim is as valuable as an
accepted one — it stops the next reviewer raising it again. Record it with the
evidence that killed it.

**2. The root cause across findings.** Individual defects are cheap; the habit
that produced five of them is not. If a round's findings share a cause, name it
at the top. That sentence is the whole point of the record.

**3. What was verified versus assumed.** Every review has both. A record that
does not distinguish them lets an assumption harden into a fact — say plainly
which claims were executed and which were reasoned.

---

## Verdicts

| Verdict | Means |
|---|---|
| `CONFIRMED` | reproduced, or proven from the source |
| `PLAUSIBLE` | reasoned but not executed — **say what would confirm it** |
| `REFUTED` | checked and wrong. **Keep it.** |

⚠️ **Never record a finding as CONFIRMED because a reviewer sounded certain.**
A clean verdict raises confidence; it is not a substitute for checking. Tests
have been known to pass **with the fix reverted**.

---

## ⛔ Where it goes

A per-branch review file is the working record — fine during the branch,
**unfindable afterwards**.

For anything whose reasoning should outlive the branch, the durable homes are:

- **the code** — a comment at the constraint or the guard, saying why
  (use `comment-discipline` if installed)
- **the project's known-debt list** — if it is a fact about the system
- **the decision-record backlog** — if it is an undocumented decision
  (use `adr-author` if installed)
- **the commit message** — the highest-survival, lowest-effort option, and the
  one most often skipped

🔶 **Write the finding into the artifact it is about.** A review record nobody
opens is only marginally better than no record; a comment at the line is read by
whoever next touches it.

---

## Re-reporting after fixes

When a round's fixes land, update the same file — do **not** start a new one and
do **not** edit history. Mark each finding `fixed` / `skipped` /
`no_change_needed`, and **strike through** anything that turned out to be wrong
rather than deleting it.

⚠️ **A review of commit A does not cover the commits that answered it.** If the
fixes were substantial, they need their own pass, and the record should say
whether they got one.
