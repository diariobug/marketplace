# Lens: testability

**Finds:** claims that cannot be verified as written, and designs that make verification
expensive or impossible. A requirement or design decision nobody can test is one nobody can
show is done.

**Applies to:** PRDs, designs, test plans; any artifact with acceptance criteria.

## Checklist

1. **Observable outcome:** each requirement says what someone could observe to know it holds
   (an output, a state, a message, a measured value).
2. **Expected results:** the artifact gives, or points to, where the expected results come from
   (the test basis). A rule with no stated expected outcome leaves the tester to guess.
3. **Measurable quality targets:** performance, availability, usability claims have numbers and
   conditions ("p95 under 300 ms at 200 requests per second"), not adjectives.
4. **Test levels:** for a design, which behaviour can be tested at component level, which only
   in integration or system tests, and whether the design isolates components enough to test
   them alone.
5. **Test types:** functional and the non-functional characteristics at risk are both covered;
   a design that only plans functional tests for a component with performance or security risk.
6. **Seams and control:** external dependencies, clocks, randomness and environment can be
   replaced or controlled in tests; there is a way to put the system in the state a test needs.
7. **Test data:** the data the tests need exists, can be created, and does not depend on
   production.
8. **Environments:** the environments the tests need are named, and exist or are planned.
9. **Failure paths:** error and degraded behaviour is specified well enough to be tested, not
   only the happy path.
10. **Exit criteria:** for a test plan, when testing is done is decided in advance and
    measurable.

## Evidence a finding needs

The requirement or design decision quoted, and the concrete test that cannot be written or
the question a tester cannot answer from the artifact.

## Cannot see

Whether the planned tests are enough for the risk; that is a test-design question. For planning
the tests of a change level by level, use the `test-planning` skill in this plugin.

## Common false positives

A target defined in a non-functional requirements document outside your scope; testing details
deliberately deferred to a test plan that exists (check before flagging).
