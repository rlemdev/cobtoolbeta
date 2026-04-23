# First Lane Implementation Brief

## Status
- `VERIFIED` This brief is the implementation-ready follow-up to `docs/execution/12-selected-first-lane.md`.
- `VERIFIED` It defines the preferred first increment inside the selected boleto issuance logging lane.

## Lane Name
`Boleto issuance logging usability improvements`

## Recommended First Increment
Implement:

`Additive in-page filtering, sorting, and list-visibility improvements for the SOLICITACOES table on page-emissao-boletos, using in-memory UI state only and preserving the existing stored row contract.`

## Why This Is The Right First Increment
- `VERIFIED` It delivers immediate utility on an operational page with stored records.
- `VERIFIED` It does not require new pages, new persistence, or new import/export contracts.
- `VERIFIED` It teaches future work how to extend a persisted module safely without touching critical recovery or document systems.
- `VERIFIED` The current page already has a clear target surface:
  - the `SOLICITACOES` table
  - existing row rendering in `bolRenderList()`
  - contained page initialization in `initEmissaoBoletos()`

## Proposed Scope

### In scope
- additive toolbar or controls above the `SOLICITACOES` table
- in-memory search/filter of rendered solicitation rows
- in-memory sorting of the existing list view
- clearer empty state or match-count feedback
- non-destructive usability improvements on the list area

### Preferred examples
- filter by requester type
- search by solicitante name
- newest/oldest sort toggle
- small count badge such as:
  - total rows
  - filtered rows

### Out of scope
- changing the stored solicitation object shape
- adding module-specific import/export
- editing or managing representative records
- adding new pages or modal flows
- redesigning the form layout wholesale
- changing backup behavior
- changing operator or session behavior

## Likely Affected Files
- `index.html`
- `script.js`
- `style.css` only if small, local styling is needed

No other files should be touched unless the scope expands and is reclassified.

## Affected Modules
- Primary:
  - `Boleto issuance logging`
- Passive dependencies:
  - `Operator session gate`
  - `App shell and navigation`

## Affected Pages
- Primary:
  - `emissao-boletos`
  - `page-emissao-boletos`
- Regression-adjacent:
  - `menu-esfera`

## Affected Storage
- Existing store only:
  - `cobtool_emissao_boletos_v1__<slug>`

### Storage rule for this increment
- `VERIFIED` Do not add new durable storage for filters or sort state.
- `VERIFIED` Keep the new UI state derived and in-memory only.
- `VERIFIED` Do not change the row payload saved by `bolSaveAll()`.

## Affected Import / Export Behavior
- `VERIFIED` None directly
- `VERIFIED` Existing rows will continue to be included indirectly in the full backup `localStorage` dump

### Import/export guardrail
- Do not change:
  - backup payload shape
  - operator payload behavior
  - restore order
  - module import/export assumptions

## Affected Document Flows
- `VERIFIED` None

## Risk Level
- Module risk classification:
  - `Safest to extend first`
- Implementation brief risk:
  - `Low`

## Required Guardrails
- keep the work additive
- keep all persistence browser-local
- do not add a backend or database
- do not rename DOM ids or route keys unnecessarily
- do not touch representative lookup contracts
- do not touch document or parser flows
- do not touch operator-scoping logic

## Minimum Regression Checklist
- Open `emissao-boletos` from `menu-esfera`.
- Confirm the new list controls render and work on existing rows.
- Register one new entry for each requester type:
  - tele vendedor(a)
  - representante externo
  - cliente
- Confirm filtering and sorting work for newly added and existing rows.
- Confirm delete still works if touched by the implementation.
- Reload the page and confirm rows still persist and render.
- Switch operator and confirm stored rows remain scoped to the operator.
- Confirm no other Esfera pages were impacted by the change.

## Acceptance Criteria
- Operators can narrow or inspect the `SOLICITACOES` list faster than before.
- The change stays on the existing `page-emissao-boletos`.
- No new durable storage is introduced.
- Existing row persistence remains compatible.
- No import/export or document flow changes are introduced.
- Regression remains limited and manageable.

## Explicit Non-Goals
- representative management
- stored row schema expansion
- backup/export work
- document/PDF work
- parser work
- navigation shell changes
- broader Esfera menu redesign

## What Must Not Be Touched
- `cobtool_emissao_boletos_v1__<slug>` key naming
- `cobtool_representantes_v1__<slug>` shape assumptions
- `cobtoolScopedStorageKey(...)`
- `payloadType` values
- `pages`, `PAGE_PARENT`, `pageIds`
- operator session keys
- any document, template, font, or barcode helper logic

## Suggested Next Prompt Direction
The next implementation prompt should target:

`Implement the first safe lane from docs/execution/13-first-lane-implementation-brief.md by adding additive in-page filtering, sorting, and list-visibility improvements to the SOLICITACOES table on page-emissao-boletos without changing storage contracts.`
