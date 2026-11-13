# Lens: architecture conformance

**Finds:** where a design or change departs from the project's own architecture rules,
decisions and patterns — the ones written down in the project, not general best practice.

**Applies to:** designs, ADRs, PRs read for intent, in a project that has written rules
(architecture overview, ADRs, layering rules, conventions, a style guide for designs).

## Before the checklist

Read the rules from their files, whole, wherever the project keeps them (step 2 of the review
workflow says how to find them). List the documents you used. If the project has no
written rules and the user cannot point to any, say so and run this lens only as notes on internal consistency of the design;
do not grade against rules you imagine the project should have.

## Checklist

1. **Layering and dependencies:** the design adds a dependency the rules forbid (a lower layer
   calling a higher one, a domain module importing an adapter).
2. **Ownership:** a responsibility placed in a component the rules assign elsewhere; a second
   copy of logic that has an owner.
3. **Decisions:** the design contradicts an accepted ADR without superseding it.
4. **Patterns:** a problem the project solves one way (errors, configuration, retries, logging,
   persistence) solved a different way here, with no reason given.
5. **Interfaces and contracts:** changes to a public interface, schema or event without the
   versioning or compatibility the rules require.
6. **Data:** data stored, duplicated or moved against the rules (source of truth, retention,
   residency).
7. **Cross-cutting rules:** security, observability or configuration rules the project states
   for every component.
8. **New components:** a new service, library or store the rules say needs a decision record.
9. **Naming and structure:** where the rules define them and the design ignores them.
10. **Reversibility:** a one-way decision (data model, public API, vendor lock-in) taken without
    the record the rules ask for.

## Evidence a finding needs

The design's text and the rule's text, with the rule's file. "Violates clean architecture" is
not a finding; "adds `orders → payments.adapter` import; the layering rule in the project's
architecture document: 'dependencies point down only'" is.

## Cannot see

Whether the rule is still right. A design that breaks a rule for a good reason is a question
for the review, and possibly a new ADR.

## Common false positives

A rule superseded by a later ADR (read the decisions in order); a deliberate, recorded exception.
