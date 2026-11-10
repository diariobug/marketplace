# Lens: reliability

**Finds:** how the design behaves when parts of it, or what it depends on, fail: the
reliability characteristic of ISO/IEC 25010:2023 (faultlessness, availability, fault
tolerance, recoverability).

**Applies to:** artifacts with calls to external services, queues, scheduled jobs, money or
state that must not be lost or duplicated, availability targets.

## Checklist

1. **Dependencies:** every external service, store and queue the design relies on is named,
   with what happens when it is slow, down, or returns something unexpected.
2. **Timeouts:** every remote call has one, and the design says what follows it.
3. **Retries:** which operations are retried, how many times, with what backoff, and whether
   a retry can duplicate an effect.
4. **Idempotency:** operations that may run twice (retries, redelivered messages, double
   submits) produce the same result; payments and messages especially.
5. **Partial failure:** a multi-step operation that fails halfway leaves a state the design
   describes and can recover from.
6. **Degraded mode:** what the user sees when a dependency is unavailable; whether the
   feature degrades or the whole flow fails.
7. **Data durability:** what is lost if a process stops at the wrong moment; backups and
   restore for new data stores.
8. **Fallbacks:** each fallback is visible (logged, alerted), not silent, and does not replace
   work it cannot equal.
9. **Availability targets:** stated, and consistent with the dependencies' own.
10. **Recovery:** how the system returns to normal after an outage (backlog drain, replays,
    manual steps).

## Evidence a finding needs

The quoted call or step, and the concrete failure ("the provider times out after the charge
succeeds") the design does not decide.

## Cannot see

How the real dependencies behave. For a deeper analysis of each dependency boundary, use the
`dependency-resilience-audit` skill in this plugin.

## Common false positives

Resilience provided by a shared client or platform the rules describe; failure handling
deliberately delegated to the caller and documented.
