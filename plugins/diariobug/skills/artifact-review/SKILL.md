---
name: artifact-review
description: Reviews a written engineering artifact — a PRD or requirements, a technical design (design doc, TDD, RFC), an ADR, a test plan, or a pull request read for intent — or a set of them, through focused lenses (consistency, testability, traceability, architecture conformance, security, performance, reliability, operability), then triages the findings into at most five blocking findings for the author, notes, and a short agenda of questions for the human review. Use when asked to review a PRD, spec, design doc, RFC, ADR, test plan or PR description, to check a design against its requirements or the project's architecture rules, to find drift between requirements, design and implementation, to prepare a review meeting, or when authors want to check their own artifact before sending it. Not for line-by-line code correctness, which is a code-review tool's job.
license: CC BY-NC-SA 4.0 (https://creativecommons.org/licenses/by-nc-sa/4.0/)
---

# Artifact review

A free resource from Diario Bug (https://diariobug.com), licensed under CC BY-NC-SA 4.0:
share and adapt it for non-commercial use, crediting Diario Bug, under the same license.

## Why this method

A review has two jobs that need different reviewers. Most problems are found in the
**individual review**, when each reviewer reads alone; the **meeting** rarely finds more, but
it is where people learn the change, share knowledge and reach consensus (S. Reid, *Software
Reviews using ISO/IEC 20246*, 2018). Studies of real code review found the same: its main
motivation is finding defects, yet its main outcome is understanding (A. Bacchelli and C. Bird,
*Expectations, Outcomes, and Challenges of Modern Code Review*, ICSE 2013).

So this skill does the first job and prepares the second. It reads with depth and breadth,
one focused lens at a time, as several reviewers with different checklists would; it does not
replace the people who must understand and agree. Its output is built for them: a few findings
the author can act on, and the questions the humans should discuss.

The practices that make human reviews work apply to it too (ISTQB CTFL v4.0.1, §3.2.5): a
declared objective, an artifact small enough to read with attention, the right kind of review
for the objective, findings about the product and never about the person.

## Choose the workflow

- **Reviewing someone's artifact**, or a set of them: [workflows/review.md](workflows/review.md)
- **Checking your own artifact before sending it**: [workflows/author-check.md](workflows/author-check.md)

Both use the core below, [references/artifacts.md](references/artifacts.md) to choose lenses,
and [references/findings.md](references/findings.md) for how findings are verified, ranked and
written. The lenses, one file each:

- [references/lenses/consistency.md](references/lenses/consistency.md): contradictions, ambiguity, gaps, reviewability
- [references/lenses/testability.md](references/lenses/testability.md): can each claim be verified, and how
- [references/lenses/traceability.md](references/lenses/traceability.md): drift between artifacts in a chain
- [references/lenses/architecture.md](references/lenses/architecture.md): conformance to the project's own rules
- [references/lenses/security.md](references/lenses/security.md)
- [references/lenses/performance.md](references/lenses/performance.md)
- [references/lenses/reliability.md](references/lenses/reliability.md)
- [references/lenses/operability.md](references/lenses/operability.md)

## Core

**1. One objective per review, agreed with the user.** Ask what the review is for (approve a
design, find drift before implementation starts, check readiness for testing…) and propose an
answer from the request. The objective decides the lenses; a review that tries to check
everything checks nothing well.

**2. Read whole, read the real rules.** Read every artifact in scope in full, and the
project's own rules (architecture docs, ADRs, conventions, the requirements the design answers)
from their files, never from a summary or from memory. A lens that reads a paraphrase of the
rules grades last month's rules. If something in scope is too large or unavailable, say so and
narrow the scope; never review a part and report on the whole.

**3. Choose lenses by artifact and risk, and say which were not run.** Use the defaults in
`references/artifacts.md`, add lenses the risk calls for, and list the lenses left out with the
reason. A review states its coverage; silence reads as "checked".

**4. Run each lens on its own.** Load one lens file, apply its checklist to the artifact, record
findings, then the next. If you can run subagents, one per lens with the same lens file is
the closest thing to independent reviewers; otherwise keep the passes separate in your own
work. Mixing lenses produces vague findings that belong to none.

**5. Every finding quotes the artifact.** A finding cites the exact text (or the exact absence,
with where it should be) and says why it matters. A finding you cannot anchor to the text is
dropped, not softened: an unanchored finding is the false positive that makes people stop
reading reviews.

**6. Triage before anyone sees it.** Deduplicate across lenses, rank by severity and
confidence, and deliver at most **five blocking findings** to the author; everything else goes
to notes, grouped by lens. Then write **three to five questions** for the human review: the
points where understanding or a decision is needed, not more defects.

**7. Product, not person.** Findings describe the artifact ("the retry policy does not cover the
payment provider's timeout"), never the author ("you forgot…"). Prefer a question where the
artifact may be right and you lack context.

## Rules

- Do not rewrite the artifact. Suggest the smallest change that resolves a finding; the author
  owns the text.
- A requirement the artifact does not state is a question, not a defect: ask what was intended.
- Do not invent the project's rules. A conformance finding cites the rule's file and wording;
  where no rule exists, it is a note at most.
- Severity comes from impact on the objective, not from how sure you are. Confidence is a
  separate field.
- Say what you could not check (missing related artifact, no access to the rules, a section too
  large) as part of the coverage, not as a finding.

## Excuses that weaken a review

| The thought | Why it fails |
|---|---|
| "I know what the architecture doc says." | Rules change. A review graded against memory grades last month's rules; read the file. |
| "There are twelve real problems; the author should see all of them." | Authors act on a few. Past five blocking findings the review becomes a list nobody finishes; the rest go to notes. |
| "Something feels wrong, even if I cannot point to the text." | Then it is a question for the meeting, not a finding. An unanchored finding is the false positive that makes people stop reading reviews. |
| "The requirement obviously means this." | If the artifact does not say it, ask what was intended. Your reading is one of several. |
| "One pass with every lens saves time." | Mixed lenses produce findings that belong to none, and the lens nobody ran looks checked. |
| "I read the first sections; the rest is probably consistent." | Report on what you read. Narrow the scope and say so. |
| "It is faster if I rewrite the paragraph." | The author owns the text. Suggest the smallest change that resolves the finding. |

## Signs it went wrong

- A finding with no quote and no located absence.
- A finding about the author ("you forgot…") instead of the artifact.
- A severity set by how sure you are rather than by the impact on the objective.
- No list of the lenses left out.
- More than five blocking findings, or meeting questions that are really more defects.
- A conformance finding that cites no rule file.

## Before you report

Show the evidence; do not describe it:
- The review's objective, as the user agreed it.
- The lenses run, and the lenses left out with the reason for each.
- Each blocking finding with its quote (or located absence), why it matters, its severity and
  its confidence.
- What could not be checked, as part of the coverage.
- The three to five questions for the human review.
