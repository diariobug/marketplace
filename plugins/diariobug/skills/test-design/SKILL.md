---
name: test-design
description: Designs the test cases for a rule, a feature, an input or a workflow with test design techniques — equivalence partitioning, boundary value analysis, decision tables, state transitions, branch coverage, error guessing, exploratory charters and checklists — choosing each technique from the shape of what is tested and taking every expected result from the test basis agreed with the user. Use when deciding which cases and values to test, when asked for edge cases, boundary values or "what inputs should I test", when a business rule combines several conditions, when something moves between states (orders, subscriptions, sessions, approvals), when reviewing whether tests chose their cases well, or after test-planning has placed a risk whose cases must be designed. Produces a case table (each case with its technique, coverage item and source of its expected result), coverage per technique, the questions the design raised, and what was not designed and why.
license: CC BY-NC-SA 4.0 (https://creativecommons.org/licenses/by-nc-sa/4.0/)
---

# Test design

A free resource from Diario Bug (https://diariobug.com), licensed under CC BY-NC-SA 4.0:
share and adapt it for non-commercial use, crediting Diario Bug, under the same license.
Techniques and coverage criteria follow the ISTQB Certified Tester Foundation Level syllabus
v4.0.1 (2024), chapter 4, cited as "§" below.

## Why this method

Nothing can be tested exhaustively. A technique is a way to choose a small set of cases with a
high chance of finding defects, and to say exactly what that set covers, so that what it leaves
out is visible too (§4.1). Asked only to "write tests", an agent writes cases that follow no
principle: the values look reasonable and their gaps are invisible. Told which technique fits
and given the context, it designs well. The person's part is that context (the rule, the risk,
what matters to the business) and the review of the result.

Designing cases has a second effect: it is a close reading of the requirement. Drawing the
partitions of a field or the columns of a decision table asks questions the requirement never
answered ("and at exactly 65?", "and if both conditions hold?"). Those questions are part of the
output, not a detour.

This skill decides **which cases and which values**. Where each test belongs (its level and
type) is `test-planning`'s job; when both are installed, that skill places the risk
and this one designs its cases.

The techniques are not limited to functional testing: a load test has partitions and
boundaries (payload sizes, concurrent users), and a recovery procedure has states. The examples
here are functional. For a test type with a skill of its own (performance, usability, a
migration), that skill owns what is specific to the type, such as workload models or real
users, and uses these techniques for the cases.

## The workflow

Copy the checklist into your working notes and check it off as you go; it is never part of the report:

```
Test design progress:
- [ ] 1. Test object and test basis agreed with the user
- [ ] 2. Technique chosen for each part, from its shape
- [ ] 3. Coverage items listed and numbered
- [ ] 4. Questions the design raised, asked with examples, answered
- [ ] 5. Cases designed from the coverage items
- [ ] 6. Expected results from the test basis, not from the code
- [ ] 7. Coverage per technique, then branch coverage as a map
- [ ] 8. Case table, coverage, and "not designed, and why" reported
```

**1. Test object and test basis.** Name what is under test (a function, an endpoint, a form, a
workflow) and the sources its expected behavior comes from: requirements, acceptance criteria,
API contracts, product docs, the project's rules. Show the user the sources and which behavior
each one specifies. Never use the code as the test basis for black-box cases: it says what the
software does, which is the thing under test.

**2. Choose the technique by the shape of what is tested.** Most features have several parts
with different shapes; each part gets its own technique. Read
[references/choosing.md](references/choosing.md) for the table of shapes and techniques, and
the technique's reference before applying it:

- [references/black-box.md](references/black-box.md): equivalence partitioning, boundary value
  analysis, decision tables, state transitions.
- [references/white-box-and-experience.md](references/white-box-and-experience.md): branch
  coverage, error guessing, exploratory charters, checklists.

[references/worked-example.md](references/worked-example.md) runs the whole workflow on one
rule, with three techniques and the questions each one raised.

**3. List the coverage items.** Every technique turns the test basis into countable items:
partitions, boundary values, feasible decision rules, transitions. Write them down and number
them (P1, B3, R5, T7) before writing any case. A case that exercises no listed item is not
designed; an item no case exercises is a gap you can now see.

**4. Ask the questions with examples.** Every coverage item the test basis does not settle
becomes a question with a concrete case and your proposed answer: "A refund requested exactly
7 days before the event: 50% (the 7–29 day band) or nothing?" Wait for the answers. A boundary
or a combination you settle yourself becomes a test that records your guess.

**5. Design the cases.** Cover the items with as few cases as the technique allows, but never
hide a defect to save one: combine valid items freely, and put **one invalid item per case**,
so that the first rejection does not mask the second (§4.2.4 states it for invalid transitions;
the same reasoning applies to invalid partitions). Order the cases by the risk of what they
cover.

**6. Expected results without the code.** Each case's expected result comes from the test basis
or from an answer the user confirmed, and names its source. If you can start a subagent with a
fresh context, give it only the test basis and the user's answers as they were written (not
your summary of them, which carries your reading) and the case inputs (never the code, the
diff or this conversation), and use its expected results; a disagreement with yours is a
defect or a gap, so ask. Without subagents, write each expected result down before reading
the code that computes it.

**7. Coverage.** Report the coverage of each technique as the syllabus defines it: items
exercised over items identified. When the code exists, run the cases with branch coverage and
read the report as a map: a branch no case reaches is either a behavior the test basis never
mentioned (ask about it) or dead code (propose deleting it). Do not add cases only to reach a
branch; add the case the test basis says that branch is for. Diario Bug explains why, in
Spanish: https://diariobug.com/cobertura-de-codigo/

**8. Report** in the format below, and nothing else: each table appears once, and no
progress checklist or restatement of earlier sections. The questions are written in full once,
in "Questions raised by the design"; everywhere else, including after the user answers,
refer to them by ID (Q3). A reader should find each fact in one place. Every section of the
template is required; write "none" in a section that has nothing, so the reader knows it was
considered.

## Report format

````markdown
## Test design — <test object>

Test basis: <sources>. Answers: see Questions (by ID).

### Questions raised by the design
| ID | Question, with its example | Proposed answer | User's answer | Cases it changed |
|---|---|---|---|---|

### Findings
<Where the code, a document or an answer contradicts the agreed test basis. Each one stated as
a defect, not as a possibility: what the basis says, what the code does (file:line), and the
case that will fail. "None" if the code was not read or nothing contradicts the basis.>

### Parts and techniques
| Part | Shape | Technique | Why |
|---|---|---|---|

### Coverage items
| ID | Technique | Item | Source |
|---|---|---|---|

### Cases
| Case | Inputs / steps | Expected result | Source of expected result | Covers | Risk |
|---|---|---|---|---|---|

### Coverage
| Technique | Items identified | Exercised | Coverage |
|---|---|---|---|
<Branch coverage, when the code exists: branches not reached and what each one is.>

### Independent expected results
<If a fresh-context subagent derived them: the exact text it was given (or the files, by path),
then a table of every case where its expected result differed from yours and how it was
settled. "Disagreements: none" only after that text. If no subagent was used: "Not used;
expected results written before reading the code" or the reason.>

### Evidence
| Claim the design relies on | Command run | Output |
|---|---|---|
<Every computed value, encoding or byte count, library or language behavior, and every claim
about what the code does, with the command and its literal output. A claim without a row is
marked "unverified guess" where it is used.>

### Not designed, and why
<Combinations, transitions or partitions left out, each with the reason.>
````

## Rules that apply everywhere

- Partitions do not overlap and none is empty (§4.2.1). If two values could belong to two
  partitions, the test basis is ambiguous: ask.
- Boundary values exist only where the values are ordered (§4.2.2). A list of countries has
  partitions, not boundaries.
- A boundary is where the test basis puts it, not where the code does. When they differ, that
  difference is the finding.
- Invalid partitions and invalid transitions are designed, not only the valid ones: the
  defects that reach production often live in "that cannot happen".
- Each case cites the coverage item it exercises and the source of its expected result.
- Combinations are a decision. Each-choice coverage ignores combinations (§4.2.1); when the
  outcome depends on how conditions combine, that is a decision table, not more partitions.
- A technique is chosen for the part's shape, not for familiarity. A rule with three
  interacting conditions tested only with boundaries leaves its combinations untested.

## Refuse

- Expected results read from the code or copied from what it returns.
- Choosing a boundary, a combination's outcome or a forbidden transition's behavior yourself
  when the test basis is silent, instead of asking with an example.
- Several invalid items in one case.
- Cases added only to raise a coverage number, with no behavior from the test basis behind
  them.

## Excuses that lead to a weak design

| The thought | Why it fails |
|---|---|
| "These values look like good edge cases." | Without a technique nobody can say what the set covers, so nobody can see what it misses. List the items, then the cases. |
| "The middle of the range is the safest value to test." | It is the least likely to find anything. The defects cluster where one partition ends and the next begins. |
| "Two boundary values are enough here." | For a high-risk comparison, the value on the other side can catch an operator written wrong that two values miss (§4.2.2). Choose 2-value or 3-value by risk, and say which. |
| "Every combination of conditions is too many cases." | Then minimize the table or rank the rules by risk (§4.2.3), and list the ones left out. Silently testing a few combinations hides which ones. |
| "Invalid transitions cannot happen; the UI prevents them." | The UI is one client. An API call, a retry or a second tab sends the event anyway. |
| "The code shows where the boundary is." | It shows where the boundary was implemented. The test exists to check that against the requirement. |
| "Branch coverage is at 100%, so the design is complete." | Branch coverage cannot see a rule nobody programmed or a wrong boundary value that still takes both branches. |

## Signs it went wrong

- Cases with no coverage item, or coverage items with no case.
- Only valid partitions, only valid transitions.
- A boundary value taken from the code, or an expected result that matches the code's output
  with no source.
- One technique applied to every part of a feature whose parts have different shapes.
- A decision table whose number of columns nobody can explain.
- No questions raised: either the test basis was unusually complete, or the design did not
  read it closely.
- A case built on a claimed failure ("this sum rounds below 50", "this library truncates")
  that nobody ran.
- The same table or question list shown twice.

## Before you report

Show the evidence; do not describe it:
- The test basis, and every question asked with its example and answer.
- For each part, the technique and why its shape calls for it.
- The numbered coverage items and, for each case, the items it covers and the source of its
  expected result.
- The coverage per technique, and branch coverage when the code exists.
- If a fresh-context subagent derived the expected results: what it was given, and each case
  where it disagreed with you and how that was settled. "It agreed" with nothing to check is a
  claim, not evidence.
- For every case that rests on a claimed failure mode, the command you ran and its output.
- What was not designed, and why.
