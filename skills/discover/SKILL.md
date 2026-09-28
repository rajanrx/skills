---
name: discover
description: Use when someone asks you to explain, walk through, or make sense of a file, subsystem, config, concept or block of terminal output — especially when they have already read it themselves, when a previous explanation was too long, or when they say they are lost, confused, or want to understand rather than be summarised.
version: 1.0.0
---

# Discover

Teach the thing, one layer at a time, letting the reader steer.

## Overview

Someone asking for this has already tried reading it. Handing them a summary is handing them a second thing to read. The job is understanding, and understanding is built in rounds, not delivered in one.

Write everything here using the `simple-writer` skill. If it is not available, its core still applies: one idea per sentence, name the actor, lead with the answer, define any word the reader has not been given.

## Round 1: the map

Four parts, in this order, 200 words maximum before the menu.

1. **What it is.** One sentence. No jargon. If someone asked "what is this file?" in a corridor, this is the answer.
2. **Why it exists.** The problem it solves, in one or two sentences. Not what it contains, what it is for.
3. **The pieces.** Three to six bullets. One line each. The reader should finish this list knowing the shape of the thing, and nothing more.
4. **The menu.** Numbered, five options maximum. Each one a plain-language question, not a section title.

The menu is required. Round 1 always ends by handing control back.

> Where do you want to go?
>
> 1. Why they picked the hard approach over the obvious one
> 2. What actually happens when you run it
> 3. The bit most people get wrong
> 4. Something else, tell me what snagged

*Where do you want to go?* beats *Let me know if you have questions.* The first offers doors. The second offers homework.

## Round 2 onwards: the drill

Three parts, in this order.

1. **Answer what they picked.** Just that. Do not revisit the map.
2. **The one surprise.** The thing about this piece that is not obvious from its name. One per round.
3. **The menu again.** Deeper here, back up a level, or on to another piece.

Stay at 200 words unless they ask for more. They will ask if they want it.

## Ask before assuming

When the right explanation depends on what they already know, ask, and wait.

> Before I explain the grammar file: have you worked with schema or config-driven parsing before, or should I start from why anyone would use a config file for this?

One question. Not a quiz. Only when the answer would genuinely change what you write.

## Terminal output

The same four parts:

| Part | For terminal output |
|---|---|
| What it is | What the command was trying to do |
| Why it exists | Did it work, yes or no, in the first line |
| The pieces | Group the noise. "Eleven warnings, three real, eight environment" |
| The menu | Which group to open |

Never walk through errors one by one before asking. Group them, count them, say which matter, then let them pick.

## Check before sending

| Ask | If no |
|---|---|
| Does it end with a numbered menu? | Add one. Round 1 is not finished without it. |
| Is it under 200 words before the menu? | Cut. The extra insight goes in a later round. |
| Would the first sentence make sense to someone who has not opened the file? | Rewrite it. |
| Is every term one they have been given? | Define it inline or use a plainer word. |
| Am I dumping everything I found because I found it? | That is for you, not them. Menu it. |

## Common mistakes

**Replacing a wall of text with a friendlier wall of text.** The most common failure by far. The explanation is accurate, well organised, genuinely insightful, and 1,500 words long. The reader is no better off. Length is the thing they complained about.

**Front-loading your critique.** You will spot flaws while reading. They are valuable and they are not round 1. Offer them on the menu: "I think two parts of this are wrong, want those?"

**Answering a question nobody asked.** Explaining all eleven warnings when they only needed to know the build is fine.

**Defining jargon with jargon.** "An oracle is the ground truth for validation" helps nobody. "An oracle is whatever tells you the answer was right" does.

**Skipping the menu when the thing seems simple.** Simple to you. They asked because it was not simple to them.
