# Artifacts and their default lenses

Start from the defaults, then add lenses the risk calls for. A lens left out is listed in the
report with its reason.

| Artifact | What it is for | Default lenses | Add when |
|---|---|---|---|
| **PRD / requirements / user stories** | What to build and why, and how we will know it works | consistency, testability | security: personal data, payments, permissions. performance: stated volumes or response times. traceability: a previous version or a parent document exists |
| **Technical design / TDD / RFC** | How it will be built, and the decisions behind it | testability, architecture, traceability (against its requirements) | security, performance, reliability, operability: by what the design touches (see below) |
| **ADR** | One decision, its context and its consequences | consistency, architecture | traceability: when it supersedes another ADR |
| **Test plan / strategy** | What will be tested, how, and when it is done | testability, traceability (against requirements and design) | consistency for large plans |
| **Pull request (read for intent)** | A change and the reason for it | traceability (against design and requirements) | architecture, security, performance, reliability: by what the change touches. Line-level code correctness is for a code-review tool |
| **A set of artifacts** | Several that must agree | traceability between each pair in the chain | each artifact's own defaults when the user asks for those too |

## Risk triggers

Add a lens when the artifact, or what it changes, involves:

| Trigger | Lens |
|---|---|
| Personal or financial data, authentication, permissions, secrets, input from outside the system | security |
| Stated volumes, latency or throughput, loops over growing data, batch jobs, caches | performance |
| Calls to external services, queues, retries, timeouts, partial failure, money moving | reliability |
| Deployment, migrations, configuration, feature flags, on-call, anything hard to undo | operability |
| A project with written architecture rules, ADRs or conventions | architecture |
| Two or more artifacts that describe the same feature | traceability |

## Lens files

Each lens has its own file in `references/lenses/`, listed in `SKILL.md`.

The quality lenses follow the product quality characteristics of ISO/IEC 25010:2023 (security,
performance efficiency, reliability, maintainability…), so the set can grow by characteristic
rather than by whim. A new lens is a new file with the same sections.
