# Cobtool Persistence Map

## Status
- `VERIFIED` Cobtool persists business data in browser storage only.
- `VERIFIED` There is no database, backend session, or remote persistence layer in the current repository.
- `VERIFIED` Persisted data is split between `localStorage`, `IndexedDB`, and user-managed exported files.

## Persistence Architecture Summary

### Storage layers in use
| Layer | Purpose | Status |
| --- | --- | --- |
| `localStorage` | business entities, UI preferences, operator identity, page state, lightweight module state | `VERIFIED` |
| `IndexedDB` | binary PIX proof files | `VERIFIED` |
| downloaded files (`.json`, `.js`, `.zip`) | backup, portability, metadata exchange | `VERIFIED` |

### Local wrapper behavior
- `VERIFIED` `script.js` wraps native `window.localStorage` at startup.
- `VERIFIED` The wrapper notifies internal listeners on `setItem`, `removeItem`, `clear`, and full replacement.
- `VERIFIED` Full import uses this wrapper behavior, not a separate storage abstraction.

## Operator Scoping Model

### Key idea
- `VERIFIED` Most business-storage keys are operator-scoped through `cobtoolScopedStorageKey(baseKey)`.
- `VERIFIED` Operator-scoped keys are suffixed as `__<operator-slug>`.

### Operator slug rules
- `VERIFIED` The slug is derived from the normalized operator name.
- `VERIFIED` Slug generation:
  - lowercases
  - removes accents
  - replaces non-alphanumeric runs with `-`
  - trims leading/trailing `-`

### Legacy migration behavior
- `VERIFIED` If `localStorage[scopedKey]` is missing and an old unscoped `localStorage[baseKey]` exists, the code copies the old value into the new scoped key.
- `VERIFIED` This is an implicit migration path and must be preserved carefully.

## localStorage Inventory

| Key / Pattern | Scope | Domain | Stored Shape | Notes | Status |
| --- | --- | --- | --- | --- | --- |
| `ch_theme` | global | UI preference | string: `light` or `dark` | theme toggle | `VERIFIED` |
| `ct_message_counter_v1` | global | UI utility | `{ entries:number[] }` | rolling 24h counter | `VERIFIED` |
| `cobtool_alfa_operador_v1` | global | operator identity | operator name string | current operator anchor for scoped storage | `VERIFIED` |
| `cobtool_operator_session_v1` | global | operator session | `{ operator:string, lastActive:number }` | idle lock/session resume | `VERIFIED` |
| `ch_last_page` | global | UI state | route key string | last visited page/pending home restore | `VERIFIED` |
| `cobtool_alfa_ufs_v1__<slug>` | operator-scoped | Alfa cobrança | stringified string array | selected UF checkboxes | `VERIFIED` |
| `cobtool_clientes_v1__<slug>` | operator-scoped | client registry | JSON array | client entities | `VERIFIED` |
| `cobtool_redes_v1__<slug>` | operator-scoped | network registry | JSON array | network entities | `VERIFIED` |
| `cobtool_negociacoes_v1__<slug>` | operator-scoped | negotiations | JSON array | negotiation entities | `VERIFIED` |
| `cobtool_transferencias_v1__<slug>` | operator-scoped | transfers | JSON array | transfer entities | `VERIFIED` |
| `cobtool_pix_state_v1__<slug>` | operator-scoped | PIX transfer row state | JSON array | checkbox/status state for PIX rows | `VERIFIED` |
| `cobtool_emissao_boletos_v1__<slug>` | operator-scoped | boleto issuance | JSON array | boleto request log rows | `VERIFIED` |
| `cobtool_registro_contato_v1__<slug>` | operator-scoped | contact log helper | JSON object | `{ ultimos, historico }` | `VERIFIED` |
| `cobtool_representantes_v1__<slug>` | operator-scoped | representative lookup | JSON array | consumed by emissão-boletos search | `INFERRED` |

## IndexedDB Inventory

### PIX proof database
- `VERIFIED` Database name: `cobtool_pix_db_v1`
- `VERIFIED` Object store: `pix_store`
- `VERIFIED` Key path: `id`

### PIX proof record shape
```js
{
  id: string,
  codigo: string,      // normalized client code
  cnpj: string,        // 14 digits or source value
  valor: string,       // formatted BR value string
  filename: string,
  storedAt: string,    // ISO timestamp
  blob: Blob
}
```

### Why IndexedDB is used
- `VERIFIED` PIX proofs are binary files and too large/awkward for `localStorage`.
- `VERIFIED` The UI keeps checkbox row state in `localStorage`, but the actual file content in `IndexedDB`.

## Persisted vs Session-like vs Derived State

### Persisted business state
- Clients
- Networks
- Negotiations
- Transfers
- PIX row state
- PIX proof files
- Boleto issuance requests
- Registro de contato history state
- Selected Alfa UFs

### Persisted identity/session state
- Current operator identity
- Last active operator session
- Last page route
- Theme
- Message counter

### In-memory only state
- `currentPage`
- active modal item references
- transient pasted transfer proof blob before download
- temporary file parsing results before export/copy
- temporary client/network selection state during form edits

### Derived state
- negotiation progress percentages
- transfer stage labels
- KPI summaries
- compensation output text
- barcode displayed in the header helper
- `uiSnapshot` inside backup export

`uiSnapshot` is `VERIFIED` as an exported recovery convenience, but `INFERRED` as a secondary representation rather than the primary domain source of truth.

## Backup Interaction With Persistence

### Full backup export
- `VERIFIED` Exports the entire current `localStorage` key/value map.
- `VERIFIED` Also exports normalized arrays for major entities:
  - clients
  - networks
  - negotiations
  - transfers
- `VERIFIED` Serializes PIX proof blobs to base64 under `pixFiles`.

### Full backup import
- `VERIFIED` Calls `localStorage.clear()`.
- `VERIFIED` Rewrites all dumped keys from the imported payload.
- `VERIFIED` Re-saves major entity arrays to reduce loss when dump contents are partial.
- `VERIFIED` Clears and rebuilds PIX IndexedDB.
- `VERIFIED` Reapplies UI snapshot and reloads the page.

### Operator import
- `VERIFIED` Operator JS import does not clear all storage.
- `VERIFIED` It merges only keys matching the imported operator suffix and preserves other operators’ data.

## Persistence Constraints For Future Work
- `VERIFIED` New business persistence should remain browser-side or file-based.
- `VERIFIED` New operator-owned data should use operator-scoped keys unless there is a strong global reason not to.
- `VERIFIED` New binary/file-heavy data should prefer IndexedDB plus explicit export/import strategy.
- `VERIFIED` Storage key changes require migration logic and documentation updates.
