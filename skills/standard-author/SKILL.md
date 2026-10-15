---
name: standard-author
description: Write an enforceable standard, meaning the rule plus the thing that mechanically checks it, in the project's standards folder. Use when a rule needs to bind rather than advise, when promoting prose from an instructions file such as CLAUDE.md or AGENTS.md into something enforced, or when an architecture decision record needs the standard that carries it. Covers the rollout ladder that stops a new standard breaking every build on day one.
version: 1.0.0
---

# Standard author

A standard is **a rule with an enforcer**. No enforcer, no standard: that is a
skill.

## When not to use

- Do not create a standard for guidance with no mechanical enforcer; write a skill.
- Do not use it for a business invariant; that belongs in the project's domain rules.
- Do not use it to record why a durable choice was made; write or link an ADR
  (use `adr-author` if installed).
- Do not invent an enforcer merely to justify a document.

---

## ⛔ The test

**Name the enforcer before you write the rule.** If you cannot, stop: you are
writing a skill.

| Enforcer | Example |
|---|---|
| a database constraint | `CHECK (amount <> 0)` |
| a linter / static check | an ESLint rule, `go vet`, a custom AST check |
| a git hook | a pre-commit hook |
| a CI job | a validator script |
| a schema validation | JSON Schema on a config or spec file |

⚠️ **"Code review will catch it" is not an enforcer.** It is the thing standards
exist to stop relying on.

---

## Shape

```markdown
---
id: std-<name>
status: proposed          # proposed → active
version: 0.1.0
date: YYYY-MM-DD
owner: @role
adrs: [<adr-id>]          # the DECISION this standard carries
applies_to:               # what it binds — be specific
  - "services/api/**/*.go"
enforced_by:
  - "ci:validate-x.py"    # the ACTUAL enforcer, named
rung: warn                # see the ladder below
---

# Standard: <the subject as a sentence>

## Rule (MUST / MUST NOT)   numbered rules, unambiguous
## Rationale                the failure it prevents, concretely
## Applies to               scope, and explicit carve-outs
## Examples                 compliant / non-compliant pairs
## Enforcement              WHAT checks it, WHERE it runs, and what it does on failure
## Rollout                  which rung, and what moves it to the next
## Exceptions               how to get one, and who grants it
```

---

## 🔶 The rollout ladder: the part that makes standards adoptable

A standard switched straight to blocking breaks every build and gets reverted.
Four rungs, and **a standard declares which one it is on**:

| Rung | Behaviour | Use when |
|---|---|---|
| `warn` | reports, never fails | brand new; measuring the true size of the problem |
| `diff-targeted` | fails only on **lines the change touched** | the backlog is large but new code should comply |
| `burndown` | the tracked debt: an **advisory count that only goes down** | a finite, shrinking list |
| `blocker` | fails on any violation | the backlog is zero |

🔶 **`burndown` is the debt a `warn`-tier rule is carrying**, not a rung of its
own, and **the flip to `blocker` is an acceptance criterion someone owns**, not
something that happens when convenient.

⚠️ **Declare the rung in frontmatter and say what promotes it.** A standard that
never leaves `warn` is decoration; one that starts at `blocker` gets turned off.

🔶 **`diff-targeted` is the workhorse.** It stops the bleeding without requiring
anyone to fix history first, which is what usually kills adoption.

---

## Standards vs the alternatives

- **vs ADR**: the ADR is the *decision*, the standard is the *enforced rule*.
  A standard should name its ADR in `adrs:`; a standard with no decision behind
  it is a rule nobody agreed to.
- **vs domain rule**: a domain rule is a business invariant that survives a
  rewrite. A standard is technical and stack-specific.
- **vs skill**: no enforcer → skill.
- **vs architecture document**: that records what the system *is*; a standard
  says what code *must* do (use `architecture-author` if installed).

---

## ⛔ Writing the rules: NUMBERED, not singular

⚠️ "One rule per standard" is wrong. A real standard carries a
**`## Rule (MUST / MUST NOT)`** section with **numbered rules**; seven or more is
normal for something like multi-tenancy.

**The numbering is an addressing scheme, not formatting.** Rules get cited from
code, from review, and from the exemption markers themselves:

```
-- tenant-exempt: platform-global lookup table [multi-tenancy standard, rule 1]
```

That citation is only possible because rule 1 has a stable number. **So numbers
are append-only**: never renumber, or you break every citation.

**One SUBJECT per standard**, many rules within it. Tenancy is one subject; its
seven rules are all tenancy.

**Write each so a machine could check it.** If a rule needs human judgement, it
cannot have an enforcer, which means it is not a rule in a standard.

### 🔶 Default-deny, with reason-bearing opt-outs

The pattern worth copying wholesale:

> *"Tenant scope is **default-deny**: the validator treats every table as
> tenant-scoped unless it opts out explicitly, either via an allowlist or a
> reason-bearing marker immediately above the definition. **Both opt-outs are
> counted by the gate** and reviewed by code owners; **a marker with no reason is
> rejected.**"*

Three things make that work, and all three are needed:

1. **Default-deny**: new things are covered automatically. An allowlist rots.
2. **The opt-out carries a reason and a rule citation**: a bare disable is
   rejected outright.
3. ⛔ **The gate COUNTS the opt-outs.** Otherwise exemptions are how the standard
   quietly dies: everyone is compliant, nobody is constrained.

⚠️ **State the carve-outs.** Generated code, vendored code, test fixtures and
migrations are the usual exemptions. Name them, or the first person to hit one
disables the whole check.

---

## Before you finish

- [ ] The enforcer **exists**, or is written in the same change
- [ ] `enforced_by:` names it precisely: not "CI", but which check
- [ ] `rung:` declared, with what promotes it
- [ ] `adrs:` names the decision behind it, or explains why there is none
- [ ] Carve-outs stated
- [ ] Compliant **and** non-compliant examples
- [ ] Added to the project's standards folder, registered in whatever index or
      lint the project runs, and referenced from any domain it binds
