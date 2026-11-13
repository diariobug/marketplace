# Choosing a technique by the shape of what is tested

A feature is rarely one shape. A refund rule has a range (days before the event), categories
(ticket type), conditions that combine (event cancelled, ticket transferred) and a life cycle
(purchased, used, refunded). Split it into parts, then match each part to a shape.

| Shape of the part | Ask | Technique | Coverage items |
|---|---|---|---|
| Values processed the same way within groups (types, categories, formats) | "Which values does the rule treat alike?" | Equivalence partitioning (§4.2.1) | Partitions, valid and invalid |
| Ordered values with thresholds (ages, amounts, dates, lengths, counts) | "Where does the treatment change?" | Equivalence partitioning + boundary value analysis (§4.2.2) | Boundary values (and their neighbors, for 3-value) |
| Several conditions whose combination decides the outcome | "Does the outcome depend on how the conditions combine?" | Decision table (§4.2.3) | Feasible columns (rules) |
| Behavior that depends on what happened before (orders, sessions, approvals, subscriptions) | "Does the same event give a different result depending on the history?" | State transition (§4.2.4) | States, valid transitions, invalid transitions |
| Code exists, and the test basis is vague, old or incomplete | "Which paths did the black-box cases never take?" | Branch coverage, read as a map (§4.3) | Branches |
| Known ways this kind of software fails | "What has gone wrong here, or in systems like it?" | Error guessing, fault attacks (§4.4.1) | The failures on the list |
| Little specification, a new area, or what a person experiences (clarity, feel, flow) | "What do we not know yet?" | Exploratory testing with a charter (§4.4.2) | The charter's objectives |
| Recurring conditions worth checking every time (accessibility, usability, a team's past defects) | "What must never be forgotten here?" | Checklist-based testing (§4.4.3) | The checklist items |

## How the techniques combine

- **Partitions come first.** Boundary values are the edges of ordered partitions, and a
  decision table's conditions often take their values from partitions (an extended-entry
  table, §4.2.3).
- **A decision table's columns become partitions to test,** and each column's numeric
  condition still needs its boundaries.
- **A state transition's guard conditions** ("cancel [less than 24 h before departure]") are
  ranges too: their boundaries are cases.
- **Black-box first, then white-box.** Design from the test basis, run with branch coverage,
  and read what was not reached. The reverse order designs cases that follow the code, defects
  included.
- **Experience-based techniques complement, never replace,** the systematic ones (§4.1). They
  find what the specification did not imagine; the systematic ones show what was covered.

## Choosing the rigor by risk

The rigor of each technique is a decision taken from the risk the part carries (likelihood and
impact). Defaults, from Diario Bug's experience, to change when the team's risk appetite says
otherwise:

| Risk of the part | Partitions and boundaries | Decision table | State transition |
|---|---|---|---|
| High (money, safety, data loss, legal) | 3-value boundaries | Every feasible column | All transitions, invalid included |
| Medium | 2-value boundaries | Minimized table; merged columns listed | Valid transitions, plus the invalid ones a real client can send |
| Low | Each partition once (each-choice) | The columns for the main outcomes | All states |

The syllabus sets one minimum of its own: all transitions coverage "should be a minimum
requirement for mission and safety-critical software" (§4.2.4).
