# Sources, quoted as they are

Verified against the pages on 2026-10-25 and 2026-10-31. Quote these; do not strengthen them.

## Google: *Software Engineering at Google* (Winters, Manshreck, Wright, eds., O'Reilly, 2020)

**Chapter 11, "Testing Overview" (Adam Bender).** https://abseil.io/resources/swe-book/html/ch11.html

- Size: "small tests run in a single process, medium tests run on a single machine, and large
  tests run wherever they want".
- "there are two distinct dimensions for every test case: size and scope." "Size and scope
  are interrelated but distinct concepts."
- The mix is by scope: "around 80% of our tests being narrow-scoped unit tests", "15%
  medium-scoped integration tests", "5% end-to-end tests that validate the entire system".
  Google says it "tend[s] to aim" for this mix; it is not a hard rule.
- Large tests: teams "will frequently isolate their large tests from their small or medium
  tests", "running them only during the build and release process so as not to impact
  developer workflow."
- Flakiness: "as you approach 1% flakiness, the tests begin to lose value." Google's flaky
  rate "hovers around 0.15%, which implies thousands of flakes every day."

**Chapter 23, "Continuous Integration" (Rachel Tannenbaum).** https://abseil.io/resources/swe-book/html/ch23.html

- Presubmit: "Our general rule of thumb is: only fast, reliable ones." "We typically limit
  presubmit tests to just those for the project where the change is happening."
- Post-submit: "you can accept longer times and some instability, as long as you have proper
  mechanisms to deal with it."
- Larger tests on presubmit: "this varies by team." (Not forbidden.)
- Flaky tests: "Some teams rely on a tool to remove such flaky tests from presubmit
  temporarily while the flakiness is investigated and fixed." (No "bots", no full automation
  claimed.)
- Selection: Google's continuous build (TAP) "can no longer run every test on every change"
  and uses each change's dependency graph to choose the tests to run.

**Chapter 14, "Larger Testing" (Joseph Graves).** https://abseil.io/resources/swe-book/html/ch14.html

- "Our large tests have a default timeout of 15 minutes or 1 hour". (A timeout, not a rule
  that rejects tests.)
- "Manual testing does not scale sublinearly; that is, it requires human time to perform the
  manual tests."

## Martin Fowler

**"Eradicating Non-Determinism in Tests" (2011).** https://martinfowler.com/articles/nonDeterminism.html

- Place non-deterministic tests in a quarantined area, out of the main pipeline, with a limit
  on how many tests it holds or how long a test may stay, so quarantine does not become where
  tests are forgotten.

**"Continuous Integration" (revised 2024).** https://martinfowler.com/articles/continuousIntegration.html

- The commit build "has to be done quickly" (the XP guideline of a ten-minute build). A
  secondary stage with tests that hit a real database "might take a couple of hours to run"
  and "may not run after every commit".

## Microsoft: "Use Test Impact Analysis - Azure Pipelines"

https://learn.microsoft.com/en-us/azure/devops/pipelines/test/test-impact-analysis

- Safe fallback: "For commits and scenarios that TIA can't understand, it falls back to
  running all tests." "You can run all tests at a configured periodicity."

## Meta: Machalica et al., "Predictive Test Selection" (arXiv:1810.05286)

https://arxiv.org/abs/1810.05286

- A selection strategy "learned from a large dataset of historical test outcomes", which
  still reports "over 95% of individual test failures and over 99.9% of faulty changes" to
  developers, at Meta's scale. The paper does not say a "minimal fraction" of tests is run.

## Experience, not sources

These are Diario Bug's rules of thumb. Present them as defaults:

- More than 15% E2E tests is a warning sign.
- Quarantine limit: 14 days, then delete.
- Gate tolerances: local seconds, pull request minutes, nightly hours.
- The probability-against-impact matrix is common practice; do not attribute it to an author.
