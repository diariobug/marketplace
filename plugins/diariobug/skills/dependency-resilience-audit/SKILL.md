---
name: dependency-resilience-audit
description: Analyzes how a system can lose because of what it does not control — SDKs, external APIs, cloud services, remote configuration, third-party scripts, packages — using an STPA-based control analysis of each dependency boundary. Use when adding, upgrading or reviewing a third-party integration, after an incident caused by a dependency, or when the user asks for a dependency resilience, third-party risk or failure-mode review. Produces a report with the control structure, unsafe control actions, loss scenarios backed by code evidence, mitigations and the tests to add. Changes code only when the user asks.
license: CC BY-NC-SA 4.0 (https://creativecommons.org/licenses/by-nc-sa/4.0/)
---

# Dependency resilience audit

A free resource from Diario Bug (https://diariobug.com), licensed under CC BY-NC-SA 4.0:
share and adapt it for non-commercial use, crediting Diario Bug, under the same license.

## Why this method

A dependency failure rarely looks like a broken part. Each piece passes its own tests, and the
loss comes from how the pieces *interact*: one component sends another a command or data it
did not expect, at a moment it did not expect, and nobody finds out in time because the feedback
that would have shown it is missing, late or measures the wrong thing. A checklist asks whether
you remembered a known failure. This audit asks how *this* system is controlled across each
boundary, and derives what could go wrong from that.

The method is an adaptation of STPA (System-Theoretic Process Analysis; N. Leveson and
J. Thomas, *STPA Handbook*, 2018) to software dependency boundaries. It is a lightweight
engineering analysis, not a full safety assessment; say so in the report.

## Principles

- **Losses first.** Everything traces to a loss the people who own the system care about. A
  finding that leads to no loss is noise.
- **Control and feedback, not components.** Model who commands whom and what each side learns
  back. Data and configuration that flow *into* our code from a dependency are as much a part
  of the control loop as the calls we make.
- **Code you own vs. code you don't.** Validation and error handling only work in code you own.
  A `try/catch` around a call does not reach a failure inside the dependency's own threads, a
  native crash, or a process exit. Against those, only isolation and a switch to not run the
  dependency help. Never propose a protection that cannot reach the failure it claims to stop.
- **Evidence or question.** Every claim about the system cites a file and line. What the code
  cannot show — the vendor's release process, an SLA, who is on call, what a dashboard alerts on
  — goes in the diagram as an unknown and in the report as a question. Do not fill gaps with
  plausible guesses.
- **Depth over breadth.** A few boundaries analyzed to the end beat a long list of shallow
  entries. Cap and rank (below).
- **Report first.** Do not edit code until the user has read the report and asked for a change.

## Procedure

Read `references/stpa.md` before step 3, `references/system-kinds.md` in step 1, and
`references/mitigations.md` in step 6.

1. **Scope.** Determine the kind of system (mobile app, backend service, web frontend, library
   or CLI, AI/LLM application, data pipeline — or a mix) from the repository; `references/system-kinds.md` says
   where its boundaries usually live. If the user named a dependency, analyze its boundary.
   Otherwise inventory dependencies from the manifests and the code's outbound and inbound
   integrations, rate each by the harm it could cause (not by the feature it provides), and
   propose the **at most five** highest-harm boundaries to analyze. Confirm with the user.
2. **Losses and hazards.** Propose 2–4 **losses** (e.g. users cannot use the core function; data
   is lost or corrupted; money is charged wrongly; private data is exposed) and the **hazards**
   — system states that lead to them under bad-enough conditions (e.g. "the app terminates
   during launch", "an order is recorded without a confirmed payment"). A hazard describes the
   system's state, not a component's failure. **Ask the user to confirm or edit the losses**:
   they are a product decision, not something the code knows.
3. **Control structure.** For each boundary in scope, build the control loop from the code:
   controllers (our modules, the dependency, the vendor's backend, the people who operate each
   side), the **control actions** each sends (calls, requests, initialization, pushed
   configuration and flags, commands), and the **feedback** each receives (responses, errors,
   callbacks, webhooks, telemetry, alerts, dashboards). Mark every element as *evidenced*
   (file:line) or *unknown*. Draw it as a Mermaid flowchart.
4. **Unsafe control actions and inadequate feedback.** For each control action, ask the four
   STPA questions in context, and for each feedback path, the five feedback questions
   (`references/stpa.md`) — "received but not acted on" is the one most often missed. Keep only those that link to a hazard. Write each as one line with
   its context and hazard.
5. **Loss scenarios.** For each unsafe action, explain *why it could happen* and *what happens
   next* in this code: the controller's algorithm, its assumptions about the other side (its
   process model), missing or late feedback, the execution path (e.g. a background queue no
   caller can catch from), the dependency's own behavior. Cite evidence. Rank scenarios by
   severity of the loss and by how little currently stands in the way.
6. **Constraints and mitigations.** Turn each scenario into a constraint ("the app must not
   terminate when a remote flag is malformed") and pick the mitigations that enforce it from
   `references/mitigations.md`. Say which scenario each one prevents, and what it does not protect against.
7. **Tests.** Derive one test per unsafe action or inadequate feedback: the context (Given),
   the variant of the action or feedback (When — with the concrete payload, timing or
   condition), and the constraint that must hold (Then). Prefer tests at the boundary, against
   the real dependency or a faithful stand-in, over mocks of our own code.
8. **Report** in the format below, in the user's language. Then offer the changes, ordered by
   risk reduced per unit of effort, and implement only the ones the user picks.

**Budget.** At most five boundaries; per boundary, the ten most severe unsafe actions or
feedback problems. Say what was left out and why.

## Report format

````markdown
# Dependency resilience analysis — <project>, <date>

Method: STPA adapted to dependency boundaries (Leveson & Thomas, 2018). Lightweight analysis,
not a safety assessment.

## Summary
<3–5 sentences: the boundaries that can cause the worst losses, the most important scenario,
and the first change to make.>

## Scope
System kind: <…>. Boundaries analyzed: <…>. Left out: <…, and why>.

## Losses and hazards
| ID | Loss | | ID | Hazard (system state) | Leads to |
|---|---|---|---|---|---|

## Control structure
```mermaid
flowchart TB
  %% controllers, control actions (solid), feedback (dashed); mark unknowns
```
| From → To | Control action / feedback | Evidence or "unknown" |
|---|---|---|

## Unsafe control actions and inadequate feedback
| ID | Source | Action / feedback | Type | Context | Hazard |
|---|---|---|---|---|---|

## Loss scenarios
### S1 — <title> (severity: high/medium/low)
<Causal chain in this code, with evidence. What currently stands in the way, if anything.>

## Constraints and mitigations
| Constraint | Mitigation | Prevents | Does not protect against |
|---|---|---|---|

## Tests to add
| For | Given | When | Then |
|---|---|---|---|

## Questions for the team
<Every "unknown" from the control structure that matters to a scenario.>

## Suggested changes
<Ordered. Nothing has been changed yet.>
````

## Excuses that weaken the audit

| The thought | Why it fails |
|---|---|
| "There is a `try/catch` around the call, so the failure is handled." | It reaches only failures on that call's own path. A crash in the dependency's threads, native code or startup passes it by. |
| "The vendor surely rolls out gradually and has an SLA." | The code cannot show it. It is an unknown in the diagram and a question in the report. |
| "To be thorough, every dependency gets an entry." | A long shallow list hides the boundary that can sink the system. Analyze at most five to the end. |
| "The losses are obvious from the code." | Losses are a product decision. Propose them; the user confirms. |
| "A retry fixes it." | Only for failures that pass on their own. Against a malformed configuration or a crash at launch, a retry repeats the loss. Every mitigation says what it does not protect against. |
| "While I am here, I will add the guard." | Report first. The user decides which changes are worth their cost. |

## Signs it went wrong

- A claim about the system with no file and line, and not marked as unknown.
- A mitigation that cannot reach the failure it claims to stop.
- A scenario that leads to no confirmed loss.
- A feedback path never asked "received but not acted on".
- A test that mocks our own code instead of standing in for the dependency.
- Code changed before the user read the report.

## Before you report

Show the evidence; do not describe it:
- The losses as the user confirmed them.
- Every element of the control structure marked evidenced (file:line) or unknown.
- Every loss scenario with its evidence and what currently stands in the way.
- Every mitigation with the scenario it prevents and what it does not protect against.
- The boundaries and actions left out by the budget, and why.
- The questions for the team; nothing changed yet.
