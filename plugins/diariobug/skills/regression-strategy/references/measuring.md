# Measuring the inventory

Every number in the report comes from the repository, so the report can be checked. When a
source is missing, name it and ask for it; do not estimate.

## Where each number comes from

| Number | Look for | Notes |
|---|---|---|
| Complete suite duration | CI run history (the full or nightly job), or a local run of the full suite | Prefer several recent runs; report the range, not one run |
| Duration per test | Test reports: JUnit XML (`time` attribute on `testcase`), pytest `--durations=0` or `--junitxml`, Jest `--json` | Sort and list the slowest |
| Scope mix (unit / integration / E2E) | Test directories, markers and tags, the frameworks used (a browser driver means E2E), what each test touches | Count by scope, not by size. When the repository does not separate them, say how you classified them |
| Flaky tests | CI history: the same commit with different results, re-runs, retry plugins, tests marked flaky or skipped with a reason | A test that needed a retry to pass is flaky |
| Obsolete tests | Tests that reference removed code or features; tests skipped for a long time | Confirm with the team before proposing deletion |
| Manual tests | The release checklist, the test management tool, the team | Ask; this is rarely in the repository |
| Gates in place | CI configuration (workflow triggers, path filters, schedules), pre-commit hooks, branch protection rules | What already runs where, and what blocks a merge |

## Classifying scope when the repository does not mark it

Many repositories have no markers, tags or folders that separate unit, integration and
end-to-end tests. Classify each test by what it touches, read from its code (fixtures, calls,
imports), and apply the first rule that matches:

1. **System / end-to-end:** drives the product through its own entry point (CLI runner,
   `main()`, HTTP client against the running app, a browser driver), or uses a fixture that
   starts the whole application or pipeline.
2. **Integration:** uses a real collaborator across a boundary (a database, even SQLite in a
   temporary folder; the file system as the thing under test; a message queue), or a fake of
   an external service over its real protocol (an in-memory HTTP server, a mock transport).
3. **Unit:** everything else. Writing to a temporary folder as setup does not make a test an
   integration test, but it makes it slower; note it.

Automate it when the suite is large (parse the test files and look at fixture arguments and
called names), then read a sample of each class to check the rules held. In the report, say
the classification was inferred, give the rules used, and give counts **and** measured time
per class: the time is usually where the surprise is (in one repository, 11% of the tests,
the system ones, took 58% of the time).

## Size and scope are different

Google classifies tests by **size**, the resources a test may use: small tests run in a single
process, medium tests on a single machine, large tests anywhere. **Scope** is how much of the
system a test exercises: unit, integration, end-to-end. The 80/15/5 mix is a scope mix. A
report that mixes the two will recommend the wrong thing: a "small" end-to-end test is
possible, and so is a slow unit test.
