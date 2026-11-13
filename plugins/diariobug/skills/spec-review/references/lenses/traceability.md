# Lens: traceability across artifacts

**Finds:** drift along a chain of artifacts that describe the same feature — requirements to
design, design to implementation, requirements to tests. Each artifact can be fine on its own
and the chain still deliver something nobody asked for.

**Applies to:** two or more artifacts in scope: PRD → design, design → PR, PRD → test plan, an
ADR and the ADR it supersedes. Needs both ends; never infer one from the other.

## Checklist

Build a short map first: each requirement (or acceptance criterion) of the upstream artifact,
and where the downstream one addresses it. The map goes in the report (see the report template
in `references/findings.md`): it is the coverage evidence, and it lets the author see at a
glance what matched, not only what drifted. Then check:

1. **Dropped:** a requirement with no counterpart downstream.
2. **Changed:** a counterpart that says something different (a limit, a rule, an order, a
   role), however small.
3. **Invented:** behaviour downstream that no upstream requirement asks for (scope creep, or a
   requirement that should be written down upstream).
4. **Weakened:** a "must" that became a "should", an "always" that became "usually", a target
   that lost its number.
5. **Assumptions:** an assumption downstream that contradicts, or is not stated in, upstream.
6. **Terms:** the same concept named differently, or one name for two concepts, across the
   artifacts.
7. **Acceptance coverage:** for a test plan, each acceptance criterion has at least one test;
   CTFL v4.0.1 lists "gaps or inaccuracies in test basis coverage" among the defects reading
   finds best (§3.1.3).
8. **Versions:** each artifact names the version of the other it was written against; a
   design written against an older PRD.
9. **Decisions:** a decision downstream that changes upstream intent is recorded (an ADR, a
   changelog), not silent.
10. **Open items:** questions marked open upstream that downstream treats as settled.

## Evidence a finding needs

Both quotes — upstream and downstream — or the upstream quote and the section downstream where
its counterpart should be.

## Cannot see

Which side is right when they differ: a drift finding names the difference and asks; the
owners decide whether the requirement or the design changes.

## Common false positives

A requirement handled in a third artifact not in scope (ask); an intentional deviation already
recorded in a decision log you were not given.
