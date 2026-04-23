# Cobtool Module Map

## Status
- `VERIFIED` Modules below are derived from actual page sections, storage keys, function groups, and asset usage in `index.html` and `script.js`.
- `INFERRED` Because the runtime is monolithic, module boundaries are logical rather than file-based.

## Classification Legend
- `Core`: directly supports day-to-day operational work and persists business data.
- `Supporting`: enables core flows or coordinates cross-cutting behavior.
- `Auxiliary`: convenience tooling, output formatting, or utility behavior with lower business centrality.

## Logical Module Inventory

| Module | Type | Main Pages | Primary Purpose | Key Dependencies | Depended On By | Status |
| --- | --- | --- | --- | --- | --- | --- |
| App shell and navigation | Supporting | `home`, menu pages, all page transitions | Controls page switching, parent navigation, active-page state, top menu behavior | DOM page ids, `setPage`, `PAGE_PARENT`, `pageIds` | All modules | `VERIFIED` |
| Operator session gate | Core | `operator-unlock`, top-menu switch operator | Selects active operator, enforces idle lock, drives operator-scoped storage | localStorage session keys, navigation shell | Alfa, import/export, registro-boletos, all scoped storage | `VERIFIED` |
| Theme and header utilities | Auxiliary | topbar | Theme toggle, message counter, CNPJ converter, quick barcode reading | localStorage, header DOM, boleto parser exposure | Operators using quick tools | `VERIFIED` |
| Alfa cobrança workspace | Core | `alfa` | Generates cobrança messages, warnings, operator identity output, contact record text | Operator session, Esfera parser, local UFs state | Contact workflow, carta generators | `VERIFIED` |
| Extrajudicial notification generator | Core | `notificacao-extrajudicial` | Builds notification PDFs and supporting email body | PDF templates, pdf.js, pdf-lib, fonts | Document workflow | `VERIFIED` |
| Cobrança letter generator | Core | `carta-cobranca` and Alfa carta modal | Builds cobrança letters from static templates and optional uploaded PDF | Operator identity, templates, pdf-lib, fonts | Document workflow | `VERIFIED` |
| Quitação letter generator | Core | `carta-quitacao` and Alfa quit modal | Builds quitação letters from operator- and esfera-specific templates | Operator identity, templates, pdf-lib, fonts | Document workflow | `VERIFIED` |
| Formatting: títulos | Auxiliary | `formatacao` | Formats open and updated title lists for communication | Text parsing helpers | Operators | `VERIFIED` |
| Formatting: cobrança text helpers | Auxiliary | `formatacao-cobrancas` | Formats Esfera/client/WhatsApp snippets and attachment tokens | String formatting helpers | Operators | `VERIFIED` |
| Rich text editor | Auxiliary | `formatacao-texto` | Markdown-like/rich-text editing utilities | DOM editor state | Operators | `VERIFIED` |
| Compensation engine | Core | `compensacoes` | Parses titles/credits, calculates compensation text, supports negotiation-linked outputs | Formatting helpers, negotiation data shape | Negotiations, operators | `VERIFIED` |
| Client registry | Core | `neg-cadastro` | CRUD for client records used across the app | operator-scoped localStorage | Networks, negotiations, transfers | `VERIFIED` |
| Network registry | Core | `redes` | CRUD for grouped client networks | client registry | Negotiations | `VERIFIED` |
| Negotiations | Core | `neg-cad-negociacoes`, `neg-andamento`, `neg-finalizadas`, `neg-canceladas`, route `negociacoes` | Registers, tracks, exports, and manages negotiations for clients and networks | clients, redes, compensation helpers, operator context | Transfers, compensation flow, exports | `VERIFIED` |
| Transfers | Core | `transferencias`, `tr-andamento`, `tr-finalizadas` | Registers bank transfers, tracks stages, links to negotiations, handles proof actions | clients, negotiations, PIX proof persistence | Backup/export, operators | `VERIFIED` |
| PIX proof persistence | Supporting | transfer UI and PIX table | Stores proof files in IndexedDB and row state in localStorage | IndexedDB, transfer rows | Transfers, backup export/import | `VERIFIED` |
| Boleto issuance logging | Supporting | `emissao-boletos` | Tracks boleto issuance requests by requester type | localStorage, representative lookup store | Operations team | `VERIFIED` |
| Boleto metadata registry | Core | `registro-boletos` | Reads boleto PDFs, extracts fields, exports/imports metadata JSON | pdf.js, operator identity | Header barcode utility, external metadata sharing | `VERIFIED` |
| Contact record helper | Supporting | `registro-contato` | Builds reusable contact-history text with lightweight persisted state | localStorage, text helpers | Operators | `VERIFIED` |
| Unified import/export | Core | `import-export` | Full backup export/import and operator payload import | All business storage, PIX IndexedDB, page state | Disaster recovery, operator portability | `VERIFIED` |
| Static assets and templates | Supporting | `notificação/`, font files, `operadores/` | Provides PDF bases, fonts, and portable operator payload format | Static hosting, fetchable local assets | Document flows, imports | `VERIFIED` |

## Dependency Notes By Module

### App shell and navigation
- `VERIFIED` Uses hardcoded route names in `pages`.
- `VERIFIED` Uses `PAGE_PARENT` for back behavior.
- `VERIFIED` Uses `pageIds` to map logical route names to actual DOM section ids.
- `VERIFIED` All feature pages depend on this layer remaining stable.

### Operator session gate
- `VERIFIED` Uses `cobtool_alfa_operador_v1` and `cobtool_operator_session_v1`.
- `VERIFIED` Idle timeout is two hours.
- `VERIFIED` `cobtoolScopedStorageKey()` uses active operator identity to resolve storage keys for multiple modules.

### Alfa cobrança workspace
- `VERIFIED` Depends on operator identity and selected UFs.
- `VERIFIED` Parses Esfera text into title data.
- `VERIFIED` Triggers contact-record modal and document-generation modals.
- `INFERRED` This module is the primary user-facing entry point for collections messaging.

### Negotiations
- `VERIFIED` Depends on clients for single-client mode.
- `VERIFIED` Depends on networks for rede mode.
- `VERIFIED` Depends on compensation helpers for text generation and compensation handoff.
- `VERIFIED` Transfers search for matching negotiations by client code/CNPJ.

### Transfers
- `VERIFIED` Depends on client lookup for identified transfer registration.
- `VERIFIED` Checks negotiation matches when the operator opens the compensation action.
- `VERIFIED` Depends on PIX proof storage for proof persistence/export.

### Boleto issuance logging
- `VERIFIED` Reads `cobtool_representantes_v1__<slug>` as lookup data.
- `UNKNOWN` The producer/owner UI for representative records is not present in the inspected repository snapshot.

### Boleto metadata registry
- `VERIFIED` Exposes parser helpers globally:
  - `window.__cobtoolRegistroBoletosReadPdfRows`
  - `window.__cobtoolRegistroBoletosExtractBarcode`
- `VERIFIED` The topbar boleto barcode helper depends on this exposure.

### Unified import/export
- `VERIFIED` Cross-cuts almost every persisted module.
- `VERIFIED` Full backup export includes full localStorage dump plus normalized entity arrays and PIX blobs.
- `VERIFIED` Import path can replace browser state and trigger reload.

## Safe Extension Summary

### Safer modules to extend first
- Formatting: títulos
- Formatting: cobrança text helpers
- Rich text editor
- Contact record helper
- Boleto issuance logging

### Medium-risk modules
- Client registry
- Network registry
- Alfa cobrança workspace
- Negotiations list rendering
- Transfers reporting/export helpers

### High-risk modules
- Operator session gate
- Unified import/export
- PIX proof persistence
- Boleto metadata parsing
- PDF letter generators
- Extrajudicial notification generator

## Boundary Rules
- `VERIFIED` Treat modules as logical seams inside `script.js`; do not assume clean isolation already exists.
- `VERIFIED` If a change touches operator storage, import/export, PDF parsing, or negotiation/transfer coupling, it is multi-module work even if only one screen is visibly edited.
