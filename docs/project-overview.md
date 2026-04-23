# Cobtool Project Overview

## Status
- `VERIFIED` The current application is a browser-only operational worktool composed of `index.html`, `style.css`, `script.js`, local font files, local PDF templates, and small operator payload files.
- `VERIFIED` Deployment is static-only. `.github/workflows/pages-from-release.yml` copies repository files into `_site/` and publishes them to `gh-pages` without a build step, backend, or database.
- `VERIFIED` The app relies on browser storage (`localStorage`, `IndexedDB`) and file import/export for persistence.

## What Cobtool Is
Cobtool is a client-side collections and financial-operations workstation. It is not a marketing site and not a generic portal. The repository implements operator-facing workflows used to:

- generate cobrança messages and contact history text
- format title and cobrança data for downstream communication
- register and monitor negotiations
- register and monitor bank transfers and PIX proofs
- generate notification, cobrança, and quitação PDFs from static templates
- read boleto PDFs and export boleto metadata
- log boleto issuance requests
- import and export local operational data

## Operational Purpose
The application centralizes daily cobrança tasks that would otherwise be spread across manual text editing, PDF handling, spreadsheets, Outlook drafts, WhatsApp records, and local notes.

The current codebase shows three dominant business capabilities:

1. Communication support
   - cobrança message generation
   - formatting tools
   - contact history records
   - document letters and notifications
2. Operational tracking
   - clients
   - networks
   - negotiations
   - transfers
   - boleto issuance logs
3. Document and import/export support
   - boleto PDF parsing
   - barcode extraction
   - PIX proof storage and ZIP export
   - full backup/import
   - operator-scoped data import

## Runtime and Deployment Model

### Browser-first architecture
- `VERIFIED` The app starts directly from `index.html`.
- `VERIFIED` `script.js` contains application logic, state handling, persistence, document generation, and UI behavior.
- `VERIFIED` `style.css` contains all UI styling.
- `VERIFIED` PDF libraries are loaded client-side from CDNs:
  - `pdf.js`
  - `pdf-lib`
  - `fontkit`

### GitHub Pages compatibility
- `VERIFIED` The current deployment model is static hosting only.
- `VERIFIED` No server runtime is required to serve or use the app.
- `VERIFIED` No database connection exists.
- `VERIFIED` No authentication service exists.
- `VERIFIED` No API endpoint is required for core functionality.

### Hard repository constraints
- `VERIFIED` Do not add a database.
- `VERIFIED` Do not introduce server-only code.
- `VERIFIED` Do not assume Node.js exists on the host at runtime.
- `VERIFIED` Do not move persistence away from browser-side storage or user-managed files.
- `VERIFIED` Do not break local-first import/export recovery.

## Main Functional Areas

### 1. App shell and operator gate
- Operator unlock screen
- Operator switch action
- Page navigation and parent/child back behavior
- Theme toggle
- Message counter
- Header utilities for CNPJ conversion and boleto barcode extraction

### 2. Cobrança workspace
- Main Alfa page for message generation
- UF-specific warning logic
- Serasa-related messaging
- Contact record modal generation
- Operator-linked identity in generated messages

### 3. Formatting tools
- Title formatting from Esfera text
- Updated-title formatting
- Cobrança-formatting helpers for WhatsApp/client/esfera text
- Rich text editor for free-form message formatting

### 4. Negotiation and compensation workflows
- Client negotiation registration
- Network negotiation registration
- Negotiation lifecycle views: in progress, finalized, cancelled
- Installment handling and payment tracking
- Compensation text generation
- Negotiation-to-compensation handoff

### 5. Transfer and PIX workflows
- Transfer registration
- Transfer stage tracking
- Transfer proof paste/download/email support
- PIX proof binary storage in IndexedDB
- PIX ZIP export

### 6. Client and network registries
- Client CRUD
- Network CRUD
- Client lookup and reuse by other modules

### 7. Boleto and document workflows
- Boleto issuance request logging
- Boleto metadata extraction from PDFs
- Barcode extraction utility
- Extrajudicial notification PDF generation
- Cobrança letter generation
- Quitação letter generation

### 8. Backup and portability workflows
- Full backup export/import
- Operator-scoped JS import format
- Client-only JSON import/export
- Boleto metadata JSON export/import

## Repository Shape
- `index.html`: page shells and DOM anchors for all workflows
- `style.css`: all static styling, layout, and modal/page presentation
- `script.js`: monolithic runtime containing all business behavior
- `notificação/`: static PDF templates used by document flows
- `operadores/`: operator data JS payload files, currently stubbed
- `calibri-regular.ttf`, `calibri-bold.ttf`: PDF font assets

## Current Architectural Reality
- `VERIFIED` The project is monolithic, not modularized by file.
- `VERIFIED` The project is still fully static-hostable.
- `VERIFIED` The most important future need is safe change governance, not immediate refactoring.
- `INFERRED` Future work should preserve the current local-first operating model even if code is later split into additional static JS files.

## What Future Changes Must Respect
- Storage compatibility matters as much as UI behavior.
- Operator scoping is a core data-boundary mechanism.
- PDF/template flows depend on exact filenames and text-placement heuristics.
- Import/export is part of the operational safety net and cannot be treated as optional.
- Static hosting constraints are architectural rules, not deployment preferences.
