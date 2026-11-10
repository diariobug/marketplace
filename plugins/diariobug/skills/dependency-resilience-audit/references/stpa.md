# STPA, adapted to dependency boundaries

STPA (System-Theoretic Process Analysis) comes from N. Leveson and J. Thomas, *STPA Handbook*
(MIT, 2018). Its premise: losses in complex systems arise from **inadequate control**, often
with no component failing at all, so the analysis models control loops rather than parts. The
handbook has four steps — define the purpose (losses, hazards), model the control structure,
identify unsafe control actions, identify loss scenarios. This file adapts each one to the
boundary between a system and the things it depends on.

## Vocabulary

- **Loss** — something unacceptable to the people who own the system: users cannot do the core
  task, data is lost or corrupted, money is mishandled, private data leaks.
- **Hazard** — a system state that, under bad-enough conditions, leads to a loss. It names the
  system's state, never a component failure: "the app terminates during launch", not "the SDK
  throws".
- **Controller** — anything that decides and acts: a module of ours, the dependency, the
  vendor's backend, a person (the on-call engineer, the vendor's release engineer).
- **Control action** — what a controller sends to change what another does: a call, a request,
  an initialization, a configuration or flag push, a command, a deployment.
- **Feedback** — what a controller learns about the process it controls: a response, an error,
  a callback, a webhook, a metric, a crash report, a dashboard, an alert.
- **Process model** — what a controller *believes* about the process it controls: "the payload
  is well formed", "the service is healthy", "this call is idempotent". Most scenarios are a
  process model that stopped matching reality, and feedback that did not correct it in time.

## Step 3 — the control structure

Draw controllers as boxes, control actions as solid arrows down, feedback as dashed arrows up.
At a typical dependency boundary there are **two loops nested**:

```
Vendor's people ──(change, release)──▶ Vendor backend ──(config, flags, responses)──▶ Dependency
                                                                                    (runs in or
Our people ──(deploy, switch off)──▶ Our code ──(init, calls)──▶ Dependency          beside us)
     ▲ alerts, dashboards (dashed)     ▲ results, errors, callbacks (dashed)
```

Questions that build it from code:

- Where is the dependency initialized and called? On which thread, queue or process?
- What does it receive from outside that changes its behavior — remote config, flags, rules,
  models, schemas — and who controls that?
- What does our code receive back, and where is it parsed? By whom: our code or the
  dependency's?
- What feedback reaches *people* on our side — which alerts, which dashboards — and does any of
  it measure the dependency's effect on *our* users rather than the vendor's health?
- What control do our people have — a switch, a config, only a new release? How long does each
  take?

Mark anything not visible in the repository as **unknown**. Unknowns that sit in a scenario's
causal chain become questions for the team.

## Step 4 — unsafe control actions (UCA)

For every control action, in its context, ask the four STPA questions:

| Type | Question | Software examples |
|---|---|---|
| **Not provided** | Does *not* sending it lead to a hazard? | no timeout fires; the kill switch is never consulted; the retry that would recover never happens |
| **Provided, unsafe** | Does sending it — or sending it with this content — lead to a hazard? | a config push with an entry missing a required field; a retry of a non-idempotent charge; initializing the SDK when it is known to be broken |
| **Wrong timing or order** | Too early, too late, out of order? | the SDK initialized before the app is usable; a webhook processed before the record it refers to exists; a token used after it expired |
| **Stopped too soon / applied too long** | For continuous actions | retries that never stop; a circuit open forever; a sync loop holding a lock; polling that outlives the screen |

Write each as: **<source> <type> <action> when <context> → <hazard>**.
Example: *Vendor backend provides a flag list with an unnamed entry when the SDK stores flags
by name → H1 (app terminates during launch).*

### Inadequate feedback

The handbook treats feedback inside loss scenarios; at dependency boundaries it deserves its own
pass, because this is where most incidents hide. For every feedback path ask:

| Type | Question | Software examples |
|---|---|---|
| **Missing** | Does the controller ever learn this? | no alert on crash rate per dependency; vendor dashboard watches servers only |
| **Wrong or corrupt** | Can it arrive malformed, misleading or for the wrong thing? | a success status with invalid content; a health check that passes while users fail |
| **Late or out of order** | Does it arrive after it was needed? | crash reports uploaded on the next launch; metrics aggregated hourly |
| **Stale or stops** | Can the controller keep acting on an old value? | cached last-known config never refreshed; a silent webhook stream taken as "nothing happened" |
| **Received but not acted on** | Does the signal arrive and change nothing? | a truncation warning logged while the cut output is used; a cost total printed with no budget; a fallback recorded in a log line nobody reads |

The last row is the commonest in practice: the signal exists, so the team believes it is
covered, but no control action is connected to it.

### Replies are feedback

A response our code acts on — an API body, a model's completion, a config payload the
dependency hands back — is **feedback to our controller**, not a control action from the
dependency. Analyze it with the feedback types above; the unsafe control action is what *our*
controller then does with it (saves it, parses it, overwrites something). Treating the reply as
the dependency commanding us sends the analysis in circles. The exception is a dependency that
genuinely directs another one — a vendor backend pushing configuration to its SDK — which is a
control action between two controllers we don't own.

## Step 5 — loss scenarios

For each UCA or inadequate feedback, answer two questions with evidence from the code:

1. **Why would the controller do this?** Its algorithm (no validation; retries without limit),
   its process model (assumes well-formed data, assumes the vendor is healthy), its inputs and
   feedback (missing, wrong, late).
2. **Why would a correct action still go wrong?** The control path (the work runs on a
   background queue where no caller can catch the failure; the call crosses a process
   boundary), the controlled process (the dependency's own behavior; its sub-dependencies),
   the feedback path (the signal exists but nobody watches it).

Then: what stands in the way today, if anything, and how severe the resulting loss is.

## Worked example (compressed)

Google Analytics for Firebase on iOS, September 28, 2026 (Firebase's post-incident report and
the incident's GitHub issues).

- **Loss:** users cannot use the app. **Hazard H1:** the app terminates during launch.
- **Structure:** Firebase engineers → (flag change) → Firebase backend → (flag list) → Analytics
  SDK, inside the app's process → (store flags) → GoogleUtilities dictionary, on a background
  queue. Feedback: Firebase's status dashboard (server health only); app developers' crash
  alerts (delayed: reports upload on next launch); GitHub issues.
- **UCA:** backend *provides* a flag list with an unnamed entry when the SDK keys flags by name
  → H1. Engineers *provide* a flag removal while the SDK still references the flag → H1.
- **Inadequate feedback:** Firebase's dashboard *wrong for the purpose* (healthy servers,
  crashing clients); developers' crash data *late*.
- **Scenario:** the SDK's process model assumed every flag has a name; no validation; the store
  ran on a background queue, so the app's code could not catch the exception; every SDK version
  was affected and the config went to everyone at once.
- **Constraints → mitigations:** the SDK must ignore a malformed entry (validate in owned code —
  the vendor's); the app must be able to not start a component without a release (own switch);
  the app team must learn of crashes from its own signals (own feedback); a config change must
  reach a fraction of clients first (vendor rollout).
- **Test the UCA yields:** given a clean app that only initializes the SDK, when the flag list
  contains an entry without a name, then the app keeps running. Developers reproduced exactly
  this: three crashes on three fresh installs.
