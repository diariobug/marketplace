# Mitigations, from first principles

Each principle enforces a kind of constraint. Pick by the scenario's causal factor, and say
what each choice does **not** protect against. Notes per system kind are in `system-kinds.md`.

| Causal factor in the scenario | Principles that address it |
|---|---|
| Bad content arrives (malformed, missing, wrong type, oversized) | P1, P4 |
| Failure happens inside code we don't own | P2, P5, P9 |
| Slowness or unavailability | P3, P4, P2 |
| Retries or load amplify the problem | P3 |
| The other side changes (version, schema, deprecation, config) | P7, P1 |
| Credentials, quotas, rate limits, billing | P8 |
| Nobody on our side finds out, or finds out late | P6 |
| People cannot act fast enough | P5, P10 |

## P1. Validate at the boundary, in code you own
Parse what crosses into your code into types you trust, and reject or discard what does not fit:
required fields, types, nulls, encoding, sizes, enumerations. Discard the bad entry, keep the
good ones, continue with a known default.
*Does not protect against:* failures inside the dependency's own parsing — you never see that data.

## P2. Isolate what you don't own
Keep the dependency off the critical path and away from shared resources: start it after the
core flow is usable, give it its own thread pool, connection pool or worker, run it in a
separate process or sandbox (a web worker, an iframe, a sidecar) when the platform allows.
*Does not protect against:* a dependency that, by design, runs in your process and can terminate it — only
P5 stops that one from running.

## P3. Bound waiting and retrying
Every call has a deadline. Retries have a budget, exponential backoff with jitter, and only
repeat operations that are safe to repeat (idempotency keys for anything with side effects).
A dependency that keeps failing stops being called for a while (circuit breaker) instead of
consuming every request's time. An outbound action with an effect — create, charge, send,
upload — must be safe to repeat *or* reconcilable: when the response is lost after the effect
happened, the next attempt must find what the first one did (an idempotency key, a
client-generated id, or looking before creating) instead of doing it again.
*Does not protect against:* wrong content delivered quickly.

## P4. Degrade, don't fail
Decide in advance what the product does without the dependency: a cached last-known-good value,
a default, the feature hidden, a queued operation completed later. The core function survives.
*Does not protect against:* losing a dependency the core function cannot work without (payments for checkout) —
there, fail clearly and safely instead.

## P5. Own the off switch
A switch you control — remote config of your own, a server-side flag — read *before* the
dependency starts, that lets you not start or not call it without shipping a release.
*Does not protect against:* a failure that happens before your switch can be read, or a one-time failure
that is over before anyone flips it.

## P6. Own your feedback
Measure the dependency's effect on *your* users from *your* side: errors, crashes, latency and
success rate per dependency; synthetic checks of the real integration; alerts routed to people
who can act. A vendor's status page tells you about the vendor, not about your users. Account
for delay: some signals (mobile crash reports, batch metrics) arrive after the fact.
*Does not protect against:* any failure on its own — it shortens the time to know, which is what makes P5 and P10 work.

## P7. Control your exposure to change
Pin versions and commit lockfiles; upgrade deliberately, behind tests and a staged rollout;
contract tests that fail when the provider's format changes; watch deprecation notices.
*Does not protect against:* server-side changes — remote config, API behavior — that reach you without any
upgrade. Pinning the client version protects nothing there.

## P8. Treat credentials and limits as dependencies
Tokens and certificates expire; quotas and rate limits run out; billing stops. Alert before
expiry, handle rate-limit responses (429, `Retry-After`) explicitly, know your quota headroom.

## P9. Know what you cannot catch
List the failure modes no code of yours can intercept: crashes in the dependency's own threads,
native crashes, out-of-memory, process exit, a blocked main thread. Only isolation (P2) and not
running the dependency (P5) help against those. Saying so prevents false protection — a
`try/catch` that cannot reach the failure.

## P10. Prepare the human response
Make the dependency identifiable in stack traces and logs; know where the vendor reports
incidents; decide in advance who chooses between waiting, switching off and releasing — and how
long a release takes on this platform.

## If you are the vendor
The same analysis, from the other side of the boundary:
- test a change from the client's side — the real client processing the new response — not
  only on the server that produces it;
- block changes that leave references the client still depends on;
- make the client discard a malformed entry and continue;
- roll configuration out to a fraction of clients first, watching client-side outcomes;
- measure what clients experience, not only server health.
