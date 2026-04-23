# Cobtool Feature Implementation Rules

## Status
- `VERIFIED` These rules are designed to keep future features compatible with the current static, local-first architecture.
- `VERIFIED` The goal is safe additive work, not speculative refactoring.

## Default Feature Strategy
- Prefer extending an existing module before creating a new one.
- Prefer additive behavior before changing existing business semantics.
- Prefer browser-local persistence or user-managed file export over any remote persistence.
- Prefer updating documentation in the same task that introduces a new contract.

## 1. Where New UI Should Attach

### Extend existing pages when
- the feature belongs to one current business workflow
- the feature uses the same stored entity type
- the feature can live inside the same table, panel, modal, or toolbar without overloading navigation

### Add a new page when
- the workflow is operationally distinct
- the page needs its own forms/tables/modals
- forcing it into an existing page would increase coupling or operator confusion

### If a new page is added
- `VERIFIED` Add a `<section id="page-...">` to `index.html`
- `VERIFIED` Add the logical route key to `pages`
- `VERIFIED` Add parent routing in `PAGE_PARENT`
- `VERIFIED` Add `pageIds` mapping if route key and section id diverge
- `VERIFIED` Add at least one navigation entry using `data-page="..."`
- `VERIFIED` Verify `nav-back` behavior from the new page

## 2. How To Persist New Data Safely

### If the data is operator-owned business data
- use `cobtoolScopedStorageKey('cobtool_<domain>_v1')`
- create explicit `load`, `save`, and `sanitize` helpers
- default missing fields during load/sanitize

### If the data is global preference/state
- do not scope it unless there is a strong business reason
- keep the key name stable and versioned if structure matters

### If the data is binary or large
- prefer IndexedDB
- define store schema explicitly
- define export/import serialization rules before shipping

### If the data is ephemeral
- keep it in memory only
- do not pollute backup payloads unless recovery is operationally important

## 3. Naming Rules For New Persistence

### localStorage
- Preferred pattern for business domains:
  - `cobtool_<domain>_v1`
  - resolved through `cobtoolScopedStorageKey(...)` when operator-scoped

### IndexedDB
- Preferred pattern:
  - DB: `cobtool_<domain>_db_v1`
  - store: `<domain>_store`

### Payloads
- Use explicit `payloadType` with semantic version suffix when shape matters.
- Keep older import compatibility when practical.

## 4. Import / Export Integration Rules

### When a new feature must update full backup logic
- The feature stores durable business data.
- The feature stores user-produced files or binary artifacts.
- The feature would be costly or impossible to recreate manually after browser loss.

### When a new feature may skip full backup integration
- The state is temporary UI state only.
- The feature is a pure formatter with no durable records.
- The output is already user-managed through explicit downloads.

### Required action if new durable data is added
- update `exportUnified()`
- update `importUnified()`
- document the contract in `data-contracts.md`
- update `import-export-map.md`
- update `public/llms.txt`

## 5. GitHub Pages Compatibility Rules

### Allowed patterns
- additional static HTML/CSS/JS files
- additional browser-side libraries loaded by script tags or vendored static assets
- browser APIs such as:
  - `localStorage`
  - `IndexedDB`
  - `Blob`
  - `FileReader`
  - `Clipboard`
  - `fetch` for static assets

### Disallowed patterns
- database clients that require a server process
- server actions
- backend-only authentication
- required API gateways for core behavior
- host-side Node processes
- runtime assumptions that depend on a build server existing in production

## 6. DOM / Navigation Safety Rules
- Reuse existing card, panel, table, modal, and toolbar patterns where they already fit.
- Do not remove or rename existing ids/classes used by `script.js` unless you update all references.
- If introducing a new modal, ensure:
  - unique id namespace
  - close behavior for click-outside and `Escape`
  - body overflow reset on close

## 7. Entity and Contract Documentation Rules

### If a feature adds a new entity
- document its persisted shape in `data-contracts.md`
- document its storage key in `persistence-map.md`
- document its module in `module-map.md`
- document its screen/page in `ui-map.md`
- update `public/llms.txt`

### If a feature adds fields to an existing entity
- mark the fields as additive
- default them during load/sanitize
- update export/import notes if payloads are affected

## 8. Preferred Safe Feature Types
- additive filters, views, sort controls, or exports
- new formatter outputs
- isolated helper panels
- low-risk reporting metrics
- new non-destructive actions that reuse existing data shapes

## 9. Feature-Readiness Checklist Before Coding
- Which module owns the feature?
- Which page or modal should host it?
- Which ids, route keys, or DOM hooks are needed?
- Does the feature create durable data?
- If yes, is it operator-scoped?
- Does it need full backup/export coverage?
- Does it affect a PDF/document flow?
- Which docs must be updated in the same task?
