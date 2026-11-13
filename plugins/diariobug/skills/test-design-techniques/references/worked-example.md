# Worked example: event-ticket refunds

An example by Diario Bug. A ticketing app lets buyers request a refund. The answers the user
gives below are illustrative; in a real run they come from the user, never from the agent.

## Step 1 — test object and test basis

The refund request, from the request to the ticket's new state. The test basis is the story's
acceptance criteria:

- **AC1.** Standard tickets: 100% refund up to 30 days before the event; 50% from 29 to 7
  days; no refund with less than 7 days.
- **AC2.** VIP tickets: 100% refund up to 7 days before the event; no refund with less than 7.
- **AC3.** If the organizer cancels the event, every ticket gets 100%.
- **AC4.** The service fee is not refunded.
- **AC5.** Used tickets cannot be refunded. A transferred ticket can be refunded only by its
  current holder.

## Step 2 — parts and techniques

| Part | Shape | Technique | Why |
|---|---|---|---|
| Days before the event | Ordered values with thresholds | Partitions + 3-value boundaries | Money: a boundary one day off pays the wrong amount (high risk) |
| Ticket type × event status × days band | Conditions that combine into an outcome | Decision table, every feasible column | AC1–AC4 interact; the combinations decide the amount |
| The ticket's life (purchased, transferred, used, refunded) | Behavior depends on history | State transitions, all transitions | Double refunds and refunds of used tickets are the costly failures |

## Step 3 — coverage items (excerpt)

Partitions of "days before the event" for a standard ticket, scheduled event:

| ID | Partition | Outcome | Source |
|---|---|---|---|
| P1 | 30 days or more | 100% | AC1 |
| P2 | 7 to 29 days | 50% | AC1 |
| P3 | 0 to 6 days, before the start | no refund | AC1 |
| P4 | after the event started | ? | none: question Q2 |

Boundaries (3-value): around 30 → 29, 30, 31; around 7 → 6, 7, 8; around the start → one minute
before and at the start.

## Step 4 — questions the design raised

| Q | Example | Proposed answer | User's answer |
|---|---|---|---|
| Q1 | Request on March 1 at 23:00 for an event on March 31 at 20:00: 30 days or 29? | Calendar days in the event's time zone: 30 | Confirmed |
| Q2 | Request 10 minutes after the event started: "no refund", or rejected with another message? | Rejected: "the event already started" | Confirmed |
| Q3 | Cancelled event: is the service fee refunded too? AC3 says 100%, AC4 says never. | Fee refunded when the organizer cancels | Fee refunded; AC4 to be reworded |
| Q4 | Original buyer requests a refund of a ticket they transferred: rejected, or refunded to them? | Rejected; only the holder | Confirmed |
| Q5 | The scanner reads a refunded ticket at the door: what does staff see? | "Refunded: not valid" | Confirmed |

Q3 is a contradiction between two criteria; the decision table found it. Q5 is an invalid
transition nobody had specified; the state table found it.

## Step 5 — the decision table

Extended entry: the days band takes the partitions of step 3.

| Conditions | R1 | R2 | R3 | R4 | R5 | R6 |
|---|---|---|---|---|---|---|
| Event cancelled | T | F | F | F | F | F |
| Ticket type | – | Std | Std | Std | VIP | VIP |
| Days band | – | ≥30 | 7–29 | <7 | ≥7 | <7 |
| **Refund** | 100% | 100% | 50% | none | 100% | none |
| **Fee refunded** | X (Q3) | | | | | |

R1 merges every ticket type and band because AC3 decides alone; R5 merges two bands because AC2
treats them alike. Both merges are listed under "not designed": a VIP ticket at 29 days is
covered by the boundary cases, not by its own column.

## Step 5 — the state table

| State \ Event | transfer | scan at the door | refund |
|---|---|---|---|
| Purchased | → Transferred | → Used | → Refunded |
| Transferred | → Transferred (new holder) | → Used | → Refunded [current holder] |
| Used | invalid | invalid (second entry) | invalid (AC5) |
| Refunded | invalid | invalid (Q5) | invalid (double refund) |

6 valid transitions, 6 invalid ones. All transitions coverage, each invalid one in its own
case.

## Steps 5–6 — cases (excerpt)

| Case | Inputs / steps | Expected result | Source | Covers | Risk |
|---|---|---|---|---|---|
| C1 | Standard, scheduled, 30 days | 100%, fee kept | AC1, AC4, Q1 | P1, B30, R2 | high |
| C2 | Standard, scheduled, 29 days | 50% | AC1 | P2, B29, R3 | high |
| C3 | Standard, scheduled, 7 days | 50% | AC1 | B7 | high |
| C4 | Standard, scheduled, 6 days | No refund | AC1 | P3, B6, R4 | high |
| C5 | Any ticket, 10 min after start | Rejected: "the event already started" | Q2 | P4 | medium |
| C6 | VIP, cancelled event, 2 days | 100% and fee | AC3, Q3 | R1 | high |
| C7 | Purchased → refund → refund again | Second request rejected; one payment only | AC5 (state table) | invalid Refunded/refund | high |
| C8 | Purchased → scan → refund | Rejected | AC5 | invalid Used/refund | high |
| C9 | Purchased → transfer → refund by original buyer | Rejected | Q4 | guard of Transferred/refund | high |
| C10 | Purchased → refund → scan | "Refunded: not valid" | Q5 | invalid Refunded/scan | medium |

Expected results were written from the criteria and the answers, before reading the refund
code.

## Step 7 — coverage

| Technique | Items identified | Exercised | Coverage |
|---|---|---|---|
| Partitions (days) | 4 | 4 | 100% |
| 3-value boundaries | 8 | 8 | 100% |
| Decision table | 6 feasible columns | 6 | 100% |
| State transitions | 12 (6 valid, 6 invalid) | 12 | 100% |

Branch coverage after running them: one branch not reached, `if ticket.promo_code` in the
refund calculation. No criterion mentions promotional codes: question for the user, with the
example "a standard ticket bought with a 20% code, refunded at 30 days: 100% of the price paid,
or of the list price?"

## Not designed, and why

- VIP tickets in the 7–29 band have no column of their own: AC2 treats them like ≥30, and the
  boundary cases at 7 cover the change. Revisit if AC2 changes.
- Combinations of transfer with cancellation: low risk per the user, left out.
- Rescheduled events: not in the test basis; asked as a question in the report, no cases until it is answered.
