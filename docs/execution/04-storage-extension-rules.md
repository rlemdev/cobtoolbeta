# Storage Extension Rules

## Status
- `VERIFIED` Cobtool must remain browser-local and file-based for persistence.
- `VERIFIED` New durable data must fit the current `localStorage` plus `IndexedDB` plus export/import model.

## Allowed Storage Layers
- `localStorage` for small structured data and UI preferences
- `IndexedDB` for binary or file-heavy data
- downloaded and imported files for backup, portability, and user-managed recovery

## Disallowed Storage Patterns
- backend databases
- hosted persistence services
- required APIs for core storage
- server sessions
- browser storage that bypasses recovery planning

## Rule 1: Choose the right storage type

### Use `localStorage` when
- the data is structured JSON
- the volume is modest
- the module already uses `localStorage`
- the data must be available immediately after reload

### Use `IndexedDB` when
- the data is binary
- the data can be large
- the data is file-oriented, like proofs or attachments

### Keep data in memory only when
- the state is transient
- the state is reconstructable
- losing it on reload is acceptable for the workflow

## Rule 2: Name keys conservatively

### localStorage
- preferred durable domain key: `cobtool_<domain>_v1`
- use `cobtoolScopedStorageKey(...)` for operator-owned business data
- keep global keys rare and justified

### IndexedDB
- preferred DB name: `cobtool_<domain>_db_v1`
- preferred store name: `<domain>_store`

## Rule 3: Default to operator-scoped business storage
Use operator scope unless the data is clearly global preference or global session state.

### Usually operator-scoped
- clients
- networks
- negotiations
- transfers
- operational logs
- workflow-specific business state

### Usually global
- theme
- last active operator session
- last visited page when used as shell state
- global UI counters or preferences

## Rule 4: New fields must be additive first
- default missing fields during load
- default missing fields during sanitize
- do not make old stored objects invalid without a migration path
- preserve current behavior for existing records

## Rule 5: Migrations are required when contracts change
Migration logic is required if any of the following happen:
- a storage key changes
- operator scope changes
- slug rules change
- a stored field changes meaning, not just presence
- an IndexedDB schema changes in a way old records cannot satisfy

## Rule 6: Backup coverage must be decided before shipping

### Full backup integration is required when
- the new data is durable business state
- recreating it manually would be costly
- the user would expect browser-loss recovery to restore it

### Full backup integration may be skipped when
- the state is ephemeral
- the output is already user-managed through explicit download
- the data is disposable UI helper state

If backup is skipped intentionally, document that limitation.

## Rule 7: Respect current storage mechanics
- preserve the localStorage wrapper behavior that reacts to `setItem`, `removeItem`, `clear`, and full replacement
- do not introduce a second persistence model that ignores the existing save and reload flow
- do not bypass current sanitize helpers when stored entities are touched

## Rule 8: Operator portability must remain safe
- operator-owned data must preserve suffix-based isolation
- operator import must not overwrite unrelated operators
- storage additions affecting operator data must be reviewed with `docs/import-export-map.md`

## Storage Change Checklist
- exact key names identified
- operator scope decision recorded
- load and save path identified
- sanitize path updated
- backup decision recorded
- migration need evaluated
- docs updated if the contract changed

## Acceptance Guidance
Storage work is only acceptable when:
- it remains browser-local
- it preserves operator boundaries
- reload behavior is safe
- backup compatibility is explicit

