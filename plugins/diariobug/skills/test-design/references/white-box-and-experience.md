# White-box and experience-based techniques

## Contents

- Branch coverage, read as a map (and MC/DC for compound decisions)
- Error guessing and fault attacks
- Exploratory testing with a charter
- Checklist-based testing

## Branch coverage, read as a map (§4.3)

**For:** after the black-box cases exist and run. White-box techniques take the whole
implementation into account, so they find defects even when the specification is vague,
outdated or incomplete; and they cannot find what was never implemented, the defects of
omission (§4.3.3).

**How:**
1. Run the black-box cases with branch coverage on (`coverage run --branch`,
   `pytest --cov-branch`, `go test -cover`, `jest --coverage`, or the project's tool).
2. List each branch no case reached, with its condition and outcome.
3. For each one, ask what behavior that branch is for:
   - The test basis specifies it, and a black-box case is missing: design that case with the
     technique its shape calls for.
   - The test basis does not mention it: it is a question for the user, with an example input
     that reaches it. The code may implement a rule nobody agreed to.
   - Nothing can reach it: it is probably dead code; propose deleting it.
4. Branch coverage includes statement coverage, not the reverse (§4.3.2). Report branches, not
   lines.
5. Read the static analyzer's warnings for the same code (the linter, the type checker):
   coverage cannot see code that was never written, but an input nobody reads (an unused
   parameter, a field fetched and ignored) often marks a rule that was never programmed. Ask
   what that input was for.

**What it cannot see:** a wrong value at a boundary that still takes both branches, a rule
nobody programmed, and a defect that only shows on a particular path through several
decisions. A high number is a map of what ran, not a grade, and a test with no assertion on the
result counts as covered. Diario Bug explains it with a worked example, in Spanish:
https://diariobug.com/cobertura-de-codigo/

**Decisions with several conditions (MC/DC).** In `a or b`, branch coverage is met by one case
where `a` is true and one where nothing is: `b` never decides, and a defect in `b` passes.
Modified condition/decision coverage asks for each condition to be shown changing the outcome
on its own, with the others fixed; for n conditions it usually takes n + 1 cases, not 2ⁿ. Use it
for compound decisions that carry high risk (Diario Bug's practice; the CTFL syllabus stops at
branch coverage). Two details:
- A relational operator hides a condition: `x >= 5` is `x > 5 or x == 5`. MC/DC on that hidden
  condition asks for exactly `x == 5`, which is the case boundary value analysis gives.
- MC/DC still says which conditions must decide, not which values to use; the values come from
  the black-box techniques.

## Error guessing and fault attacks (§4.4.1)

**For:** anticipating errors, defects and failures from knowledge of how this application has
failed before, the errors its developers tend to make, and how similar applications fail. A
fault attack makes it systematic: a list of possible failures, and a test designed for each.

**Building the list:** the syllabus's categories are a starting point: input, output, logic,
computation, interfaces and data. Fill them from the project's own history first: past bug
reports, incidents, reverted commits, the comments that say "careful". A list from this
project beats any generic one.

Each entry on the list becomes a case with an expected result from the test basis, like any
other case. Error guessing chooses the inputs; it never decides what is correct.

**Check the guess before it becomes a case.** A guessed failure that can be checked cheaply (a
floating-point sum, a date parse, what a library does with a long input) is checked by running
it in the project's language before the case is written. A guess that turns out false designs a
case that cannot fail, and the reader trusts it anyway. Keep the command and its output as the
case's evidence; if you cannot run it, mark the case "unverified guess".

## Exploratory testing with a charter (§4.4.2)

**For:** little or poor specification, time pressure, a new area, and what only a person can
judge: whether a flow is clear, whether a message makes sense, whether something feels
broken. Test design, execution and learning happen together.

An agent can prepare and support a session; the person runs it, because the point is what a
person notices. Write the charter for them:

```markdown
Charter: Explore <target> with <resources, data, tools> to discover <information>.
Time box: <60–90 min>
Areas: <the parts, risks or questions to focus on>
Starting points: <inputs, accounts, states worth trying first>
Notes to keep: what was tried, what was found, questions, ideas for new cases
```

After the session, the debrief turns its findings into defects, questions for the test basis,
and new systematic cases where a finding has a shape (a boundary, a missing column, an
invalid transition).

## Checklist-based testing (§4.4.3)

**For:** conditions worth checking every time: accessibility, usability heuristics, a team's
recurring defects. Each item is a question that can be checked on its own and directly. Leave
out items a tool can check automatically, items that are really entry or exit criteria, and
items too general to check. Update the list from defect analysis, retire items the team no
longer gets wrong, and keep it short.
