# Test levels and test types: what each one sees

Sources: ISTQB Certified Tester Foundation Level syllabus v4.0.1 (2024), §2.2, for the five
levels and four types; its previous edition, v3.1 (2019), §2.2–2.3, for the per-level test
basis, objects and typical defects, which v4.0.1 keeps as attributes but no longer lists.
The "In code" and "Cannot see" lines are this kit's reading for software projects, not
syllabus text.

## Contents

- The five levels: component; component integration; system; system integration;
  acceptance. For each: focus, test basis, typical defects, in code, cannot see.
- The four types: functional, non-functional, black-box, white-box; confirmation and
  regression.
- Placing a risk: quick guide.

## The five levels

### Component (unit) testing

- **Focus:** "testing components in isolation. It often requires specific support, such as
  test harnesses or unit test frameworks. Component testing is normally performed by
  developers in their development environments" (v4.0.1).
- **Test basis:** detailed design, code, data model, component specifications (v3.1).
- **Typical defects:** incorrect functionality (not as in the design), data flow problems,
  incorrect code and logic (v3.1).
- **In code:** one function, class or module; collaborators replaced by stubs (they answer
  with predefined responses) and the caller by a driver or the test itself. No real
  database, network, file system or clock.
- **Cannot see:** whether the component and its neighbors agree on data, units, order or
  errors; anything about the deployed system.

### Component integration testing

- **Focus:** "the interfaces and interactions between components. Component integration
  testing is heavily dependent on the integration strategy like bottom-up, top-down or
  big-bang" (v4.0.1). Tests "should concentrate on the integration itself", not on the
  functionality of each component (v3.1).
- **Test basis:** software and system design, sequence diagrams, interface and protocol
  specifications, architecture, workflows (v3.1).
- **Typical defects:** incorrect, missing or wrongly encoded data; incorrect sequencing or
  timing of interface calls; interface mismatch; unhandled communication failures;
  incorrect assumptions about the meaning, units or boundaries of the data passed (v3.1).
- **In code:** real collaborators inside the system: handler and service, service and
  repository, repository and a real (test) database, producer and consumer of an internal
  queue.
- **Cannot see:** whether the whole flow works for a user; how external systems behave.

### System testing

- **Focus:** "the overall behavior and capabilities of an entire system or product, often
  including functional testing of end-to-end tasks and the non-functional testing of quality
  characteristics" (v4.0.1). Some characteristics are best tested on the complete system in
  a representative environment, usability for instance (v4.0.1).
- **Test basis:** system and software requirements, risk analysis, use cases, epics and user
  stories, models of behavior, system and user manuals (v3.1).
- **Typical defects:** incorrect calculations; incorrect or unexpected functional or
  non-functional behavior; incorrect control or data flows; failure to carry out end-to-end
  tasks; failure to work in the system environment or as the manuals say (v3.1).
- **In code:** the system through its own entry point (public API, CLI, UI), deployed the
  way the project deploys for tests, with external systems simulated where needed.
- **Cannot see:** which component caused a failure (it must be traced); real external
  systems when they are simulated.

### System integration testing

- **Focus:** "the interfaces of the system under test and other systems and external
  services. System integration testing requires suitable test environments preferably
  similar to the operational environment" (v4.0.1). When the other side belongs to another
  organization, "the developing organization does not control the external interfaces"
  (v3.1).
- **Test basis:** external interface definitions, communication protocol specifications,
  the other system's contract (v3.1).
- **Typical defects:** inconsistent message structures between systems; incorrect, missing
  or wrongly encoded data; interface mismatch; failures in communication and unhandled
  communication failures; wrong assumptions about the data passed; failure to comply with
  mandatory security regulations (v3.1).
- **In code:** against the provider's sandbox, a contract test, a recorded double, or a
  fake that injects faults: timeouts, error statuses, malformed and unexpected responses.
- **Cannot see:** whether the business process is the right one; the other system's
  internals.

### Acceptance testing

- **Focus:** "validation and … demonstrating readiness for deployment, which means that the
  system fulfills the user's business needs. Ideally, acceptance testing should be performed
  by the intended users" (v4.0.1). Its forms: user acceptance, operational acceptance,
  contractual and regulatory acceptance, alpha and beta testing.
- **Test basis:** business processes, user or business requirements, regulations and
  contracts, use cases and user stories, documentation, installation procedures (v3.1).
- **Typical defects:** workflows that do not meet business or user requirements; business
  rules implemented incorrectly; contractual or regulatory requirements not met;
  non-functional failures such as security vulnerabilities or poor performance under load
  (v3.1). Finding many defects here "may in some cases be considered a major project risk"
  (v3.1).
- **In code:** executable acceptance criteria the users can read (see ATDD/BDD); the
  acceptance itself is the users'.
- **Cannot see:** which piece is at fault; it confirms fitness, it does not diagnose.

## The four types

| Type | Evaluates | Tests come from | Note |
|---|---|---|---|
| Functional | "what" the test object should do: functional completeness, correctness, appropriateness | requirements, stories, specs | |
| Non-functional | "how well the system behaves": a quality characteristic | the requirement for that characteristic | ISO/IEC 25010:2023 lists performance efficiency, compatibility, interaction capability (usability), reliability, security, maintainability, flexibility (portability), safety. Often a functional test plus a constraint (v4.0.1) |
| Black-box | behavior against the specification | documentation not related to the internal structure | equivalence partitioning, boundary values, decision tables, state transitions |
| White-box | coverage of the structure | code, architecture, work flows, data flows | statement and decision coverage at component level; interfaces exercised at integration level (v3.1) |

Confirmation and regression testing are not test types in v4.0.1 (§2.2.3): they follow a
change. Confirmation checks that a defect was fixed; regression checks that the change broke
nothing else, in the same component, other components or connected systems.

## Placing a risk: quick guide

| The risk is about… | Earliest level | Usual type |
|---|---|---|
| a calculation, a rule, a branch inside one unit | component | functional black-box + white-box for new branches |
| a time limit or resource use of one unit | component | non-functional (performance efficiency) |
| data crossing between two parts of our system | component integration | functional; non-functional (security) for input handled across the boundary |
| a user task end to end, supported devices or platforms | system | functional; non-functional (compatibility, flexibility) |
| an external system's answers, errors, timeouts | system integration | functional + non-functional (reliability) |
| whether the business accepts the result | acceptance (the users') | executable criteria only |

Some risks are cheaper to find without running anything (static testing, §3.1). A requirement
two readers will build differently goes to a review of the document (`spec-review`, when it
is installed). In code, unused inputs, unreachable code, wrong types and broken conventions
are found by the project's linter, type checker or static analyzer, at no test's cost; plan
them as a check that runs, not as tests. Tune the analyzer's rules rather than accepting its
noise: a check that raises many false positives teaches the team to ignore it.
