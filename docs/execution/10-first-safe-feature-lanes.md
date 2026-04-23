# First Safe Feature Lanes

## Status
- `VERIFIED` These lanes are derived from the module priority matrix and existing feature rules.
- `VERIFIED` They are the recommended starting points after the execution pack is installed.

## Purpose
Give future Codex work a low-risk starting queue instead of beginning in the most coupled parts of the monolith.

## Recommended First Lanes

### 1. Formatting output improvements
- target modules:
  - `formatacao`
  - `formatacao-cobrancas`
  - `formatacao-texto`
- why safe:
  - low persistence impact
  - low route risk
  - strong additive surface
- examples:
  - new output presets
  - copy helpers
  - preview improvements
  - non-destructive formatting options

### 2. Contact-record helper quality-of-life
- target module:
  - `registro-contato`
- why safe:
  - localized state
  - narrow operator-scoped persistence
  - limited coupling to other business flows
- examples:
  - better templates
  - small search or filter additions
  - usability improvements around saved history blocks

### 3. Boleto issuance logging improvements
- target module:
  - `emissao-boletos`
- why safe:
  - relatively isolated workflow
  - clear localStorage footprint
  - low dependency on import/export and documents
- examples:
  - filters
  - sorting
  - better row actions
  - safer validation that does not change stored contract

### 4. Read-only UI quality-of-life on existing pages
- target modules:
  - client registry
  - network registry
  - formatting pages
  - contact helper
  - boleto issuance logging
- why safe:
  - mostly additive
  - can avoid storage contract changes
- examples:
  - filters
  - non-destructive table actions
  - improved empty states
  - small read-only metrics

### 5. Client and network registry usability improvements
- target modules:
  - `neg-cadastro`
  - `redes`
- why this is a later-safe lane:
  - still manageable, but persistence is important
  - changes must avoid contract drift
- examples:
  - input validation improvements
  - search improvements
  - export helpers that do not alter stored entities

## Guardrails For Safe Lanes
- do not change storage keys
- do not change operator-scoping rules
- do not change payload structures unless necessary
- do not touch the document generators or boleto parser
- do not add a backend or database

## Feature Categories To Defer
- operator unlock or session changes
- storage model changes
- import/export payload changes
- backup restore flow changes
- document template changes
- boleto parser rewrites
- negotiation data-model changes
- transfer workflow rewrites
- new binary persistence features

## Acceptance Guidance
A feature is a good first-lane candidate when:
- it is additive
- it stays inside a `Safest to extend first` or lower-end `Medium-risk` module
- it does not require new recovery contracts
- it does not touch document or operator boundaries

