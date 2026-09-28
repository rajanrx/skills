---
name: comment-discipline
description: Decide what to comment and what to delete. Use when writing or reviewing code comments, or when a diff adds narration. Comment only what the code cannot say, and leave comment density lower than you found it.
version: 1.0.0
---

# Comment discipline

Most codebases already have a comment policy somewhere. It is usually ignored.
This skill is that policy, made invokable.

---

## Comment ONLY when the code cannot speak

Write a comment for:

- a **non-obvious why** — the reason a surprising choice is correct
- a **genuine gotcha** — a trap the next reader would fall into
- a **subtle invariant** — something that must stay true and is not visibly enforced

A solid one-liner beats a paragraph.

## Do NOT write

- **narration** — restating what the code plainly does
- **the symbol name again** — `// UserRepository fetches users`
- **ticket, stage or RFC references scattered through source** —
  `// Stage 5b: …`, `// PROJ-131 territory`, `// caught in review`

That history belongs in **commit messages and pull requests**, which is where
anyone looking for it will search. In the source it is noise that survives long
after the ticket is closed.

---

## The rule that makes it stick

**When you touch code and see noise, delete it.** Leave comment density **lower
than you found it**, never higher.

Deleting a bad comment is a real contribution and needs no permission.

---

## What a load-bearing comment looks like

```go
// DeletedAt makes the ORM's Delete() an UPDATE instead of a DELETE, so the
// charges table's ON DELETE CASCADE foreign key never fires. Without this,
// deleting a session permanently destroys the order's charge ledger.
```

```go
// Defined in models so that both the assignment and billing packages can
// reference it without an import cycle.
```

Both explain a **why that is invisible from the code**, and both would be
guessed wrong without the comment.

⚠️ **Contrast:** a comment asserting something the code does not do is worse
than none. A comment can also accurately describe behaviour that contradicts a
recorded decision; nothing flags the conflict, so it reads as sanctioned. When
a comment and a decision disagree, surface it.

---

## Before you commit

- [ ] Every new comment explains a **why**, a gotcha, or an invariant
- [ ] No narration, no restated names, no ticket or stage markers
- [ ] Noise you passed through was deleted
- [ ] Any comment asserting behaviour is **actually true** of the code beneath it
