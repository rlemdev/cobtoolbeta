# Cobtool Import / Export Map

## Status
- `VERIFIED` Cobtool uses file-based portability and browser storage recovery instead of server-side sync.
- `VERIFIED` The current codebase implements multiple export/import flows with different purposes and compatibility expectations.

## Flow Inventory

| Flow | Entry Point | Output / Input | Persists Into App? | Purpose | Status |
| --- | --- | --- | --- | --- | --- |
| Full backup export | `import-export` page | `cobtool_backup.json` | yes, on re-import | full machine/browser recovery | `VERIFIED` |
| Full backup import | `import-export` page | `.json` | yes | restores app state | `VERIFIED` |
| Operator data import | `import-export` page | `.js` or JSON-like operator payload | yes | imports one operator’s scoped storage | `VERIFIED` |
| Client export | client registry | `cobtool_clientes_backup.json` | yes, on client import | module-level portability | `VERIFIED` |
| Client import | client registry | `.json` | yes | replaces/loads client registry list | `VERIFIED` |
| Negotiation export | negotiation list pages | clipboard TSV | no | reporting / tabular copy | `VERIFIED` |
| Transfer export | transfer list pages | clipboard TSV | no | reporting / tabular copy | `VERIFIED` |
| PIX proof export | PIX area | `comprovantes_pix.zip` | no | file extraction/archive | `VERIFIED` |
| Boleto metadata export | registro-boletos | JSON file | no app persistence; import is display-only | metadata sharing | `VERIFIED` |
| Boleto metadata import | registro-boletos | JSON file | no permanent module storage | render/copy imported metadata | `VERIFIED` |
| Operator JS files in `operadores/` | repository asset | `.js` files | yes, if imported | portable operator seed format | `VERIFIED` |

## 1. Full Backup Export

### Entry point
- `VERIFIED` `#imp-exp-exportar` on `page-import-export`.

### Payload type
- `VERIFIED` `cobtool_backup_v2`

### Included data
- `VERIFIED` Raw `localStorage` dump
- `VERIFIED` Entity arrays:
  - `clientes`
  - `redes`
  - `negociacoes`
  - `transferencias`
- `VERIFIED` `uiSnapshot`
  - current page
  - compensation mode
  - checkbox state
  - selected radio values
  - range values
- `VERIFIED` `pixFiles`
  - IndexedDB PIX proof records serialized with base64 file data

### Export behavior
- `VERIFIED` The file is generated client-side as JSON via `Blob`.
- `VERIFIED` Filename is fixed as `cobtool_backup.json`.

## 2. Full Backup Import

### Accepted source
- `VERIFIED` `.json` selected in `#imp-exp-importar`

### Restore behavior
- `VERIFIED` Parses JSON and branches on `payloadType`.
- `VERIFIED` For `cobtool_backup_v2`:
  - clears `localStorage`
  - rewrites all imported localStorage keys
  - re-saves entity arrays using current save helpers
  - clears PIX IndexedDB
  - rebuilds PIX IndexedDB from `pixFiles`
  - restores `uiSnapshot`
  - refreshes in-memory lists
  - reapplies theme
  - reloads the page

### Compatibility implications
- `VERIFIED` Because the restore path clears and rewrites storage, missing keys in the imported payload can remove data.
- `VERIFIED` The redundant entity-array re-save path is a defensive recovery step for partial dumps.

## 3. Operator Data Import

### Accepted source
- `VERIFIED` `.js` or JSON-like payload containing `cobtool_operator_data_v1`

### Parser behavior
- `VERIFIED` Import first tries JSON parse.
- `VERIFIED` If JSON parse fails, it tries to extract:
  - `window.COBTOOL_OPERATOR_DATA = {...}`
  - `const COBTOOL_OPERATOR_DATA = {...}`

### Restore behavior
- `VERIFIED` The importer:
  - normalizes operator name
  - computes that operator’s suffix
  - keeps all current keys that do not end with the imported suffix
  - replaces only keys ending with that suffix
- `VERIFIED` This prevents cross-operator overwrite when used correctly.

### Important caveat
- `VERIFIED` The imported `localStorage` object may contain many keys, but only suffix-matching keys are applied.
- `VERIFIED` Files currently stored in `operadores/` are stub payloads with empty `localStorage`.

## 4. Client Module Import / Export

### Export
- `VERIFIED` Client registry exports the current client array as JSON file `cobtool_clientes_backup.json`.

### Import
- `VERIFIED` Client registry imports a JSON array and replaces the active client list.
- `VERIFIED` Imported records are re-sanitized through current client normalization.

### Scope
- `VERIFIED` This is module-specific and does not capture negotiations, networks, transfers, or PIX files.

## 5. Negotiation and Transfer Table Exports

### Negotiations
- `VERIFIED` `neg-andamento`, `neg-finalizadas`, and `neg-canceladas` export TSV to clipboard.
- `VERIFIED` These exports are reporting/copy flows only.

### Transfers
- `VERIFIED` `tr-andamento` and `tr-finalizadas` export TSV to clipboard.
- `VERIFIED` These exports are not re-importable app payloads.

## 6. PIX Proof ZIP Export

### Entry point
- `VERIFIED` PIX export button in the transfer/PIX workflow.

### Output
- `VERIFIED` ZIP file `comprovantes_pix.zip`

### Included data
- `VERIFIED` Saved proof files from IndexedDB only.
- `VERIFIED` Row checkbox state is not embedded in the ZIP.

### Internal implementation note
- `VERIFIED` ZIP generation is implemented manually in `script.js`; no external zip library is used.

## 7. Boleto Metadata JSON Export / Import

### Export
- `VERIFIED` Registro-boletos exports payload type `cobtool_registro_boletos_metadados_v1`.
- `VERIFIED` File name format:
  - `Boletos Enviados - DD-MM-YYYY - <Operator>.json`

### Import
- `VERIFIED` Registro-boletos import accepts:
  - the full payload object with `rows`
  - a raw array of row objects
- `VERIFIED` Imported rows are normalized and shown in a separate import panel.
- `VERIFIED` Import does not write rows into main application persistence.

### Special restriction
- `VERIFIED` The import panel is visible only when the current operator label resolves to `Rafael`.

## 8. Legacy Import Branch

### Supported shape
- `VERIFIED` Import code still supports a non-`payloadType` branch shaped like:
  - `clientes`
  - `redes`
  - `negociacoes`
  - `transferencias`
  - `pix.rows`
  - `pix.files`

### Interpretation
- `INFERRED` This is a legacy backup format kept for backward compatibility.
- `VERIFIED` Future removals of this path require an explicit compatibility decision and migration note.

## Compatibility Risks

### High-risk compatibility breaks
- Changing `payloadType` values
- Renaming storage keys without migration
- Changing operator suffix logic
- Making required fields mandatory in imported payloads without defaults
- Dropping the legacy import path casually
- Forgetting to include new persisted domains in full backup export/import
- Changing PIX proof serialization shape

### Medium-risk compatibility breaks
- Reordering table columns in clipboard exports without coordination
- Changing boleto metadata export filename format if downstream manual processes expect it
- Changing `rows` normalization semantics in registro-boletos import/export

## Preservation Rules For Future Work
- New persisted data that matters for recovery must be added to full backup export/import.
- New operator-scoped data must preserve operator suffixing.
- New binary/file data must define export and import behavior explicitly.
- Module-specific exports should state whether they are round-trip safe or one-way only.
- Backward compatibility notes must be written whenever payload shapes change.
