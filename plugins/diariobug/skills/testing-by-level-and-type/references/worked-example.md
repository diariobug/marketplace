# Worked example: a personal-loan application

The example of both articles, based on the banking example of the ISTQB syllabus v3.1
(§2.3.5), adapted by Diario Bug. A bank's mobile app lets a person apply for a personal loan.
The app calculates the monthly payment, checks the applicant's credit history with an
external credit bureau the bank does not control, and approves or declines the application
automatically or sends it to a credit analyst who decides.

**The change:** add the loan application, end to end. **The specification:** the story and
its acceptance criteria (AC1–AC5), the payment formula in the product spec, the bureau's
API documentation, and the response-time requirement for the payment calculation.

## Step 1 — what the change touches

- Components with logic: the payment calculation; the approval rule (approve / decline /
  send to review).
- Internal interfaces: the application screen → the business logic; the logic → the
  applications store.
- External system: the credit bureau's API.
- User-visible flows: applying for a loan; the analyst's review queue.

## The plan

Rows ordered by risk: likelihood in this change and impact on the applicant.

| Risk | What can break | Level | Type | Test | Expected result from |
|---|---|---|---|---|---|
| high | The monthly payment is wrong for some amounts or terms | component | functional, black-box | Equivalence classes and boundaries of amount, rate and term; the calculation alone, rate table stubbed | product spec, payment formula |
| high | The bureau does not answer, answers late or errors | system integration | non-functional (reliability) | Fault-injecting fake: timeout, 5xx, malformed body; the app keeps the application and says what happens next | AC4 |
| high | The amount or term arrives changed at the logic (text vs number, months vs years, cents vs pesos) | component integration | functional | Screen handler + real business logic: what was entered is what is calculated | AC1, interface spec |
| high | What the applicant types changes what the logic does | component integration | non-functional (security) | Hostile input in every field reaches the logic as data | security requirement |
| medium | A branch of the approval rule is never exercised | component | white-box | One case per branch: auto-approve, auto-decline, send to review; decision coverage of the rule | AC3 |
| medium | The bureau answers in a shape we do not expect | system integration | functional | Every query type the app sends, against the bureau's sandbox or a contract double | bureau API docs |
| medium | The application cannot be completed end to end | system | functional | Through the app's own entry point, in the test environment, bureau simulated: apply, receive the folio | AC2 |
| medium | The analyst cannot decide the applications sent to review | acceptance | functional | Executable version of AC5 for the analysts to read and confirm; the acceptance itself is theirs | AC5 |
| low | The calculation is too slow | component | non-functional (performance efficiency: time behavior) | The calculation returns within the specified time | response-time requirement |
| | **Not covered** | | | Usability and accessibility of the analyst's screen (needs the analysts, and a review with assistive technology); the bureau's real production behavior (no access); load at peak hours (no performance requirement written; asked the product owner) | |

**Entry criteria:** the existing suite passed before the change; the bureau's sandbox is available; AC1–AC5 and the payment formula say what each result should be.

**Exit criteria:** every high-risk row covered and passing; every changed branch reached; the open question about the malformed bureau reply answered.

**White-box pass:** the change adds 9 decision outcomes (the approval rule's three
branches, the bureau client's timeout, error-status and malformed-reply handlers and their
success paths). After the tests above, branch coverage of the changed lines reaches all 9;
the one that needed its own test was the malformed-reply handler, whose behavior AC4 did
not state: asked the product owner, who answered "keep the application and retry later".

## What the plan avoided

- Testing the payment formula again through the whole app: the component tests own it.
- An "integration" test of screen → logic with the logic mocked: it would test nothing that
  crosses the interface.
- Only the happy path against the bureau: its failures are the scenario where an applicant
  is left without an answer.
- Expected payments taken from running the calculation: they come from the formula in the
  spec, so a wrong formula in the code fails the test instead of becoming its expectation.
