# Cobtool Change Rules

## Status
- `VERIFIED` These rules are derived from current runtime behavior and repository constraints.
- `VERIFIED` They are intended to be hard rules for future Codex tasks, not optional suggestions.

## Hard Rules

### 1. Do not change storage keys without a migration strategy
- `VERIFIED` Business stores are read by exact key names.
- If a key must change:
  - keep a compatibility read path
  - migrate old values into the new key
  - document the migration in `persistence-map.md` and `import-export-map.md`

### 2. Do not change operator suffix logic casually
- `VERIFIED` Operator scoping is based on `__<slug>`.
- Changing slug generation can orphan data or mix operator data.

### 3. Do not change `payloadType` strings without explicit compatibility notes
- `VERIFIED` Import dispatch depends on payload type values.
- Required review targets:
  - `cobtool_backup_v2`
  - `cobtool_operator_data_v1`
  - `cobtool_registro_boletos_metadados_v1`

### 4. Do not add new persisted business data without deciding its backup behavior
- If the new data is important to recovery, it must be included in full backup export/import.
- If it is intentionally local-only or ephemeral, that limitation must be documented.

### 5. Do not rename critical DOM ids or `data-page` values blindly
- `VERIFIED` The runtime uses direct DOM queries and route-key lookups.
- Any navigation or id rename requires:
  - HTML update
  - `pages` review
  - `PAGE_PARENT` review
  - `pageIds` review
  - manual navigation regression

### 6. Do not alter operator behavior without tracing all dependents
- Review impact on:
  - storage scoping
  - unlock/idle flow
  - Alfa operator select
  - document template selection
  - registro-boletos operator labels
  - operator import/export

### 7. Do not alter PDF/document flows without explicit verification
- Required checks after document changes:
  - notification PDF generation
  - cobrança letter generation
  - quitação letter generation
  - boleto PDF parsing
  - topbar barcode extraction

### 8. Do not remove legacy import behavior unless the compatibility decision is explicit
- `VERIFIED` There is still a legacy import branch for older backup shape.
- Removal requires:
  - documented deprecation decision
  - user migration note
  - regression against current export path

### 9. Do not move persistence to any server-side system
- `VERIFIED` The app must remain GitHub Pages compatible.
- Disallowed additions:
  - database services
  - server sessions
  - backend APIs required for core flows
  - host-side Node runtimes

### 10. Do not assume generated/downloaded files can replace browser persistence automatically
- `VERIFIED` Export flows are explicit user-managed operations.
- Keep import/export actions user-triggered and browser-safe.

### 11. Do not break module-global bridges without replacement
- Examples:
  - `window.__cobtoolRegistroBoletosExtractBarcode`
  - `window.__cobtoolTransferenciasRefresh`
  - `window.ctMessageCounter`

### 12. Do not change business calculations without preserving current semantics
- High-sensitivity areas:
  - negotiation parcel/value math
  - compensation calculations
  - transfer stage semantics
  - Serasa / atraso warning thresholds

### 13. Do not replace static assets with remote-only or authenticated assets
- Templates and fonts must remain fetchable in a static deployment context.

### 14. Do not introduce build-step assumptions into runtime behavior
- Static file splitting is acceptable later.
- Runtime must still work when served as plain static assets from GitHub Pages.

### 15. Do not close a task touching storage, documents, or routing without updating docs
- Minimum doc updates when relevant:
  - `module-map.md`
  - `ui-map.md`
  - `persistence-map.md`
  - `data-contracts.md`
  - `import-export-map.md`
  - `pdf-and-document-map.md`
  - `public/llms.txt`

## Mandatory Review Questions Before Any Change
- Which page ids, route keys, or modal ids are affected?
- Which storage keys are read or written?
- Is the data operator-scoped?
- Does the feature need backup/export coverage?
- Does it change a document/PDF path, anchor, filename, or library call?
- Does it rely on an existing global bridge used elsewhere?
- What manual regression steps are required afterwards?

## Approval Thresholds

### Changes that require an explicit plan before coding
- operator scoping changes
- backup/import changes
- document-template or parser changes
- negotiation data model changes
- navigation map changes

### Changes that can usually proceed with normal care
- additive UI inside isolated existing pages
- non-persistent formatting helpers
- low-risk reporting/copy improvements
- purely presentational CSS fixes with page-level verification
