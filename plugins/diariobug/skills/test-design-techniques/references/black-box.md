# Black-box techniques

Black-box cases come from the specified behavior, without reference to the internal
structure, so they stay useful when the implementation changes and the behavior does not
(§4.1).

## Contents

- Equivalence partitioning
- Boundary value analysis
- Decision tables
- State transitions

## Equivalence partitioning (§4.2.1)

**For:** any data element the rule treats in groups: inputs, outputs, configuration values,
time values, interface parameters. One value from a partition stands for all of it, because
the rule is expected to process them the same way.

**Finding the partitions:**
1. For each data element, list the groups the test basis treats differently. Read for "if",
   "when", "only", "except", "between", "at least", and for every outcome the rule can give.
2. Add the invalid partitions: values the rule must reject or ignore, and values the test basis
   does not mention. "Not mentioned" is a question, not an assumption.
3. Check the partitions: none empty, none overlapping. A value that fits two partitions is an
   ambiguity in the test basis.
4. Remember the elements no one types: the output's partitions (each kind of message or
   result), the current time, a configuration flag, the type and the absence of a value
   (empty, null, missing field).

**Coverage:** partitions exercised over partitions identified, invalid ones included. With
several elements, each-choice coverage exercises every partition of every element at least
once, and says nothing about combinations.

**Example.** A username must have 3 to 20 characters, letters, digits and underscore only.
- Length: P1 shorter than 3 (invalid), P2 3–20 (valid), P3 longer than 20 (invalid), P4 empty
  (invalid; is it a different message? ask).
- Characters: P5 allowed set (valid), P6 a space, P7 another symbol, P8 letters outside a–z
  ("ñ", "é": allowed or not? ask).
- Four of these are questions before they are cases. That is the technique working.

**What it cannot see:** values inside a partition the code treats differently without the test
basis saying so (white-box finds those), and combinations of partitions.

## Boundary value analysis (§4.2.2)

**For:** ordered partitions only. The minimum and maximum values of each partition are its
boundary values; defects cluster there because a boundary gets implemented one step too high,
one step too low, or not at all.

**Finding the boundaries:**
1. Start from the ordered partitions. For each boundary, find the smallest step of the value:
   1 for whole numbers, 0.01 for money in cents, one day, one second, one character. The step
   decides the neighbor, and the test basis often does not say it (is "7 days" counted in
   calendar days or in hours, and in whose time zone?): ask.
2. **2-value:** each boundary value and its closest neighbor in the adjacent partition.
3. **3-value:** each boundary value and both neighbors. It catches defects 2-value can miss: if
   "x ≤ 10" is implemented as "x = 10", the 2-value cases 10 and 11 pass and 9 fails (the
   syllabus's own example).

**Example.** The username's length partitions P1 (0–2), P2 (3–20), P3 (21+). 2-value: 2, 3, 20,
21. 3-value adds 4 and 19. The empty name (0) stays its own case if it gets its own message.

**Common mistakes:** taking the boundary from the code; testing only the valid side; forgetting
the boundaries of outputs (the last amount that gets free shipping) and of time (midnight, the
last day of the month, a leap day); using a step finer than the data allows.

**What it cannot see:** a rule with no threshold, and a threshold the test basis forgot.

## Decision tables (§4.2.3)

**For:** requirements where combinations of conditions give different outcomes, such as
business rules, eligibility, pricing, permissions.

**Building the table:**
1. Conditions as rows (each true/false, or a set of partitions in an extended-entry table),
   then actions as rows.
2. One column per combination. With n true/false conditions, a full table has 2ⁿ columns.
3. Mark infeasible combinations "N/A" and remove them; the reason each is infeasible is worth
   writing down, because "impossible" is sometimes only "not yet seen".
4. For every remaining column, the test basis must give the actions. A column it does not
   answer is a gap; a column it answers twice, differently, is a contradiction. Both go to the
   user as questions with that column as the example.
5. Minimize only after the table is complete: merge columns where a condition does not change
   the outcome ("–"), and list what was merged.

**Coverage:** feasible columns exercised over feasible columns. When the conditions are many
and the columns grow exponentially, use a minimized table or rank columns by risk, and list the
columns not exercised.

**Example.** Free shipping when the order is at least 50 or the customer is a member, except for
oversized items, which never ship free.

| Conditions | R1 | R2 | R3 | R4 | R5 |
|---|---|---|---|---|---|
| Oversized item | T | F | F | F | F |
| Order ≥ 50 | – | T | T | F | F |
| Member | – | T | F | T | F |
| **Free shipping** | | X | X | X | |

R1 merges four columns because "oversized" decides alone, but only if the test basis says so:
does a member's oversized order of 200 really pay shipping? The table makes that question
visible. "Order ≥ 50" is a boundary too: 49.99 and 50.00 belong to the cases of R2–R5.

**What it cannot see:** conditions nobody listed, and the order of events (that is a state
model).

## State transitions (§4.2.4)

**For:** anything whose response to an event depends on what happened before: orders, payments,
tickets, subscriptions, sessions, approval flows, devices.

**Building the model:**
1. The states, from the test basis and the user's vocabulary.
2. The events, including the ones from outside the happy path: cancel, retry, time out, pay
   twice, go back, a second tab, a webhook arriving late.
3. The valid transitions as "event [guard] / action".
4. The **state table**: states as rows, events as columns. Every empty cell is an invalid
   transition, and the test basis must say what happens there (rejected with which message?
   ignored? logged?). Empty cells are the questions this technique exists to raise.

**Coverage criteria, weakest to strongest:**
- All states: every state visited at least once.
- Valid transitions (0-switch): every valid transition exercised; the most widely used.
- All transitions: every valid transition, and an attempt at every invalid one, **one invalid
  transition per case** so one defect does not mask another.

**Example.** A ticket: Purchased, Transferred, Used, Refunded. Events: transfer, scan at the
door, request refund. Questions the table raises at once: can a Transferred ticket be
refunded, and to whom? What does scanning a Refunded ticket do at the door? Each answer is a
case, and so is each "it cannot happen".

**What it cannot see:** states the model left out (a payment "pending" between purchased and
paid is the classic one), and timing between events unless the model includes time.
