# Lens: operability

**Finds:** what it will take to deploy, observe, change and undo the design in production, and
what will be hard to diagnose when it misbehaves.

**Applies to:** designs and PRs that deploy something new, migrate data, change configuration,
add feature flags, or are hard to reverse.

## Checklist

1. **Rollout:** how the change reaches production (all at once, staged, behind a flag), and in
   what order its parts must deploy.
2. **Rollback:** how to undo it, and what cannot be undone (data migrations, sent messages,
   external side effects).
3. **Migrations:** data changes are backward compatible during the rollout, can run on the real
   data size, and can be paused or resumed.
4. **Configuration:** new settings are named, have defaults, and are validated at startup.
5. **Feature flags:** who turns them on, what the off state does, and when the flag is removed.
6. **Observability:** the logs, metrics and traces that would show the feature working or
   failing; what an alert would fire on.
7. **Diagnosis:** enough context in errors to find the cause without reproducing it locally.
8. **Runbook:** the manual steps an on-call person may need, written down.
9. **Ownership:** who operates the new component, and who is paged.
10. **Cleanup:** temporary code, flags, data and jobs have an end date.

## Evidence a finding needs

The quoted change, and the operational moment (deployment, the first failure at 3 a.m., the
rollback) the artifact leaves undecided.

## Cannot see

The platform's actual capabilities; check the project's operations docs before flagging a
missing capability.

## Common false positives

Concerns the platform handles for every service (standard logging, deployment pipelines),
when the architecture rules say so.
