# Review the tests of a change that already happened

For a diff, a commit or a pull request whose code and tests exist. The output is the review;
write no tests unless the user asks afterwards. Copy the checklist:

```
Review progress:
- [ ] 1. Test basis agreed with the user (core 1), gaps answered with examples
- [ ] 2. Baseline: the suite run, with branch coverage on
- [ ] 3. Risks listed and ranked, regressions included (core 2)
- [ ] 4. Each risk placed at a level and a type (core 3)
- [ ] 5. Each row mapped to the existing tests that cover it
- [ ] 6. Probes for the gaps the table shows
- [ ] 7. White-box: changed branches reached
- [ ] 8. Verdict on the exit criteria; residual risk left to the user (core 5)
```

**1. Test basis.** Core 1, for the behavior the change touches. A commit message or a pull
request description is a source; so are the docs and rules the change was meant to satisfy.
The checkpoint applies in full: in a review, the gap you do not ask about is the one the
change got wrong.

**2. Baseline.** Run the suite once with branch coverage on (`coverage run --branch`,
`pytest --cov-branch`, …). It answers two questions: does the suite pass (if not, report
that first), and which changed lines and branches does it reach (step 7).

**3–4. Risks, levels, types.** Core 2 and 3. Read the diff for what changed and for what
sits next to it and must keep working: the same function's other inputs, the same format's
other cases, the same callers.

**5. Map the rows.** For each row, the existing test that covers it, by name, and whether it
really does: a test of the right behavior at the wrong level, or with mocks where the risk
needs real collaborators, does not count. Mark each row ✅ covered, ❌ not covered, or ⚠️
evidence only (a model's judgment, a manual run).

**6. Probe the gaps.** For a ❌ row whose behavior you can exercise cheaply, run it now on a
few inputs from the test basis: a one-off script or an interactive call, not a committed
test. A probe turns "untested" into "untested and broken" or "untested and fine", and that
decides the risk. Report what each probe showed.

**7. White-box.** From the baseline coverage, the changed branches reached and the changed
lines never run. An unreachable branch is usually dead code.

**8. Verdict.** The table, ordered by risk:

| Risk | What can break | Level | Type | Covered by | Expected result from |
|---|---|---|---|---|---|
| high | … | component | functional, black-box | ✅ `test_…` | docs/x.md |
| high | … | component | functional (regression) | ❌ broken, probe shows … | previous behavior, confirmed |
| | **Not covered** | | | … | **why** |

Then the exit criteria (default: every high-risk row covered and passing, every changed
branch reached, every question answered), each met or not. Unmet ones are residual risk:
list them, propose the fixes, and let the user decide.
