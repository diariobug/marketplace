# System kinds: where the boundaries are

Identify the kind (or mix) first; it decides where to look and which hazards are typical.
These are starting points for the control structure, not a checklist.

## Mobile app
- **Boundaries:** SDKs compiled into the app (analytics, ads, crash reporting, auth, payments,
  remote config), backend APIs, push notifications, the platform's own services.
- **Where to look:** app startup (`AppDelegate`/`@main`, `Application.onCreate`), dependency
  manifests (`Package.swift`, `Podfile`, `build.gradle`), network clients, remote config fetches.
- **Typical hazards:** terminates during launch; blocks the main thread; drains battery or data.
- **What is specific:** SDKs run in your process, so their crashes are yours (P9). A fix needs a
  store release and users updating — hours to days — so the off switch (P5) is the only fast
  lever. Crash reports usually upload on the next launch, so feedback is late (P6).

## Backend service
- **Boundaries:** third-party APIs, databases and caches you don't operate, queues, cloud
  services, identity providers, inbound webhooks, client libraries.
- **Where to look:** HTTP and RPC client configuration (timeouts, retries, pools), webhook
  handlers, startup health checks, background workers, secrets and token handling.
- **Typical hazards:** request threads or connections exhausted waiting on a slow dependency;
  retries amplifying an outage; duplicated side effects; data written in a corrupt state.
- **What is specific:** failures cascade through shared pools (P2, P3). Inbound webhooks are
  control actions *into* you: they arrive late, twice, out of order or forged (verify
  signatures; make handlers idempotent). Credentials and quotas fail on a calendar (P8).

## Web frontend
- **Boundaries:** third-party scripts (tags, analytics, chat, A/B testing, ads), CDNs, embedded
  widgets, backend APIs, browser APIs.
- **Where to look:** `<script>` tags and tag managers, bundler config, CSP headers, API clients,
  feature-flag clients.
- **Typical hazards:** the page does not render or is not interactive; checkout breaks; data
  leaks to a third party.
- **What is specific:** third-party scripts share your main thread and your page's privileges
  (load them `async`/`defer`, isolate them in iframes or workers where possible, pin with
  Subresource Integrity, restrict with CSP). A tag manager is remote configuration of your page.

## Library or CLI
- **Boundaries:** your own dependencies, which your users inherit; the services the tool calls;
  the environment (filesystem, network, shell).
- **Where to look:** manifests and lockfiles, version ranges, install scripts, network calls.
- **Typical hazards:** a broken or compromised transitive dependency reaches every user; the
  tool hangs without network.
- **What is specific:** supply chain is the main boundary — maintenance status, known
  vulnerabilities, version ranges that let breaking releases in (P7).

## AI / LLM application
- **Boundaries:** model providers (directly or through a gateway such as LiteLLM), embedding and
  vector stores, image or speech models, tool calls the model triggers.
- **Where to look:** every completion call and what is done with its reply; retry and timeout
  settings; structured-output parsing; fallbacks; cost tracking.
- **Typical hazards:** a cut, refused or malformed reply used as if it were a good one; good
  work overwritten by a worse generation; spend with nothing usable to show for it.
- **What is specific:** model output is untrusted input (P1) even from a good model — check
  `finish_reason`, validate structure against a schema, and compare against what the output
  replaces. Calls are paid and nondeterministic, so a retry is a new purchase and a new answer
  (P3). A fallback that produces *something* (a template, a placeholder) can be worse than
  failing (P4: decide what is better than nothing). Credit running out is an outage on a
  calendar (P8).

## Data pipeline
- **Boundaries:** source systems and their schemas, file drops, external datasets, warehouses,
  scheduled jobs.
- **Where to look:** ingestion code, schema definitions, job schedules, data-quality checks.
- **Typical hazards:** corrupt or partial data published as complete; silent drops.
- **What is specific:** the worst failures are silent — the job succeeds with wrong data. Schema
  validation (P1) and row-count or freshness checks (P6) are the feedback that is usually
  missing.
