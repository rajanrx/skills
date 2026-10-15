---
name: tdd
description: "Write tests before production code, in TWO passes — a generator pass that writes them and a separate critic pass that tries to break them with mutations. Use when starting any change with testable behaviour, when asked to \"write tests first\", or after a review finding that needs a test to close it. The critic pass is the point: tests that pass with the fix reverted are common and invisible."
version: 1.0.0
---

# TDD — generator, then critic

Two passes, **separated on purpose**. The same reasoning that writes a test is
the worst judge of whether it can fail.

> ⚠️ **Tests that pass with the fix reverted are common.** So are negative
> assertions that pass because no request fired at all, and integrations
> reported "working" several times while broken against the live API.
> **The critic pass exists because of those, not as ceremony.**

---

## Pass 1 — Generator

Write the tests. Nothing else. **No production code yet.**

- One behaviour per test, named for the behaviour: `TestCancelOrder_RecordsAuditEvent`
- **Happy path + every branch + negatives + edges** (zero, empty, expired, boundary)
- **File naming mirrors the source:** `addon.go` → `addon_test.go`. Never
  `coverage_boost_test.go`. One source file, one test file.
- Assert on **observable outcome**, not on internal calls. A test that asserts a
  mock was called passes when the mock is wrong.

Run them. **They must fail**, and fail for the *stated* reason. A test that
passes before the code exists is testing nothing.

---

## Pass 2 — Critic

**A separate pass.** Do not fold this into writing the tests.

For each test, ask:

1. **Mutate the production code to break the behaviour. Does the test fail?**
   If not, it does not test what its name claims. This is the whole pass.
2. **Would it pass if the feature were deleted?** Negative assertions are the
   usual culprit — asserting "no error" when no call was made at all.
3. **Does the stub mirror the REAL call ordering?** A stub that fires only the
   final callback makes a no-op fix look verified.
4. **Does it assert attribution, or only a total?** `Σ == total` proves the sum
   and **never** the attribution. A charge on the wrong entity reconciles
   perfectly.
5. **Is the fixture doing the work?** If the test only passes with one crafted
   input, it tests the fixture.
6. **Can the fixture even EXPRESS the state you are trying to catch?** A fixture
   built by reading the implementation offers exactly the fields the code
   consumes — so the state it ignores is unreachable from the test, and no
   mutation can reveal it. ⚠️ This is invisible from a green run AND from a
   caught-mutation count: dozens of passing tests and several caught mutations
   can sit on top of a check that reads one status field and never the other,
   because the fixture had no way to set the other. Build the fixture from the
   CONTRACT — the API's real shape — not from the fields your code happens to read.

### Discipline

**Cap mutations at 3–5 discriminating ones.** A 13-mutation battery is method
failure, not thoroughness. **A mutation whose result you can predict teaches
nothing** — pick the ones you are unsure about.

**Never run the full suite per mutation.** Narrow to the changed package while
iterating; one full run before the final push.

**A mutation is only evidence if the interpreter actually ran it.** Python
invalidates `__pycache__` on **(mtime, size)** — so a mutation that swaps one
identifier for another of the SAME LENGTH leaves the file identical in size,
and a restore within the same second reuses the *mutated* bytecode against
restored source. The run reports a pass and the mutation looks caught. Example:
`ready < updated` → `ready < desired` (both seven characters) scored as caught
when the check was not tested at all. Run every Python mutation as `python3 -B`
with `__pycache__` cleared, and script the loop rather than hand-editing — the
hand loop is where the same-second restore happens. Go and TypeScript toolchains
hash content, so they do not have this failure.

---

## What not to test here

- **End-to-end flows** that need a live environment belong in the suite that
  has one. Unit suites that need a live database and API fail at setup in CI.
- **Paths your test driver cannot exercise** (for example recovery behaviour an
  in-memory database does not reproduce). **Say so in the test file** rather
  than writing a test that appears to cover it.

---

## Before you finish

- [ ] Every test failed first, for its stated reason
- [ ] 3–5 discriminating mutations run; each failed the right test
- [ ] No test passes with the feature removed
- [ ] Stubs mirror real call ordering
- [ ] Attribution asserted, not just totals
- [ ] Untestable paths **named**, not silently uncovered
