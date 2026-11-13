---
name: test-planning
description: Decides what testing a change or a codebase needs by test level (component, component integration, system, system integration, acceptance) and test type (functional, non-functional, black-box, white-box), with the test basis agreed with the user and risks ranked by likelihood and impact. Use it whenever code that will need tests is being written or changed - a new feature, command, endpoint or bug fix - even when the request only says to build or fix it and never mentions tests; the tests are planned here, with the user, before they are written. Also for reviewing the tests of a change that already happened ("is this PR tested enough?", "review the tests of this commit") and for assessing how an existing test suite covers a module or feature ("how well do our tests cover X?", "where are our testing gaps?"). Produces a risk-ranked plan or matrix with an explicit "not covered, and why" and leaves residual risk to a person.
license: CC BY-NC-SA 4.0 (https://creativecommons.org/licenses/by-nc-sa/4.0/)
---

# Test planning, by level and type

A free resource from Diario Bug (https://diariobug.com), licensed under CC BY-NC-SA 4.0:
share and adapt it for non-commercial use, crediting Diario Bug, under the same license.
Levels, types and attributes follow the ISTQB Certified Tester Foundation Level syllabus
v4.0.1 (2024), cited as "§" below; v3.1 (2019) where noted.

## Choose the workflow

Read the one file that matches the task, then follow its checklist:

- **Planning or writing a change** (new tests, a feature, a bug fix): [workflows/plan.md](workflows/plan.md)
- **Reviewing a change that already happened** (a diff, a commit, a pull request): [workflows/review.md](workflows/review.md)
- **Assessing an existing test suite** (a module, a feature, a repository): [workflows/assess.md](workflows/assess.md)

All three use the core below. [references/levels-and-types.md](references/levels-and-types.md)
says what each level sees and cannot see; [references/worked-example.md](references/worked-example.md)
is a complete plan for one change.

## Hand off to the other Diario Bug skills

This skill is the entry point: it decides what testing a change needs and where each risk
belongs. When another Diario Bug skill is installed, hand it the part it owns, and say in the
plan which rows went where:

- **The cases of a row** (which inputs, values, combinations, sequences): `test-design`.
- **A risk at a boundary the project does not control** (an SDK, an external API, a cloud
  service, remote configuration, a third-party script): `dependency-failures` analyzes how it
  can fail and derives the tests.
- **A risk that lives in a document** (requirements that contradict each other, a design that
  misses a requirement): `spec-review`.
- **How the tests run once they exist** (which gate, slow or flaky tests, CI time):
  `regression-strategy`.
- **A test type with a skill of its own** (performance, usability, a migration…), when one is
  installed: hand it everything specific to that type; the level and the risk stay here.

A skill that is not installed is not a gap in the plan: do that part here, with this skill's
references.

## Core

**1. Agree the test basis with the user, always.** The test basis is what the expected
results come from: issue and acceptance criteria, pull request description, product or
design docs, API contracts, decision records, the project's docs and rules, commit messages.
Show the user the sources you found and, for each behavior in scope, which source specifies
it or that none does. Ask about every gap **with a concrete example** (this input, this
expected output) and your proposed answer: "`<tr><td>a</td><td>b</td></tr>` → `a | b`?" is
answered in seconds, "how should tables be handled?" is not. Wait for the answer, even when
the sources look complete: a gap is invisible from inside your own reading, and filling it
from the code, or from what the code used to do, turns the test into a record of current
behavior, defects included. You prepare the questions; the agreement is the user's. Every
black-box result cites a source or the user's confirmed example.

**2. Rank the risks.** Name what can go wrong (wrong result, wrong data across an
interface, broken flow, a mishandled failure of an external system, a quality characteristic
at risk) and give each a level, high, medium or low, from its likelihood and its impact
(§5.2.1). When existing behavior is in scope, what must keep working is a risk too:
regression testing "confirms that no adverse consequences have been caused by a change"
(§2.2.3), and requirements are usually silent about what a change must keep. Its expected
result is the previous behavior, confirmed by the user, because the old behavior can be the
defect.

**3. Place each risk at the earliest level that can see it, and give it a type.** Lower and
the test cannot see the defect; higher and it costs more to find and to trace. Level and
type are independent (§2.2.2). Use the "cannot see" column of the reference.

**4. Say what is not covered, and why.** Every risk gets a row, covered or not. "Not
covered" is a decision with a reason (no sandbox, needs real users, low risk and out of
time), never an omission. "The tests pass" means nothing until it says at which levels and
of which types.

**5. Leave residual risk to a person.** If a high risk is uncovered or an exit criterion is
unmet, write the residual risk down and stop. The syllabus allows ending without meeting
the exit criteria "if the stakeholders have reviewed and accepted the risk" (§5.1.3); you
are not the stakeholders.

## Rules that apply everywhere

- A component test isolates its component: no real database, network, file system or
  clock. One that needs them is an integration test; call it that.
- An integration test checks what crosses the interface, not each side's logic again.
- An external boundary is tested on its failures too: timeout, error status, malformed or
  unexpected response.
- At a language or vision model, test the contract (request, reply schema, timeout,
  unusable reply, retry and its cost), not the judgment. Judgment is evaluated with a
  recorded real run, listed as evidence, not as a test.
- A test that has never failed proves nothing.
- Branch coverage is measured on what changed or what is assessed, never as one number for
  the project.

## Refuse

- Mocking the unit under test, or a test whose only assertion is that a mock was called.
- An "integration test" whose collaborators are all mocks.
- Expected values copied from what the code returns.
- Changing an existing test's expected value to make it pass, without the test basis saying
  the old value was wrong.
- Filling a gap in the test basis yourself instead of asking with an example.

## Excuses that lead to a weak plan

| The thought | Why it fails |
|---|---|
| "The code makes the intended behavior clear." | The code shows what it does. An expected value taken from it passes on the very defect it should catch. |
| "The sources look complete; asking would slow the user down." | A gap is invisible from inside your own reading. A question with an example costs the user seconds; a wrong expected value stays in the suite for years. |
| "Mocking the database keeps it a fast component test." | If the risk is in the query or in the data crossing the interface, the mock removes what was under test. Test it where the risk is, and name the level honestly. |
| "The end-to-end test already covers it." | It sees that something failed, not where, and it is the slowest place to find out. The risk belongs at the earliest level that can see it. |
| "Coverage is high, so it is tested." | Coverage says what ran, not what was checked, and it cannot see a rule nobody programmed. |
| "This risk is low; I will leave it out." | Leaving it out can be right; leaving it unlisted never is. Without a row, nobody can tell a decision from an oversight. |
| "The exit criteria are almost met; I will call it done." | Accepting what is left is the stakeholders' call (§5.1.3). Write the residual risk down and stop. |
| "The model's answer looked right when I ran it." | That is one recorded run, evidence about judgment, not a test. Test the contract; list the run as evidence. |

## Signs it went wrong

- A new test passed the first time it ran, and nobody has seen it fail.
- An expected value matches the code's output exactly, and no source or confirmed example
  gives it.
- An existing test's expected value changed in the same change as the code it checks.
- "All tests pass", with no levels or types named.
- A check that could not run is reported the same way as a check that passed.
- The plan has no "not covered" row.
- A test's only assertion is that a mock was called.

## Before you report

Show the evidence; do not describe it:
- The test basis the user confirmed, with every question asked and its answer.
- The risk table: every risk with a row, covered or not, and the reason for each "not
  covered".
- For each new test, that it failed before it passed: the failing run, or the line you broke
  on purpose to see it fail.
- The suite run with branch coverage: the changed branches reached, and the ones not reached
  with why.
- If a fresh-context subagent derived expected values: what it was given, and each
  disagreement with how it was settled.
- Each exit criterion, met or not, and the residual risk left to the user.
