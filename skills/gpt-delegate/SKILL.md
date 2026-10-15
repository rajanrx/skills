---
name: gpt-delegate
description: Delegate an IMPLEMENTATION task to a second model (for example GPT via the Codex CLI) and verify what comes back, rather than only using a second model to review. Use when a task is well-specified, mechanical and independently verifiable, or when asked to "get GPT to do this". Gated — not a default path unless the project owner has opted in.
version: 1.0.0
---

# GPT delegate

> ⚠️ **Gated.** Most teams use a second model **only to review**. This skill is
> the other direction: a second model **writes code**. **Do not make this a
> default path unless the project owner says so.** Write the discipline down
> *before* the first delegation, not under time pressure.

---

## ⛔ What may be delegated

All four, or delegate nothing:

1. **Specified** — the change is described precisely enough that "done" is not a
   judgement call
2. **Mechanical** — applying a known pattern, not choosing one
3. **Independently verifiable** — a test, a constraint or a diff-read proves it,
   without trusting the report
4. **Bounded blast radius** — the worst outcome is a bad diff you throw away

## ⛔ What may NOT be delegated

- **Money, payments, auth/access-control, multi-tenant isolation, migrations** —
  the highest-risk tier. These need more independent scrutiny, not a second
  author.
- Anything where **discovering the right shape** is the work
- Anything touching **release, deploy or production**
- Anything where you could not tell a correct result from a plausible one

---

## The brief

Delegation fails on **under-specification**, not on model capability.

- **Paste the context** — file paths, signatures, the exact lines. Do not make it
  rediscover what you already know.
- **State the success criteria** as something checkable.
- **Name the files** it may touch, and say what is out of scope.
- ⛔ **Scope the brief to the files in play.** Not a huge full-branch diff.
- Say explicitly: **no push, no merge, no release.**

---

## ⛔ Verifying what comes back

**The report is a claim. The diff is the evidence.**

- [ ] **Read the whole diff.** Not the summary.
- [ ] **Run the tests yourself.** ⚠️ Tests can pass **with the fix reverted** —
      a green report proves nothing on its own.
- [ ] **Mutate the fix and confirm a test fails.** If nothing fails, the change is
      unverified regardless of what came back. (`tdd`, if installed, has the
      critic pass for this.)
- [ ] **Check it did only what was asked.** Unsolicited scope continuation is the
      characteristic failure — diff-verify the final state rather than trusting
      the first "done".
- [ ] **Confirm nothing was pushed, merged or released.**

🔶 **You own the result.** Delegation moves the typing, never the accountability.

---

## Review vs delegation

| | Second model… | Risk if it is wrong |
|---|---|---|
| second-model review | **reviews** | a bad finding you can reject |
| `gpt-delegate` | **writes** | a bad diff you might merge |

⚠️ **The asymmetry is the whole reason this skill is gated.** A wrong review
costs a conversation; a wrong implementation costs whatever it touched.

**Two AI models agreeing is correlated, not independent.** If the delegate wrote
it, the delegate's confidence is not review — it still needs a human or a
genuinely independent pass.
