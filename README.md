# Diario Bug marketplace

Testing skills from [Diario Bug](https://diariobug.com) for your coding agent.
Free for everyone. Subscribers get the kits with the method guide and hear
first about new skills: [diariobug.com/recursos/](https://diariobug.com/recursos/).

## Skills

- **testing-by-level-and-type** — decide what testing a change needs by test
  level (component, integration, system, acceptance) and type (functional,
  non-functional, black-box, white-box). Plans new tests, reviews a PR's
  tests, assesses a module's coverage. Ask your agent things like "is this PR
  tested enough?" or "plan the tests for this change".
- **test-design-techniques** — design the cases themselves: equivalence
  partitions, boundary values, decision tables, state transitions, branch
  coverage read as a map, error guessing, exploratory charters. Chooses the
  technique from the shape of the rule, numbers the coverage items, asks
  about the gaps the design exposes, and takes every expected result from the
  requirements, never from the code. Ask "what inputs should I test for
  this?" or "design the tests for this rule".
- **dependency-resilience-audit** — analyze how your system can fail through
  what it does not control (SDKs, APIs, cloud services, third-party scripts)
  with an STPA-based boundary analysis. Produces a report with loss
  scenarios backed by code evidence, mitigations, and the tests to add.
- **regression-strategy** — decide which tests run where, when and how, so a
  growing suite stays fast, stable and trusted. Assigns each test to a gate,
  picks the simplest selection mechanism that works at your scale, and sets
  health rules for flaky and slow tests.
- **artifact-review** — review a requirements document, technical design, ADR,
  test plan or PR description through focused lenses (consistency,
  testability, traceability across artifacts, architecture conformance,
  security, performance, reliability, operability), then triage: at most five
  blocking findings for the author and a few questions for the human review.
  Ask "review this PRD" or "check this design against its requirements".

New skills ship with the articles that teach them; subscribers hear first.

## Install (Claude Code)

```sh
/plugin marketplace add diariobug/marketplace
/plugin install diariobug
```

Update later with `/plugin update diariobug`. Skills are namespaced:
`/diariobug:testing-by-level-and-type`, `/diariobug:test-design-techniques`,
`/diariobug:dependency-resilience-audit`,
`/diariobug:regression-strategy`, `/diariobug:artifact-review`.

## Install (Cursor, Codex, Gemini CLI, Copilot and other agents)

The same skill folders work anywhere `SKILL.md` is supported. With the
[`skills` CLI](https://github.com/vercel-labs/skills), which installs into
most coding agents:

```sh
npx skills add diariobug/marketplace                                  # choose skills and agents
npx skills add diariobug/marketplace --skill testing-by-level-and-type
```

With Cline:

```sh
cline skill install https://github.com/diariobug/marketplace --skill testing-by-level-and-type
cline skill install https://github.com/diariobug/marketplace --skill dependency-resilience-audit
cline skill install https://github.com/diariobug/marketplace --skill regression-strategy
cline skill install https://github.com/diariobug/marketplace --skill artifact-review
```

No marketplace support? Copy the skill folder into your agent's skills
directory, or paste `SKILL.md` plus its `references/` and `workflows/` into
your project's instructions. The subscriber kits on diariobug.com bundle the
same folders with a PDF method guide.

## En español: cómo instalar los skills

Los skills son gratuitos para todos. En **Claude Code**:

```sh
/plugin marketplace add diariobug/marketplace
/plugin install diariobug@diariobug
```

El plugin instala los cinco skills; actualízalo después con `/plugin update diariobug`.

**Con Cursor, Codex, Gemini CLI, Copilot u otro agente de código**, el comando
`npx skills add diariobug/marketplace` te deja elegir los skills y el agente donde instalarlos.
Sin Node.js, descarga este repositorio
([ZIP](https://github.com/diariobug/marketplace/archive/refs/heads/main.zip)) y copia la
carpeta del skill que quieras, por ejemplo `plugins/diariobug/skills/testing-by-level-and-type/`,
a la carpeta de skills de tu agente. Si tu agente no admite skills, agrega el contenido de
`SKILL.md`, con sus carpetas `references/` y `workflows/`, a las instrucciones de tu proyecto.

Los kits completos, con la guía del método en PDF y las hojas de trabajo, son gratuitos
para suscriptores: [diariobug.com/recursos/](https://diariobug.com/recursos/).

## Privacy

The plugin makes no network calls. No telemetry, no tracking — it only reads
the project you point it at.

## License

CC BY-NC-SA 4.0 — share and adapt non-commercially, crediting Diario Bug,
under the same license. Full text:
https://creativecommons.org/licenses/by-nc-sa/4.0/deed.en
