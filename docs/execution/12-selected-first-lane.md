# Selected First Lane

## Status
- `VERIFIED` The selected first implementation lane is based on `docs/execution/01-module-priority-matrix.md`, `docs/execution/10-first-safe-feature-lanes.md`, and direct inspection of `index.html` and `script.js`.
- `VERIFIED` The selected lane stays inside the `Boleto issuance logging` module on `page-emissao-boletos`.
- `INFERRED` The representative lookup store consumed by this page is not owned by a visible editor in the current repository snapshot, so it should be treated as read-only in the first lane.

## Candidate Lanes Considered

### 1. Formatting output improvements
- modules:
  - `formatacao`
  - `formatacao-cobrancas`
  - `formatacao-texto`
- strengths:
  - lowest persistence risk
  - easy regression
  - good additive surface
- reasons not selected first:
  - lower operational learning about persisted modules
  - lower direct value for managing stored work items

### 2. Contact-record helper quality-of-life
- module:
  - `registro-contato`
- strengths:
  - narrow operator-scoped persistence
  - localized UI
  - low document/import-export risk
- reasons not selected first:
  - slightly narrower operational value than boleto issuance logging
  - more business-language-specific, which reduces the general learning value of the first implementation

### 3. Boleto issuance logging usability improvements
- module:
  - `emissao-boletos`
- strengths:
  - `Safest to extend first` classification
  - persisted operational value
  - isolated page logic
  - no PDF or parser coupling
  - no required import/export contract changes for additive UI work
- selection result:
  - selected

### 4. Client and network registry usability improvements
- modules:
  - `neg-cadastro`
  - `redes`
- strengths:
  - good business value
  - contained UI
- reasons not selected first:
  - storage contracts are more central
  - client and network data feed negotiations and transfers
  - regression surface is broader than boleto issuance logging

## Selected Lane
The first safe implementation lane for Cobtool is:

`Boleto issuance logging usability improvements`

This lane should focus on additive, operator-safe, page-local improvements inside the existing `SOLICITACOES` workflow on `page-emissao-boletos`.

## Why This Lane Was Selected

### Best balance of value and safety
- `VERIFIED` It improves a real operational workflow rather than a purely presentational tool.
- `VERIFIED` It remains inside a module classified as `Safest to extend first`.
- `VERIFIED` The page already has a contained form plus list structure that supports safe additive UI work.

### Good first learning surface
- `VERIFIED` It exercises existing Cobtool patterns:
  - operator-scoped `localStorage`
  - page-local list rendering
  - direct DOM event wiring
  - existing table-based UI
- `VERIFIED` It does so without crossing into:
  - backup/import orchestration
  - negotiation or transfer coupling
  - PDF generation
  - boleto parsing

### Strong compatibility posture
- `VERIFIED` The lane is fully compatible with GitHub Pages.
- `VERIFIED` The lane does not require a database or backend.
- `VERIFIED` The lane can be implemented with additive UI and in-memory derived state only.

## Affected Areas

### Affected modules
- Primary:
  - `Boleto issuance logging`
- Secondary, dependency-only:
  - `Operator session gate`
  - `App shell and navigation`

The first implementation in this lane should not change secondary modules. They are included only because the page is operator-scoped and route-bound.

### Affected pages
- Primary:
  - route `emissao-boletos`
  - DOM section `page-emissao-boletos`
- Regression-adjacent:
  - route `menu-esfera`
  - DOM section `page-menu-esfera`

### Affected storage
- Primary operator-scoped store:
  - `cobtool_emissao_boletos_v1__<slug>`
- Read-only lookup dependency:
  - `cobtool_representantes_v1__<slug>` as `INFERRED` representative lookup input

The selected lane should avoid changing the stored row contract in the first implementation.

### Affected import/export behavior
- `VERIFIED` No direct module-specific import/export flow exists for boleto issuance logging.
- `VERIFIED` Existing entries are still captured indirectly by the full backup `localStorage` dump.
- `VERIFIED` The first implementation should not change full backup payloads, operator payload behavior, or restore order.

### Affected document flows
- `VERIFIED` None

## Risk Level
- Module classification:
  - `Safest to extend first`
- Lane risk:
  - `Low` for additive list and UX work

### Why the risk stays low
- the route already exists
- persistence already exists
- document and parser flows are not involved
- no payload change is required for the first increment

### What would raise the risk
- changing the stored row shape
- changing operator scoping
- changing representative lookup assumptions
- introducing module-specific export/import behavior

## Regression Checklist For This Lane
- Navigate from `menu-esfera` to `emissao-boletos`.
- Confirm the existing requester-type switching still works:
  - `TELE VENDEDOR(A)`
  - `REPRESENTANTE EXTERNO`
  - `CLIENTE`
- Confirm representative search/select behavior still works for touched requester types.
- Register at least one new solicitation for:
  - tele vendedor(a)
  - representante externo
  - cliente
- Confirm touched list usability behavior works on existing and newly created rows.
- Reload the page and confirm stored rows still render correctly.
- Switch operator and confirm rows remain operator-scoped.
- Confirm no document, parser, or import/export flow was unintentionally touched.

## Acceptance Criteria
- The first implementation stays entirely within the boleto issuance logging lane.
- The implementation is additive and does not require a new page.
- No backend, database, or server-only dependency is introduced.
- No storage key, payload type, or operator-scoping rule changes are introduced.
- Operators can manage the `SOLICITACOES` list more efficiently after the change.
- Regression remains limited to the affected page plus operator-scope smoke checks.

## Explicit Non-Goals
- changing representative data contracts
- adding representative CRUD
- changing import/export payloads
- changing full backup behavior
- changing PDF/document flows
- changing boleto parser behavior
- changing navigation maps
- redesigning the page shell
- touching negotiations, transfers, or compensation logic

## What Must Not Be Touched During This First Implementation
- `cobtoolScopedStorageKey(...)`
- `cobtool_emissao_boletos_v1__<slug>` key name
- `cobtool_representantes_v1__<slug>` data contract
- `payloadType` contracts
- `pages`, `PAGE_PARENT`, `pageIds`
- operator session keys
- any PDF template, font, parser, or barcode helper logic
