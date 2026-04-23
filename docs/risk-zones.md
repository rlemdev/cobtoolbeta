# Cobtool Risk Zones

## Status
- `VERIFIED` Risk zones below are based on actual coupling, persistence behavior, parser/generator complexity, and DOM dependence.

## Severity Guide
- `High`: likely to break recovery, operator separation, or core workflows
- `Medium`: localized breakage with manageable blast radius if tested well
- `Watch`: lower direct risk, but still coupled enough to require checks

## Risk Matrix

| Risk Zone | Severity | Why It Is Risky | Dangerous Change Types | Minimum Safeguard | Status |
| --- | --- | --- | --- | --- | --- |
| Full backup import/export | High | touches all localStorage, PIX IndexedDB, UI snapshot, reload behavior | changing payloads, removing fields, changing restore order | export/import round-trip test | `VERIFIED` |
| Operator scoping and session gate | High | defines data boundaries per operator and unlock behavior | changing keys, slugs, idle logic, operator names | switch-operator and per-operator storage tests | `VERIFIED` |
| PDF template generation | High | depends on fixed filenames, coordinates, and text anchors | changing template names, offsets, fonts, anchor strings | generate all PDFs after change | `VERIFIED` |
| Boleto PDF parsing | High | heuristic extraction from variable PDF layouts | changing regexes, line grouping, extractor order | parse known sample boletos and compare fields | `VERIFIED` |
| Navigation maps and DOM ids | High | runtime binds directly to ids and route names | renaming ids, sections, route keys, `data-page` values | full page-navigation regression | `VERIFIED` |
| Negotiation engine | High | rich state, lifecycle transitions, installment math, compensation coupling | changing entity shape, parcel logic, close/reopen logic | negotiation lifecycle regression | `VERIFIED` |
| Transfers + PIX dual persistence | High | combines localStorage row state with IndexedDB files and negotiation linkage | changing PIX ids, proof save/reset flow, transfer status logic | transfer + PIX save/export/import tests | `VERIFIED` |
| Client/network relationship | Medium | networks store client snapshots and negotiations depend on both | changing client ids or network member shape | client/network/negotiation regression | `VERIFIED` |
| Boleto issuance lookup store | Medium | depends on external representative data contract not owned here | changing expected lookup shape | emissão-boletos search test | `INFERRED` |
| Header utilities | Watch | small UI, but barcode helper depends on boleto parser globals | removing global exposure, renaming ids | topbar utility smoke test | `VERIFIED` |
| Theme and message counter | Watch | low business criticality, but globally persistent | changing keys or cleanup logic | cold reload smoke test | `VERIFIED` |

## Detailed Risk Notes

### 1. Full backup import/export
- `VERIFIED` Import runs `localStorage.clear()`.
- `VERIFIED` Import then rebuilds entity stores and PIX files and finally reloads.
- Dangerous changes:
  - changing `payloadType`
  - not exporting a newly persisted module
  - forgetting to import a newly exported module
  - changing raw `localStorage` expectations

### 2. Operator scoping
- `VERIFIED` Business data isolation depends on suffixing keys by operator slug.
- Dangerous changes:
  - changing `cobtoolScopedStorageKey`
  - changing slug normalization
  - renaming operators without template/storage review
  - switching global keys to scoped or vice versa without migration

### 3. Document flows
- `VERIFIED` Letter generators and notification generator are strongly coupled to:
  - base PDF filenames
  - `notificação/` path
  - text anchors inside templates
  - local font availability
- Dangerous changes:
  - replacing templates
  - changing overlay offsets
  - changing font loading
  - altering page assembly order

### 4. Boleto parsing
- `VERIFIED` Field extraction depends on positional heuristics and regexes.
- Dangerous changes:
  - simplifying extractors without sample-PDF validation
  - changing line-merging tolerances
  - removing Itaú-specific fallback

### 5. UI bindings and page switching
- `VERIFIED` Initialization code often aborts if expected DOM ids are missing.
- `VERIFIED` Route names and DOM section ids are part of runtime behavior.
- Dangerous changes:
  - renaming `page-...` ids
  - removing `data-page` hooks
  - altering `PAGE_PARENT` without back-navigation review

### 6. Coupled business flows
- `VERIFIED` Negotiations feed compensation text and transfer matching.
- `VERIFIED` Transfers can jump into compensation workflows.
- `VERIFIED` Registro-boletos exposes barcode extraction to the header tool.
- Dangerous changes:
  - changing negotiation snapshots
  - changing transfer matching fields
  - changing exported globals

### 7. Static hosting assumptions
- `VERIFIED` Document libraries come from CDN.
- `VERIFIED` Templates are fetched as static assets.
- Dangerous changes:
  - adding runtime server dependency
  - moving assets behind authenticated endpoints
  - assuming filesystem write access beyond download prompts

## Do-Not-Touch Casually List
- `cobtoolScopedStorageKey(...)`
- `cobtool_operator_session_v1`
- `cobtool_alfa_operador_v1`
- `payloadType: "cobtool_backup_v2"`
- `payloadType: "cobtool_operator_data_v1"`
- `payloadType: "cobtool_registro_boletos_metadados_v1"`
- `pages`, `PAGE_PARENT`, `pageIds`
- `window.__cobtoolRegistroBoletosExtractBarcode`
- template filenames in `notificação/`
- PIX IndexedDB schema (`cobtool_pix_db_v1` / `pix_store`)
