# rajanrx skills

Twenty agent skills for writing clearly, explaining things, recording decisions and architecture, planning and scoping changes, checking your own work before review, and turning corrections into lasting rules. They work in Claude Code as the `rajanrx` plugin, and as plain `SKILL.md` files in any agent that reads them.

## Install

In Claude Code:

```
/plugin marketplace add rajanrx/skills
/plugin install rajanrx@rajanrx-skills
```

Or with the open skills CLI:

```
npx skills add rajanrx/skills
```

Or copy any folder under `skills/` into `~/.claude/skills/` (or your agent's skills directory).

## Skills

### Writing and explaining

Clear prose for people, and teaching instead of summarising.

| Skill | Use it when |
|---|---|
| [`simple-writer`](skills/simple-writer/SKILL.md) | Writing anything a person reads. Answer first, each point once, every fact kept. |
| [`discover`](skills/discover/SKILL.md) | Someone wants to understand a file, subsystem, concept or terminal output. Teaches one layer at a time and ends each round with a menu, instead of dumping a summary. |
| [`wait-what`](skills/wait-what/SKILL.md) | Someone pushes back in a way that suggests they read something different from what you meant. Re-pitches the point instead of repeating it louder. |

### Decisions and design

Records that explain why the system is the way it is, and what it is.

| Skill | Use it when |
|---|---|
| [`spec-writing`](skills/spec-writing/SKILL.md) | Writing ADRs, RFCs, specs or design docs. Dated inline corrections, honest gaps, `[UNVERIFIED]` markers, owned open questions. |
| [`rfc-author`](skills/rfc-author/SKILL.md) | A change is big enough to need its shape agreed before anyone builds it. Writes the RFC between the problem and the change. |
| [`adr-author`](skills/adr-author/SKILL.md) | A cross-cutting technical decision is being made or discovered. Writes the decision record, or decides it isn't one. |
| [`standard-author`](skills/standard-author/SKILL.md) | A rule keeps being broken and needs to bind. Writes the standard together with the check that enforces it. |
| [`architecture-author`](skills/architecture-author/SKILL.md) | Proposals keep re-specifying things that already exist. Writes what the system is: its flows, state machines, legacy parts and the plan to retire them. |
| [`characterise`](skills/characterise/SKILL.md) | Before changing code nobody has written down. Records what it actually does, with evidence, before deciding what it should do. |
| [`c4-diagrams`](skills/c4-diagrams/SKILL.md) | Writing a design or RFC. Draws the smallest C4-style Mermaid view that answers a real review question, marking planned versus existing. |

### Planning a change

Scope the work before writing code, and keep what you learn.

| Skill | Use it when |
|---|---|
| [`context-driven`](skills/context-driven/SKILL.md) | Starting any task, and in every subagent handoff. Keeps one context folder per task so findings are read once and never rediscovered. |
| [`ticket-plan`](skills/ticket-plan/SKILL.md) | Before implementing a task. Writes the goal, non-goals, affected areas and test plan into the task's context folder. |
| [`spec-impact`](skills/spec-impact/SKILL.md) | Before a spec or plan goes for approval. Walks ten areas a change can touch (API shape, data, events, generated files, compatibility, access, money, config, deployables, rollback) so none is missed. |

### Building and reviewing

Write code and tests that hold up, and prove it before review.

| Skill | Use it when |
|---|---|
| [`tdd`](skills/tdd/SKILL.md) | Writing tests first. A generator pass writes them; a separate critic pass mutates the code to prove they can fail. |
| [`comment-discipline`](skills/comment-discipline/SKILL.md) | Writing or reviewing code comments. Comment only what the code cannot say; leave density lower than you found it. |
| [`a11y-check`](skills/a11y-check/SKILL.md) | Lint flags a jsx-a11y rule, or before a React component ships. Fixes the cause rather than silencing the rule. |
| [`author-preflight`](skills/author-preflight/SKILL.md) | About to hand a plan or branch to a reviewer. Verify every claim, trace every caller, run a ten-minute pre-mortem, give every fix its own failing case. |
| [`record-craft-review`](skills/record-craft-review/SKILL.md) | After any review. Turns findings, verdicts, evidence and what was done about each into a durable record. |
| [`gpt-delegate`](skills/gpt-delegate/SKILL.md) | A task suits a second model. Hands implementation to GPT through Codex and verifies what comes back instead of trusting it. |

### Learning

Make each correction stick.

| Skill | Use it when |
|---|---|
| [`learn`](skills/learn/SKILL.md) | A correction keeps coming back, or the user says "remember this" or `/learn`. Finds the lesson, picks the smallest durable home (skill, instructions file, memory or project record), shows the exact diff and writes nothing until it is approved. |

`discover` uses `simple-writer` when both are installed, and `simple-writer` defers to `spec-writing` for governance documents.

## Licence

MIT. See [LICENSE](LICENSE).
