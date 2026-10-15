# rajanrx skills

Eight agent skills for writing clearly, explaining things, drawing architecture, checking your own work before review, and turning corrections into lasting rules. They work in Claude Code as the `rajanrx` plugin, and as plain `SKILL.md` files in any agent that reads them.

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

| Skill | Use it when |
|---|---|
| [`discover`](skills/discover/SKILL.md) | Someone wants to understand a file, subsystem, concept or terminal output. Teaches one layer at a time and ends each round with a menu, instead of dumping a summary. |
| [`simple-writer`](skills/simple-writer/SKILL.md) | Writing anything a person reads. Answer first, each point once, every fact kept. |
| [`c4-diagrams`](skills/c4-diagrams/SKILL.md) | Writing a design or RFC. Draws the smallest C4-style Mermaid view that answers a real review question, marking planned versus existing. |
| [`tdd`](skills/tdd/SKILL.md) | Writing tests first. A generator pass writes them; a separate critic pass mutates the code to prove they can fail. |
| [`comment-discipline`](skills/comment-discipline/SKILL.md) | Writing or reviewing code comments. Comment only what the code cannot say; leave density lower than you found it. |
| [`author-preflight`](skills/author-preflight/SKILL.md) | About to hand a plan or branch to a reviewer. Verify every claim, trace every caller, run a ten-minute pre-mortem, give every fix its own failing case. |
| [`spec-writing`](skills/spec-writing/SKILL.md) | Writing ADRs, RFCs, specs or design docs. Dated inline corrections, honest gaps, `[UNVERIFIED]` markers, owned open questions. |
| [`learn`](skills/learn/SKILL.md) | A correction keeps coming back, or the user says "remember this" or `/learn`. Finds the lesson, picks the smallest durable home (skill, instructions file, memory or project record), shows the exact diff and writes nothing until it is approved. |

`discover` uses `simple-writer` when both are installed, and `simple-writer` defers to `spec-writing` for governance documents.

## Licence

MIT. See [LICENSE](LICENSE).
