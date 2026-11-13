---
name: regression-strategy
description: Designs or reviews the regression strategy of a repository, deciding which tests run where, when and how, so the suite stays fast, stable and trusted as it grows. Use when CI is slow, when tests pass and fail with the same code, when someone asks which tests should run on a pull request, when setting up nightly or scheduled runs, or when the user asks for a regression strategy or a test-suite health review. Measures the inventory, assigns each test to a gate, picks the simplest selection mechanism that works at this scale, and sets health rules. Produces a report; changes CI configuration only when the user asks.
license: CC BY-NC-SA 4.0 (https://creativecommons.org/licenses/by-nc-sa/4.0/)
---

# Regression strategy

A free resource from Diario Bug (https://diariobug.com), licensed under CC BY-NC-SA 4.0:
share and adapt it for non-commercial use, crediting Diario Bug, under the same license.
The method follows the article "Pruebas de regresión: qué hacer cuando ya no se puede correr
todo" (https://diariobug.com/alcance-de-la-regresion/).

## Why this method

Regression testing checks that a change did not alter, unintentionally, anything outside
itself: at every test level, for every type of test, functional and non-functional. Ideally
every change would run every test. But every feature and every fix adds tests, so the suite
grows on its own, until it is slow, flaky, partly obsolete and costly to maintain, and the
team stops trusting it. The answer is not to run less; it is a strategy that decides **which
tests run, where, when and how**, with three goals: efficient regression, a stable suite, and
results the team trusts.

This skill does not decide *what* to test; for that, use `testing-by-level-and-type`. It
decides how the tests that exist are run.

It also does not cover **operational checks**: whether a production dependency is up, whether
an API key still works, whether a paid service still has credit. Those are monitoring, not
regression. When the assessment finds one missing (for example, the image provider's credit ran
out in the middle of a run), mention it under "what was not assessed" as a separate concern,
with a one-line suggestion, and do not fold it into the gates.

## Rules that apply throughout

- **Evidence or a question.** Every number in the report (durations, scope mix, flaky rate)
  comes from the repository, its CI configuration or its test reports. When the data is not
  there, say what is missing and ask; never estimate it. See
  [references/measuring.md](references/measuring.md).
- **Experience is labelled as experience.** The thresholds below are Diario Bug's rules of
  thumb, not standards. Present them as defaults the team can change.
- **Sources are cited as they are.** What the report attributes to Google, Fowler or
  Microsoft must match [references/sources.md](references/sources.md). In particular, Google's
  80/15/5 mix is about test **scope** (unit, integration, end-to-end), not test **size**.
- **Report first.** Change CI configuration, test markers or quarantine lists only when the
  user asks, and show the diff.
- **The simplest mechanism that works.** Do not recommend dependency-graph or predictive
  selection to a team whose fast tests already run in minutes.

## The workflow

Copy the checklist into your working notes and check it off as you go; it is never part of the report:

```
Regression strategy progress:
- [ ] 1. Inventory measured (or the missing data named)
- [ ] 2. Scale identified (A: small/medium, B: very large)
- [ ] 3. Each group of tests assigned to a gate
- [ ] 4. Selection mechanism chosen for the pull-request gate, with its fallback
- [ ] 5. Health rules set (flaky tests, obsolete tests)
- [ ] 6. Report written; changes applied only if asked
```

### 1. Measure the inventory

You cannot improve what you do not measure. Answer, with evidence:

- How long does the complete suite take today, end to end?
- What share of the tests are unit, integration and end-to-end (E2E)? Count by **scope**,
  how much of the system a test exercises, and measure the time each class takes. When the
  repository does not mark scope, classify with the rules in `references/measuring.md` and
  say so. *Rule of thumb (experience):* if E2E tests are
  more than 15% of the total, that is a warning sign; plan to move some checks down to unit
  or isolated integration tests.
- Which tests are slow? List the slowest by measured duration.
- Which tests are flaky, that is, pass and fail with the same code? Use CI history
  (re-runs, retries, the same commit with different results).
- Which tests protect nothing any more: tests of removed features, or tests nobody can
  explain?
- Which manual tests run before a release, and how long do they take?

### 2. Identify the scale

- **Scenario A, small or medium project.** A simple split between unit tests (fast) and
  integration/E2E tests (slow). Everything fast runs on every pull request; the full suite
  runs nightly; flaky tests are disabled by hand with a ticket to fix them.
- **Scenario B, very large repository.** Thousands of developers integrate at once. A
  reference mix by scope, default time limits for large tests, all gates instrumented,
  selection by dependency graph or by prediction, and automated detection that takes flaky
  tests out of the main run while someone fixes them.

Most teams are in A. Say which one this repository is, and why.

### 3. Assign each test to a gate

A gate is a point in development where a set of tests must pass for work to move on. Each
gate tolerates something different: how long its tests may take, how much flakiness it
accepts, and who gets the result.

| Gate | Tolerates (experience, as defaults) | What runs there |
|---|---|---|
| **Local development** | Seconds; no flakiness, because the person waiting is the one writing the code | Fast tests for what was modified |
| **Pull request** (blocks the merge) | Minutes; almost no flakiness | Smoke tests (a fixed list of the most critical flows, chosen by product risk) and the tests for what the change can affect |
| **After merge, or scheduled (nightly)** | Hours; some flakiness if there is a mechanism to handle it; the result goes to the whole team | Slow tests, tests with real databases, the complete suite |
| **Before release, manual** | The most expensive to repeat | Only what needs a person or a full environment, prioritized by risk |

The rule: put each test in the gate whose tolerances it meets. A slow or flaky test in a gate
that blocks work protects nothing; sooner or later someone removes it or stops reading its
result. Google's practice supports the split: only fast, reliable tests before submit; longer
times and some instability after it (see `references/sources.md`).

Prioritize by risk, especially for manual tests: a probability-against-impact matrix of a
failure is common practice. High probability and high impact get every related test; low
probability and low impact may go untested, accepting the risk.

### 4. Choose how the pull-request gate selects tests

Turning a list of changed files into a list of tests needs a mechanism. Start simple:

1. **Initial:** tags or code areas ("if `shipping/` changes, run `#shipping`"). The risk
   analysis lives here: done once, when the rule is written and reviewed, not on every
   change. Works best with clear module boundaries; the rule must be kept current.
2. **Intermediate:** build tools that know the code's dependencies and run only the tests of
   what depends on the change.
3. **Advanced, very large repositories:** impact analysis (select by the code each test
   exercises) or predictive selection (select from the history of results).

Every mechanism needs a **fallback**: when it cannot decide, run everything; also run
everything on a schedule, and when a third-party library or service version changes.

### 5. Set health rules

Choosing well what to run is worth little if what runs is not trustworthy:

- Every flaky test goes to **quarantine**, out of the main run, so it does not block anyone.
- Quarantine has a limit. *Default (experience):* 14 days; if nobody fixes it, the test is
  deleted. A test that lies is worse than none.
- Prune: delete tests of features that no longer exist; rewrite tests that break on every
  internal change although the behavior did not change. Periodic reviews (or mutation
  testing, where available) help delete obsolete test code with confidence.

Health is not a stage. It applies at every scale, every day.

### 6. Write the report

In this order:

1. **Inventory**, with the numbers and where each came from; missing data named.
2. **Scale** (A or B) and why.
3. **Gate assignment**: a table of test groups → gate, with each group's measured duration
   and flakiness.
4. **Selection mechanism** for the pull-request gate, its fallback, and what it needs
   (a mapping file, a build tool, coverage or history data).
5. **Health rules**: the quarantine list, its limit, the tests to delete or rewrite.
6. **Changes, in order of value**: the first change should be the one that buys the most
   time or trust for the least work. Mark each as done or proposed.
7. **What was not assessed, and why.**

Changes to CI configuration, markers or quarantine lists are made only when the user asks,
and shown as a diff.

## Excuses that weaken the strategy

| The thought | Why it fails |
|---|---|
| "The suite takes about twenty minutes, I would guess." | A guessed number decides gates nobody can check. Take it from CI or the test reports, or ask. |
| "Large companies use predictive selection, so should we." | A mechanism built for thousands of developers costs more than it saves at this scale. Start with the simplest one that works. |
| "Re-run the flaky test until it passes." | Each re-run teaches the team to ignore red. Quarantine it, with a limit. |
| "Keep every test; deleting one is risky." | A test that protects nothing costs every run and every reader. One that lies is worse than none. |
| "To make CI fast, run fewer tests." | The goal is to run each test in the gate whose tolerances it meets, with a fallback that still runs everything. |
| "Google recommends 80/15/5 test sizes." | Google's mix is about scope, not size. Cite sources as they are written. |
| "The expired API key is a regression failure." | It is monitoring. Mention it under what was not assessed. |

## Signs it went wrong

- A number in the report with no source.
- A slow or flaky test in a gate that blocks work.
- A selection mechanism with no fallback.
- A quarantine with no time limit.
- Thresholds presented as standards instead of defaults from experience.
- CI configuration changed without the user asking.

## Before you report

Show the evidence; do not describe it:
- Each number with where it came from, and the data that was missing.
- The scale (A or B) and why.
- The gate table with each group's measured duration and flakiness.
- The pull-request selection mechanism and its fallback.
- The health rules: the quarantine list, its limit, the tests to delete or rewrite.
- What was not assessed, and why.
