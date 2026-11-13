# Findings: verify, triage, report

## One finding

```
- [lens] Severity: blocking | note    Confidence: high | medium | low
  Where: <section or heading>  — "<exact quote>" (or: missing from <section>)
  Problem: <what is wrong with the artifact, in one or two sentences>
  Why it matters: <the consequence for the review's objective>
  Suggestion: <the smallest change that resolves it, or the question to ask>
```

- **One problem per finding.** Two defects in the same sentence are two findings, each with
  its own quote and suggestion. **Why:** a finding that bundles three problems makes the
  five-finding cap lenient without saying so, and the author cannot fix or answer one part
  without the others. Merge only the same problem found by two lenses (see Triage).
- **Where** quotes the artifact. An absence names the section where the missing thing belongs.
- **Problem** is about the artifact, never the author.
- **Severity** comes from impact on the objective. Blocking: the artifact cannot do its job
  until this is resolved (a requirement two readers will build differently, a design that
  breaks a stated rule, an untestable acceptance criterion on a critical path). Note: worth
  fixing, does not block.
- **Confidence** is separate: high when the text is unambiguous, low when it depends on
  context you do not have. A blocking, low-confidence finding becomes a question.

## Verify

Before triage, re-read every finding against its quote. Drop it when:

- the quote does not say what the finding claims;
- another part of the artifact, or a related artifact in scope, already answers it;
- it depends on a rule the project does not have (keep it as a note only if the risk is real,
  and say no rule covers it);
- it restates a preference ("I would structure this differently") with no consequence.

Count what you dropped. A review that drops nothing usually did not verify.

## Triage

1. **Merge duplicates.** The same gap found by two lenses is one finding; name both lenses.
2. **Rank** by severity, then by confidence, then by how much the fix unblocks.
3. **Blocking: at most five.** If more than five are blocking, the artifact is not ready for this
   review: say so in one sentence at the top and list the five that matter most. Do not
   inflate a note to fill the five, and do not hide a sixth blocking finding in the notes
   without saying the cap was reached.
4. **Notes** grouped by lens, short, with the quote.
5. **Questions for the human review, three to five.** Where understanding or a decision is
   needed: a trade-off the artifact does not make explicit, a requirement whose intent is
   unclear, a disagreement between two artifacts that only their authors can settle. Not
   defects in question form.

## Report

```
# Review: <artifact(s)>
Objective: <one sentence, as agreed>
Coverage: lenses run — <list>; not run — <lens: reason>; could not read — <what, why>.
Findings verified: <n> kept, <n> dropped.

## Blocking (<n> of at most 5)
<findings>

## Notes
### <lens>
<findings>

## Requirement map (only when the traceability lens ran)
| Upstream | Downstream | Status |
|---|---|---|
| R1 "<short quote>" | <section> | matches / changed / dropped / weakened |
| — | <section> "<short quote>" | invented |

## Questions for the review
1. …
```

Lead with what changes the decision the review exists for. Keep the language plain; the author
will read this right after finishing their work.
