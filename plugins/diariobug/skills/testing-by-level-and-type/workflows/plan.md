# Plan and write the tests for a change

For a change you are about to write, or have just written and must test. Copy the checklist
and check it off as you go:

```
Plan progress:
- [ ] 1. Test basis agreed with the user (core 1), gaps answered with examples
- [ ] 2. Entry criteria checked
- [ ] 3. Risks listed and ranked, regressions included (core 2)
- [ ] 4. Each risk placed at a level and a type (core 3)
- [ ] 5. Plan shown, with exit criteria
- [ ] 6. Tests written, highest risk first (test-first if the code is not written yet)
- [ ] 7. White-box pass on the changed branches
- [ ] 8. Exit criteria checked; residual risk left to the user (core 5)
```

**1. Test basis.** Core 1. The examples the user confirms are the first test cases: in
acceptance test-driven development the test cases "can be seen as examples of how the
software works", written by people with different perspectives before the code (§2.1.3,
§4.5.3). Examples you write after the code describe the code, not an agreement.

**2. Entry criteria** (§5.1.3: unmet, the work "will prove to be more difficult,
time-consuming, costly, and riskier"):
- The existing suite passes before you start. If it is red, report that first: its failures
  would read as failures of the change.
- The environments the plan needs exist (test database, provider sandbox, fixtures). A row
  whose environment is missing goes to "not covered"; it is not replaced by a mock that
  tests nothing.

**3–4. Risks, levels, types.** Core 2 and 3. Read the project's existing tests first for its
conventions: framework, folders, markers for integration and end-to-end tests, fixtures, how
external services are faked, how coverage is measured.

**5. The plan,** before any test, ordered by risk:

| Risk | What can break | Level | Type | Test | Expected result from |
|---|---|---|---|---|---|
| high | … | component | functional, black-box | … | issue #12, criterion 2 |
| high | … | component | functional (regression) | … | previous behavior, confirmed by the user |
| medium | … | system integration | non-functional (reliability) | … | the API's documented timeout |
| | **Not covered** | | | | **why** |

**Expected results from a reader who has not seen the code.** Whoever has read the code
tends to expect what it does. If you can start a subagent with a fresh context, give it only
the agreed test basis, the confirmed examples and the plan's test cases (never the code, the
diff or this conversation) and ask it for each case's expected result, citing its source.
Those answers are the expected values. Where its answer differs from yours, either the code
has a defect or the test basis has a gap: ask the user with that case as the example. Without
subagents, write each expected value down, with its source, before you read the code that
computes it.

Below the plan, the exit criteria for this change. Default: every high-risk row covered and
passing; every changed branch reached, or listed as dead code or not covered with its
reason; every question about the test basis answered.

**6. Write the tests,** highest risk first, in the project's conventions. When you also
write the code, write the tests first, in two loops:
- **Outer, at system or acceptance level:** each confirmed example becomes an executable
  test (a Given/When/Then the user can read, if the project uses that style), seen failing
  before the code exists.
- **Inner, at component level:** test-driven development, "Tests are written first, then the
  code is written to satisfy the tests, and then the tests and code are refactored" (§2.1.3), in small steps, each test seen failing first.

When the change is a bug fix, the first test is a confirmation test that reproduces the
failure against the unfixed code; regression tests around the fix follow (§2.2.3).

Per level:
- Component: isolated, stubs for what it calls; black-box cases from the test basis
  (equivalence partitions, boundaries); white-box cases for the branches the change added.
- Component integration: real collaborators inside the system, checking what crosses the
  interface.
- System: through the system's own entry point (API, CLI, UI), in the project's
  production-like setup.
- System integration: the provider's sandbox, a contract double or a fault-injecting fake,
  including the failures.
- Acceptance: "ideally … performed by the intended users" (§2.2.1). Turn the acceptance
  criteria into executable checks they can read and confirm; the sign-off is theirs.

**7. White-box pass.** List the decisions the change added or modified (each `if`, loop
condition, early return, exception handler, boolean expression) with both outcomes. Run the
suite with branch coverage (`coverage run --branch`, `pytest --cov-branch`, `go test
-cover`, `jest --coverage`…) and read it for the changed lines. For each outcome no test
reaches, write a test that asserts the specified behavior on that path; if the test basis
does not say what that is, ask (core 1). An outcome no test can reach is usually dead code:
propose deleting it.

**8. Exit.** Go through the exit criteria one by one. Report what each level covers, the
changed branches reached, and the "not covered" row. Anything unmet goes to the user as
residual risk (core 5).
