---
name: simple-writer
description: Use when writing or rewriting anything a person will read — a terminal reply, summary, report, review, or markdown document — especially after research or a long task, or when existing text is confusing, repetitive, padded, or over-formatted.
version: 1.0.0
---

# Simple Writer

## Overview

Write so a busy reader gets everything on one pass. **Simple means each point said once, in plain words, in the order the reader needs it. It never means less content.** Every fact in the source reaches the reader, and nothing outside the source does.

## The shape

Every output has these parts, in this order. Skip a part only when the source has nothing for it.

1. Answer: one or two sentences with the conclusion, decision, or result.
2. Reasons: one paragraph per reason, most important first. Each opens with a plain sentence stating the point, then gives its facts. A caveat sits in the same paragraph as the fact it qualifies.
3. Action: what the reader does next, beyond the Answer. A markdown file uses the alert block; a terminal reply uses the diff block (see Destinations). One line per step. The why is already in Reasons.
4. Limits: open questions that no Reasons paragraph already qualifies.

Each point lives in exactly one part. When a later part needs an earlier point, it refers to it in a phrase ("because of the slot limit above").

The part names are for you, not the reader. A heading says what the reader finds there ("Your board can't run it"), not "Reasons".

## Keep every fact

1. Before writing, list every fact in the source: numbers, names, versions, file paths, dates, quotes, caveats, sources.
2. After writing, tick each one off against the draft. The draft states it in the source's terms: the same number, the same unit, the same thing it applies to. A missing or changed fact goes back in as the source had it.
3. Then read the draft one sentence at a time. Each sentence traces to a listed fact. A sentence that doesn't (a comparison, a guess about the reader, an option the source never raised) comes out.
4. Count the bold phrases. There is at most one: the answer sentence. A fixed shape that sets its own markers overrides this — see "When another format already applies".

## Form

- Sentences: short, one idea each, plain words. Keep a technical term when it is the concept the reader needs; say what it means the first time.
- Spelling: British English, such as colour, organise, behaviour, centre, and licence as a noun. Code, commands, and quoted text keep their original spelling. (Swap this line for your house style if you prefer another.)
- Paragraphs: one to four sentences.
- Tables: when every row has the same fields.
- Lists: for steps, or for three or more parallel items.
- Bold: the answer sentence only. The action block's format marks it, so its label takes no bold.

## Destinations

| | Markdown file (GitHub, GitLab 17.10+) | Terminal reply (e.g. Claude Code) |
|---|---|---|
| Action block | `> [!IMPORTANT]` headed "Your next steps", then `> - [ ]` task items inside the quote | A `diff` code block: `@@ Your next steps @@`, then one `+ 1.` line per step, plain text only (the terminal colours `+` lines green and the `@@` line blue or cyan) |
| Headings | `##` sections, only past one screen of text | None under one screen; beyond that, one level only (h2 to h6 often render as the same bold line) |
| Links | `[label](url)` | The label in the sentence, then the bare URL (terminals often drop link labels) |
| Commands, paths, flags | Code spans | Code spans in the text; plain text inside the diff block |

Renderers without alert support show the action block as a plain quote, which still stands apart from the text.

When you write a script or CLI tool that prints steps for a person, colour them with the terminal's standard ANSI colours (green `32`, yellow `33`) so the user's theme adapts them to a light or dark background. Print plain text when output is not a TTY or `NO_COLOR` is set.

## When another format already applies

Some output already has a fixed shape, and that shape wins. This skill then governs the prose inside it.
- Governance documents (ADRs, standards, specs, RFCs): see `spec-writing`.
- Review findings that must be pasted verbatim.
- Any template set by another skill, agent file or project, such as a report table or a finding block.

## Rewriting existing text

Run the fact list on the original. Write it fresh in the shape, not sentence by sentence. Tick every fact against the new version. Report: "Same N facts, M words shorter."

## Example

Before (68 words):
> **Memory bandwidth is the hard ceiling on single-stream token generation** for a memory-bound decode workload like this. The B60's per-die bandwidth (456 GB/s) is under half the 3090's (936 GB/s), and even the newer, faster B70 (608 GB/s) measures below a 3090 in practice. There's no reason to expect a single B60 die to beat either.

After (38 words):
> Speed for one user depends on memory bandwidth. The B60 has 456 GB/s per GPU, the B70 608 GB/s, and the RTX 3090 936 GB/s. The B70 already measures slower than a 3090, so the B60 will be slower still.

Same four facts. Half the words.
