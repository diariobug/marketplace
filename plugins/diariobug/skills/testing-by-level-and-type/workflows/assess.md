# Assess how an existing test suite covers a module or feature

For "how well do our tests cover X?" with no particular change in view. The output is a
coverage matrix and the gaps ranked by risk; write no tests. Copy the checklist:

```
Assessment progress:
- [ ] 1. Scope agreed with the user
- [ ] 2. Test basis agreed with the user (core 1), gaps answered with examples
- [ ] 3. Risks listed and ranked (core 2)
- [ ] 4. Every existing test in scope classified by level and type
- [ ] 5. Branch coverage of the scope measured
- [ ] 6. Matrix built: risks × levels, cells = tests and their types
- [ ] 7. Gaps ranked; findings reported; residual risk left to the user (core 5)
```

**1. Scope.** A module, a feature or a flow, small enough to read its code and its tests:
for a whole repository, assess its riskiest areas one at a time. Confirm the scope with the
user before reading everything.

**2–3. Test basis and risks.** Core 1 and 2, for the scope as it is today. The risks come
from what the scope does and what it depends on, not from the tests: start from the tests and
you only find what they already look at.

**4. Classify every test by what it does, not by its name or folder.**
- **Level**, from what it touches: everything replaced by doubles → component; real
  collaborators inside the system → component integration; the system's own entry point
  → system; a real or simulated external service → system integration.
- **Type**, from where its expected result comes from: the test basis → functional or
  non-functional black-box (which characteristic: time, security, reliability…);
  covering a branch or path → white-box; the value the code returns today → **suspect**.
- **Suspect** too: a "unit" test that opens a database or the network; a test whose only
  assertion is that a mock was called; a test that cannot fail (asserts nothing, or asserts
  what it just set up).

**5. Branch coverage** of the scope (`coverage run --branch`, `pytest --cov-branch`, …),
read per file and per function. List the branches no test reaches; an unreachable one is
usually dead code. For each behavior the test basis promises, check that the code can
actually produce it, not only that a test exists: a documented check behind a branch that
can never be taken is a defect the docs hide, and no test will fail for it.

**6. The matrix.** Risks ordered by level of risk as rows, the five levels as columns; in
each cell the tests that cover that risk at that level, with their type. Mark ⚠️ where the
only evidence is a manual or recorded run (a model's judgment, a real external service).

| Risk | Component | Comp. integration | System | Sys. integration | Acceptance |
|---|---|---|---|---|---|
| high: … | ✅ `test_a` (functional) | ❌ | — | ⚠️ recorded run | the users |
| medium: … | — | ✅ `test_b` (non-functional: reliability) | — | — | — |

"—" means the level cannot see this risk or is not needed for it; ❌ means it should be
covered there and is not.

**7. Findings,** ranked by risk:
- High risks with ❌ at the earliest level that can see them.
- Risks covered only end to end: slow, and a failure does not say which piece broke.
- The same logic tested again at several levels.
- Non-functional risks with no test at all.
- Suspect tests (step 4), each with why.
- Uncovered and dead branches (step 5).

End with "not covered, and why" and the residual risk for the user to accept or act on.
For any gap the user wants closed, the plan workflow is the next step.
