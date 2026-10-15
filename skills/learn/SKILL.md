---
name: learn
description: Turn a correction, a repeated instruction, a mistake-and-fix or a proven technique from the current session into a durable improvement — a skill, an agent instructions file (CLAUDE.md, AGENTS.md or similar), a memory note, or a project record such as a decision record, documented rule or backlog item — after the human approves the exact diff. Use when the user says "remember this", "learn from this" or "/learn"; when a session is ending and something in it would help the next one; or when the same correction has already been given more than once.
version: 1.0.0
---

# Learn

*Every correction should only need to be given once.* This skill is the mechanism:
find the signal, pick the smallest durable home, **show the human the exact diff,
and write nothing until they approve it.**

**Two kinds of lesson, two kinds of home.** A lesson about *how agents should work*
goes to agent surface: skills, agent instructions files, agent definitions, memory.
A lesson about *what is true of the project or system* goes to the project's own
records: a decision record, a documented rule, a known-debt list, a backlog item,
a glossary. Sweep for both every time; most sessions produce one of each.

## When to use

- The user says "remember this", "learn from this", "add this as a rule", or
  invokes `/learn`.
- A correction just landed that has clearly been given before. Check memory and
  the instructions files first: if it is already written down and was simply not
  read, the fix is "read it next time", not a second copy.
- A session is wrapping up and something in it (a gotcha, a working technique, a
  wrong assumption that cost time) is not captured anywhere durable.
- A skill was followed and turned out to have a gap, a stale instruction, or a
  step nobody could complete as written.

**Don't use it for:** facts that only matter to the current task, a behaviour
change that still needs building (record the *decision*, hand the feature to the
project's normal change process), or a one-off fact no future session would use.

## Invocation

- `/learn` with no argument: scan the conversation for learnable signal and
  propose what to capture.
- `/learn <statement>`: the human is handing you the lesson directly (for example
  `/learn always use the money input component, never a raw number field`). Skip
  signal-finding and go straight to placing it.

## Step 1 — Find the signal

| Signal | Example |
|---|---|
| **Correction** | the human said an approach, assumption or output was wrong |
| **Repeated instruction** | the same thing had to be said more than once, this session or across sessions |
| **Mistake + fix** | something broke or was wasted (time, a wrong file touched, a bad assumption) and you now know why |
| **Proven technique** | a non-obvious approach worked and would save time next time |
| **Confirmed approach** | the human explicitly signed off on a way of working that wasn't written down |

**Then sweep again for project signal.** ⚠️ These are the ones that get lost: they
arrive as an aside ("oh, nothing actually writes that column"), the session fixes
the immediate problem, and the fact never reaches the project's records.

| Project signal | Looks like | Typical home |
|---|---|---|
| **Undocumented decision** | two ways of doing one thing coexist; a choice was made in code with no record; "why X?" was answered only out loud | a decision record (ADR) or a candidate list of decisions to write up |
| **Product or domain rule** | a human stated how the system must behave in a way the docs don't say, or the docs contradict the code | the project's spec or rules document |
| **Enforceable engineering rule** | "always X, never Y" that could be checked mechanically and keeps being broken | a lint rule, test or written standard with its check |
| **Legacy path or band-aid** | a fallback kept "for now"; a second implementation of a settled thing | a known-debt list, with what blocks its removal |
| **Out-of-scope defect or idea** | found mid-task, not this task's, nowhere to put it | an issue or backlog item |
| **Vocabulary drift** | one concept under two names, or one name for two concepts | the glossary |

Discard: facts the repo already records (code structure, past fixes visible in
version control), anything that only matters to this conversation, and anything
already covered by an existing memory, skill, instruction or record.

## Step 2 — Look for an existing home before drafting

A duplicate that drifts from the original is worse than the original alone. Search:

- the memory index and topic notes, for an entry to **update** rather than duplicate;
- the skill list and the target skill, for a section this already belongs under;
- the instructions files (root and subdirectory), for a rule already stated but
  not followed;
- for project signal, the project's records: existing decisions, rules, debt
  items, open issues. ⚠️ A document's **dated correction** beats its headline
  status line, so read to the bottom before concluding nothing covers it.

If the existing entry is **stale or wrong** rather than missing, don't fix it in
place yet; that is still a write. Fold the correction into the Step 4 draft so it
gets the same approval. One draft replaces the old entry; it never sits beside it.

## Step 3 — Pick the smallest correct scope

| Scope | Destination | Reaches |
|---|---|---|
| A rule for one directory's conventions | that directory's instructions file | agents working in that subtree |
| A cross-cutting rule for the whole repo | the root instructions file — **and its mirror**, if the repo keeps one per agent (for example `CLAUDE.md` and `AGENTS.md`) | every agent in the repo |
| A reusable technique, checklist or process | an existing skill (edit) or a new skill in the repo's skills directory | every agent that loads skills |
| How one agent role should behave | that agent's definition file | that role only |
| A fact, preference or incident worth recalling, not a rule anyone must follow | the agent's memory store, if it has one | that agent's future sessions only; say so if the acting agent has no memory store, and fall back to the nearest rule-shaped home |
| Anything personal to the user across all projects | the user-level instructions file or memory | that user's sessions everywhere |
| A decision, rule, debt item, defect or term about the system | the project record from Step 1, written through whatever process or skill owns that record type | every reader of the project |

Ask, in order:

1. *Is this about how agents work, or about what is true of the system?* A system
   truth written into an instructions file or memory is a fact the project never
   learns.
2. *Must it bind, or is it worth recalling?* Rules go to instructions and skills;
   facts with no obligation go to memory.
3. *One place, or everywhere?* Picks subdirectory versus root versus user level.
4. *Will a future agent need to DO something differently, or just KNOW something?*
   Technique versus fact.

⚠️ **Pick one destination, not several.** The same lesson in memory, a skill and
an instructions file is three copies that will drift.

## Step 4 — Draft. Do not write yet.

Produce the literal change:

- **Editing a file:** the exact before and after text, not a description of it.
- **A new skill:** the complete `SKILL.md`, named, in the repo's skills directory,
  following the conventions of the skills already there.
- **A new memory note:** the complete note in the store's format, plus its index
  line if the store has an index.
- **A project record:** use the skill, template or process that owns that record
  type and present *its* output as the draft. Draft it as proposed, never as
  accepted; `learn` routes, it does not decide. A counted claim ("three callers",
  "no writer since June") carries the command or query that produced it.
- **Editing an existing skill or instructions file:** reread it for anything the
  new text makes redundant or contradicts, and draft that removal in the same
  diff. Scope this strictly to what the new text touches.

Several learnings? Draft them together, one round of review, each as its own
labelled diff so the human can approve some and reject others.

## Step 5 — ⛔ Get explicit approval before touching a single file

**This is the rule the whole skill exists to enforce.**

Show every draft from Step 4 in full, then ask plainly: *"Apply this?"* Wait for an
explicit answer. "Yes", "approved", "go ahead", "apply 1 and 3" count. Silence, a
topic change or moving on does **not** count; it means don't write.

| Rationalization | Reality |
|---|---|
| "This is clearly correct" | Correct-to-you isn't the bar. Show it. |
| "It's only a memory note" | A memory note loads into every future session as settled fact. A wrong one compounds silently. |
| "They invoked /learn, that's the go-ahead" | That approved the search, not the diff it produced. |
| "I already described what I'll change" | A description isn't the literal text, and telling isn't waiting. |
| "There's obviously one place this goes" | The destination is part of the approval. Name it and let them confirm. |
| "It's just fixing a stale entry" | A correction is still a write. It goes in the draft. |

**Red flag:** you are about to write or edit a skill, instructions file, agent
definition, memory note or project record, and the human's last message was not an
explicit yes to a diff you showed. Stop.

## Step 6 — Apply and verify

1. Write exactly the approved diff, with no drive-by changes.
2. Run whatever the repo uses to validate what you touched: a skill linter or sync
   step, a docs or spec index, tests for a new lint rule.
3. Memory: update the index line in the same pass, and delete a superseded note
   rather than keeping both.
4. A mirrored root rule: confirm every mirror was actually edited.
5. Commit per the repo's own rules (branch, review, who merges). Keep project
   records in their own commit, separate from agent-instruction changes, so each
   diff can be reviewed alone.
6. Say plainly what was written and where: the file path, not just "done".

## Quick reference

```
agent signal   ─┐
                ├→ look for an existing home → pick the smallest scope → draft the literal diff
project signal ─┘
  → SHOW IT, ask "apply this?" → wait for an explicit yes → write → validate → commit
```

## Common mistakes

- **Applying the fix and mentioning it afterwards.** The order is show, wait, write.
- **Writing a rule into memory.** If a future agent must *do* something
  differently, it belongs in a skill or instructions file, not a fact nobody is
  obliged to act on.
- **Duplicating instead of correcting.** A second, slightly different copy of a
  rule is how contradictions stack up.
- **Forgetting the mirror.** In a repo with one instructions file per agent, a
  root rule added to one and not the other reaches only half the agents.
- **Writing a system truth into agent memory.** "Nothing writes that column" is a
  debt item or a decision correction. In memory it reaches one agent; in the
  project's records it reaches everyone.
- **Only sweeping for agent signal.** If the project sweep came back empty, say so
  rather than skipping it silently.
- **Recording a decision as accepted.** `learn` proposes; the project's owners
  decide.
