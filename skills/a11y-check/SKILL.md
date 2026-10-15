---
name: a11y-check
description: Fix an accessibility violation flagged by eslint-plugin-jsx-a11y (or an equivalent static a11y lint), or check a component before it is flagged. Use when lint reports a jsx-a11y rule, when a component explorer's a11y panel shows a violation, or when building an interactive component. Covers the fix for each rule class, how to ratchet rules from warning to error, and — more importantly — what the gate does not catch.
version: 1.0.0
---

# a11y check

Fix what the lint flags. Find where the project configures its a11y rules and
their status, and its written accessibility standard if it has one.

> **Look at a neighbouring component first.** In an established codebase most
> components already handle this by hand — you are almost certainly maintaining
> an existing convention, not inventing one.

---

## ⛔ What this gate does NOT catch

Read this before trusting a green lint.

- **keyboard order** — tab sequence, and whether it matches visual order
- **focus traps** — a modal you can tab out of behind
- **focus restoration** — where focus goes when a dialog closes
- **anything requiring interaction**

🔶 **Real barriers usually live in exactly those.** A clean lint means "no static
violations", never "accessible". Do not cite the gate as though it means more.

---

## Fixes by rule class

### Content that is invisibly missing

`alt-text` · `anchor-has-content` · `heading-has-content`

```tsx
<img src={p.photo} alt={`${p.name}, profile photo`} />    // meaningful
<img src={divider} alt="" />                              // decorative — empty, not absent
```
⚠️ **`alt=""` and no `alt` are different.** Empty says "decorative, skip me";
absent makes a screen reader read the filename.

### ARIA that is wrong

`aria-props` · `aria-proptypes` · `aria-unsupported-elements` ·
`role-has-required-aria-props` · `role-supports-aria-props`

Usually a typo — `aria-labeledby` for `aria-labelledby`. 🔶 **Wrong ARIA is worse
than none**: it silently does nothing while looking handled. These are cheap to
fix, so make them blocking first; once they are, a new hit is a new mistake.

### Controls that cannot be operated or announced

`label-has-associated-control` — a placeholder is **not** a label; it disappears
on input.

```tsx
<label htmlFor="email">Email</label><input id="email" />
<label>Email<input /></label>                              {/* wrapping also works */}
```

`no-autofocus` — usually right to obey. It steals focus from someone navigating
by keyboard. **Legitimate exception:** a single-purpose step such as an OTP
field, where focus *is* the interaction. Disable **with the reason**.

`tabindex-no-positive` — never a positive `tabIndex`; it hijacks global order.
`0` and `-1` are fine.

### Interactive semantics — usually the biggest backlog

`no-static-element-interactions` · `click-events-have-key-events` ·
`interactive-supports-focus` · `no-noninteractive-element-interactions`

**A `<div onClick>` is not a button.** It cannot be tabbed to, cannot be
activated by Enter or Space, and is announced as nothing.

```tsx
<div onClick={submit}>Save</div>                            // ⛔
<button type="button" onClick={submit}>Save</button>        // ✅ the real fix
```

🔶 **Reach for the native element first.** `role` + `tabIndex` + key handlers is
a reimplementation of `<button>` that will be subtly wrong. Only do it when the
element genuinely cannot be a button:

```tsx
<div role="button" tabIndex={0} onClick={go}
     onKeyDown={(e) => (e.key === 'Enter' || e.key === ' ') && go()}>
```

---

## Exceptions

Per-line, **with a reason on the same line**. A bare disable is itself a
violation.

```tsx
// eslint-disable-next-line jsx-a11y/no-autofocus -- OTP step; focus is the interaction
<input autoFocus />
```

---

## Promoting a rule (the ratchet)

1. Run the a11y rules in a measure-only mode — reports everything, fails nothing
2. A rule at **zero** violations across the whole codebase is promoted to
   `error` in the lint config
3. Verify every package or app still lints clean
4. ⛔ **Per rule, never in a batch** — one stubborn rule must not hold the rest
   hostage, and a batch promotion cannot be attributed when it breaks

Keep the backlog and current per-rule counts in one tracked issue, not in the
config comments.
