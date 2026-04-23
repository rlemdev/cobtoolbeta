# Cobtool Repository Operating Guide

## Status
- `VERIFIED` Cobtool is a static, browser-only operational worktool.
- `VERIFIED` The current production model is GitHub Pages static hosting with no backend and no database.
- `VERIFIED` The detailed repository maps live in `docs/`.
- `VERIFIED` The execution layer for future implementation work lives in `docs/execution/`.
- `VERIFIED` The durable Codex operating layer for this repo lives in `AGENTS.md`, `skills/`, `docs/`, `docs/execution/`, and `public/llms.txt`.

## What Cobtool Is
- `VERIFIED` Cobtool is a client-side collections and finance-operations workstation used by operators.
- `VERIFIED` It runs from static files: `index.html`, `style.css`, `script.js`, local fonts, local templates, browser storage, and file import/export.
- `VERIFIED` Core workflows include operator-gated access, cobranca support, formatting tools, client and network records, negotiations, transfers, PIX proof handling, boleto metadata parsing, document generation, and recovery/import-export flows.

## What Cobtool Is Not
- `VERIFIED` It is not a marketing site.
- `VERIFIED` It is not a SaaS platform with server-backed CRUD.
- `VERIFIED` It is not a database-backed app.
- `VERIFIED` It is not an API-first architecture.
- `VERIFIED` It is not a project that can rely on a host-side Node runtime in production.

## Permanent Sources Of Truth
- `AGENTS.md`: repo-wide rules and mandatory working posture.
- `skills/00-skill-index.md`: skill routing and usage order.
- `skills/01-repo-identity-skill.md`
- `skills/02-github-pages-skill.md`
- `skills/03-persistence-skill.md`
- `skills/04-import-export-skill.md`
- `skills/05-pdf-and-document-skill.md`
- `skills/06-operator-session-skill.md`
- `skills/07-ui-navigation-skill.md`
- `skills/08-feature-implementation-skill.md`
- `skills/09-regression-skill.md`
- `skills/10-modularization-skill.md`
- `docs/`: detailed maps, contracts, risks, and regression guidance.
- `docs/execution/`: mandatory implementation workflow, priority matrix, extension rules, regression matrix, and default Codex operating sequence.
- `public/llms.txt`: concise machine-readable project index for future Codex tasks.

## Layer Responsibilities
- `AGENTS.md`: hard constraints, repo identity, mandatory read order, and non-negotiable operating posture.
- `skills/`: task routing. Use them to decide which topical guidance applies to the current work.
- `docs/execution/`: action layer. Use it to decide implementation order, risk classification, intake, and regression scope.
- `docs/`: canonical detailed maps, data contracts, persistence details, risk notes, and test steps.
- `public/llms.txt`: compact machine-readable index for future Codex orientation.

## Mandatory Read Order Before Making Changes
1. Read `AGENTS.md`.
2. Read `skills/00-skill-index.md`.
3. Read `skills/01-repo-identity-skill.md` and `skills/02-github-pages-skill.md`.
4. Read any additional repo skill that matches the task:
   - storage or state: `skills/03-persistence-skill.md`
   - backup, restore, portability: `skills/04-import-export-skill.md`
   - PDFs, boleto parsing, letters, templates: `skills/05-pdf-and-document-skill.md`
   - operator gate or scoped behavior: `skills/06-operator-session-skill.md`
   - pages, routes, DOM ids, modals: `skills/07-ui-navigation-skill.md`
   - new feature work: `skills/08-feature-implementation-skill.md`
   - verification and close-out: `skills/09-regression-skill.md`
   - refactor or extraction work: `skills/10-modularization-skill.md`
5. Read the execution layer in `docs/execution/` before any implementation or risky change. Minimum baseline:
   - `docs/execution/00-execution-index.md`
   - `docs/execution/01-module-priority-matrix.md`
   - `docs/execution/02-feature-intake-template.md`
   - `docs/execution/03-safe-implementation-workflow.md`
   - `docs/execution/08-regression-matrix.md`
   - `docs/execution/11-codex-default-operating-sequence.md`
6. Read the relevant detailed docs in `docs/`. Minimum baseline:
   - `docs/project-overview.md`
   - `docs/module-map.md`
   - `docs/ui-map.md`
   - `docs/persistence-map.md`
   - `docs/import-export-map.md`
   - `docs/pdf-and-document-map.md`
   - `docs/operator-session-map.md`
   - `docs/risk-zones.md`
   - `docs/change-rules.md`
   - `docs/testing-checklist.md`
   - `docs/github-pages-constraints.md`
7. Inspect the affected implementation files directly:
   - `index.html`
   - `style.css`
   - `script.js`
   - any touched assets under `operadores/`, the notification template folder, font files, or import/export files

## Hard Repository Constraints

### GitHub Pages only
- `VERIFIED` The delivered app must remain usable as plain static files.
- `VERIFIED` Do not require a backend, server runtime, or production build host to make core features work.
- `VERIFIED` Static JS splitting is allowed later, but runtime behavior must still work under GitHub Pages.

### No database
- `VERIFIED` Do not add SQL, NoSQL, hosted key/value, or backend-managed persistence.
- `VERIFIED` Persistent state must remain browser-local or file-based.

### Browser-local persistence only
- `VERIFIED` Durable state currently lives in `localStorage`, `IndexedDB`, and exported/imported files.
- `VERIFIED` New durable state must follow the same model unless the deployment model is explicitly changed by the user.

### No server-only production assumptions
- `VERIFIED` Do not introduce required server actions, server-only authentication, backend PDF generation, or API-only feature paths.
- `VERIFIED` Optional tooling for local development is not the same as production runtime. Production must stay static.

## Hard Business-Safety Rules

### Persistence
- `VERIFIED` Treat storage keys and operator suffix rules as contracts.
- `VERIFIED` Do not rename storage keys without migration logic and doc updates.
- `VERIFIED` Do not change operator slug behavior casually.

### Import and export
- `VERIFIED` Treat import/export as an operational recovery system, not a convenience extra.
- `VERIFIED` Do not change payload shapes or `payloadType` values without compatibility handling and notes.
- `VERIFIED` Do not add durable data without deciding whether full backup coverage is required.

### Operator scope
- `VERIFIED` Operator identity affects storage, session state, some document template selection, and import/export behavior.
- `VERIFIED` Do not change operator session keys, unlock flow, or scoped persistence without tracing every dependent module.

### PDF and document flows
- `VERIFIED` Template names, anchor strings, font loading, and parser heuristics are fragile contracts.
- `VERIFIED` Do not casually rename, relocate, or replace document assets.
- `VERIFIED` Do not change boleto parsing or barcode extraction logic without explicit verification.

### UI and routing
- `VERIFIED` DOM ids, `data-page` hooks, route keys, `pages`, `PAGE_PARENT`, and `pageIds` are part of runtime behavior.
- `VERIFIED` Do not rename or remove them blindly.

## How Codex Must Work In This Repo
1. Start from the docs and skills, not assumptions.
2. Use `docs/execution/` as the mandatory action layer for implementation work.
3. Map the task to real modules, pages, storage, payloads, and assets before editing.
4. Prefer additive changes over semantic rewrites.
5. Keep Cobtool as an operational worktool, not a redesign exercise.
6. Keep persistence browser-local and user-managed.
7. If a change touches storage, operator scope, import/export, documents, or routing, treat it as multi-module work even if one page is visibly edited.
8. Update `docs/`, `docs/execution/`, and `public/llms.txt` when routes, entities, storage, payloads, operating rules, or risks change.
9. Mark uncertainty explicitly as `VERIFIED`, `INFERRED`, or `UNKNOWN`.
10. Do not invent business rules that are not supported by the code or existing docs.

## Required Checks Before Future Changes
- Which logical module owns the change?
- Which routes or DOM sections are affected?
- Which storage keys are read or written?
- Is the data operator-scoped?
- Does the change affect backup or portability?
- Does the change affect a PDF, boleto parser, barcode helper, template, or font?
- Which regression steps from `docs/testing-checklist.md` apply?

## Highest-Risk Areas
- `VERIFIED` Operator unlock, idle session, and scoped persistence
- `VERIFIED` Full backup import/export and restore order
- `VERIFIED` PDF generation, boleto parsing, and barcode extraction
- `VERIFIED` Navigation maps and critical DOM bindings
- `VERIFIED` Negotiation and transfer coupling, including PIX proof persistence

## Config Note
- `VERIFIED` No repo-level `.codex/config.toml` is installed at this time.
- `VERIFIED` The default operating rules for this repository are intentionally documented here and in `skills/`.
- `INFERRED` This is safer than guessing a config schema that may not add reliable value.
