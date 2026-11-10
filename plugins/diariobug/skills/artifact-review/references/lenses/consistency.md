# Lens: consistency and completeness

**Finds:** what makes two readers build different things from the same artifact, and what is
missing for it to be built at all. These are the requirement defects the ISTQB syllabus lists
as easier and cheaper to find by reading than by running: inconsistencies, ambiguities,
contradictions, omissions, inaccuracies, duplications (CTFL v4.0.1, §3.1.3).

**Applies to:** every artifact; the default for PRDs, requirements, user stories and ADRs.

## Checklist

1. **Contradictions:** two statements that cannot both hold (a limit stated twice with different
   values, a flow that is optional in one section and required in another).
2. **Vague terms:** "fast", "secure", "user-friendly", "frequent", "large" with no measure.
3. **Undefined boundaries:** "more than five", "up to", "after", "within" — is the edge included?
   What happens exactly at it?
4. **Ambiguous logic:** "and/or", "or" that may be inclusive or exclusive, conditions that may
   stack, precedence between rules.
5. **Undefined terms:** a domain word or acronym used before or without a definition, or used
   with two meanings.
6. **Missing paths:** what happens on error, timeout, cancellation, retry, empty input, the
   second attempt, the reverse operation (refund, undo, delete).
7. **Missing actors or states:** who can do this; what other states the object can be in;
   what happens to existing data.
8. **Acceptance criteria:** each one checkable, specific, and tied to a requirement; a
   requirement with none.
9. **Scope:** what is explicitly out of scope; assumptions and dependencies stated.
10. **Reviewability:** length and density against what the artifact decides. A document whose
    decisions fit in a page but run to twenty is itself a finding: reviewers cannot read it
    with attention (CTFL v4.0.1, §3.2.5, "performing reviews on small chunks").

## Evidence a finding needs

The quoted text, and either the second quote it contradicts, or the concrete case it does not
decide ("a cart with exactly five items").

## Cannot see

Whether the requirement is the right one for the business; that needs the people who own it.

## Common false positives

A term defined in a glossary or parent document you were not given (ask before flagging); a
deliberate "to be decided" marked as such (a note at most, unless the objective is readiness).
