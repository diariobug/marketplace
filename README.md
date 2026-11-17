**English** | [Español](README.es.md)

# Diario Bug marketplace

Testing skills from [Diario Bug](https://diariobug.com) for your coding agent.
Free for everyone. Subscribers get the kits with the method guide and hear
first about new skills: [diariobug.com/recursos/](https://diariobug.com/recursos/).

## Skills

**Testing activities** — they apply to any kind of testing.

- **test-planning** — the entry point. Decides what testing a change needs by
  test level (component, integration, system, acceptance) and type
  (functional, non-functional, black-box, white-box), and hands parts to the
  other skills. Plans new tests, reviews a PR's tests, assesses a module's
  coverage. Ask "is this PR tested enough?" or "plan the tests for this change".
- **test-design** — design the cases themselves: equivalence partitions,
  boundary values, decision tables, state transitions, branch coverage read as
  a map, error guessing, exploratory charters. Chooses the technique from the
  shape of the rule, asks about the gaps the design exposes, and takes every
  expected result from the requirements, never from the code. Ask "what inputs
  should I test for this?" or "design the tests for this rule".
- **spec-review** — review a requirements document, technical design, ADR,
  test plan or PR description through focused lenses (consistency,
  testability, traceability, architecture conformance, security, performance,
  reliability, operability), then triage: at most five blocking findings for
  the author and a few questions for the human review. Ask "review this PRD"
  or "check this design against its requirements".
- **regression-strategy** — decide which tests run where, when and how, so a
  growing suite stays fast, stable and trusted. Assigns each test to a gate,
  picks the simplest selection mechanism that works at your scale, and sets
  health rules for flaky and slow tests.

**Specific risks and test types** — what one kind of testing needs beyond the
activities.

- **dependency-failures** — how your system can fail through what it does not
  control (SDKs, APIs, cloud services, third-party scripts), with an
  STPA-based boundary analysis. Produces loss scenarios backed by code
  evidence, mitigations, and the tests to add.

New skills ship with the articles that teach them; subscribers hear first.

**Renamed in 1.4.0:** `testing-by-level-and-type` → `test-planning`,
`test-design-techniques` → `test-design`, `artifact-review` → `spec-review`,
`dependency-resilience-audit` → `dependency-failures`. Claude Code picks up
the new names on update; with `npx skills` or Cline, remove the old folders
and add the new ones.

## Install (Claude Code)

```sh
/plugin marketplace add diariobug/marketplace
/plugin install diariobug
```

Update later with `/plugin update diariobug`. Skills are namespaced:
`/diariobug:test-planning`, `/diariobug:test-design`, `/diariobug:spec-review`,
`/diariobug:regression-strategy`, `/diariobug:dependency-failures`. Add a
workflow after the name: `/diariobug:test-planning review`.

## Install (Cursor, Codex, Gemini CLI, Copilot and other agents)

The same skill folders work anywhere `SKILL.md` is supported. With the
[`skills` CLI](https://github.com/vercel-labs/skills), which installs into
most coding agents:

```sh
npx skills add diariobug/marketplace                                  # choose skills and agents
npx skills add diariobug/marketplace --skill test-planning
```

With Cline (it installs through the same CLI):

```sh
cline skill add diariobug/marketplace -g --skill '*' -y    # all skills, for your user
cline skill update -g                                     # later, to update
```

No marketplace support? Copy the skill folder into your agent's skills
directory, or paste `SKILL.md` plus its `references/` and `workflows/` into
your project's instructions. The subscriber kits on diariobug.com bundle the
same folders with a PDF method guide.

## En español

Este README está en español, completo, en [README.es.md](README.es.md).

## Privacy

The plugin makes no network calls. No telemetry, no tracking — it only reads
the project you point it at.

## License

CC BY-NC-SA 4.0 — share and adapt non-commercially, crediting Diario Bug,
under the same license. Full text:
https://creativecommons.org/licenses/by-nc-sa/4.0/deed.en
