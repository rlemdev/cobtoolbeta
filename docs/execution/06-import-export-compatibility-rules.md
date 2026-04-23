# Import Export Compatibility Rules

## Status
- `VERIFIED` Import and export are part of Cobtool's recovery model, not optional convenience features.
- `VERIFIED` Compatibility is controlled by payload contracts, storage keys, operator suffix rules, and restore order.

## Canonical Payload Contracts
- full backup: `cobtool_backup_v2`
- operator payload: `cobtool_operator_data_v1`
- boleto metadata payload: `cobtool_registro_boletos_metadados_v1`

These values are compatibility contracts. Do not change them casually.

## Rule 1: Treat full backup as recovery-critical
Any feature that adds durable business state must answer this before shipping:
- should full backup export it?
- should full backup import restore it?

If the answer is no, document why the state is intentionally excluded.

## Rule 2: Prefer additive payload evolution
Safer compatibility changes:
- additive top-level fields
- additive entity fields with defaults
- additive metadata that old imports can ignore

Higher-risk changes:
- renaming required fields
- changing payload meaning
- changing storage keys without migration
- changing restore order

## Rule 3: Do not change restore semantics blindly
Current full restore behavior includes:
- `localStorage.clear()`
- rewriting stored keys
- re-saving major entity arrays
- clearing and rebuilding PIX IndexedDB
- restoring UI snapshot
- reloading the page

Any change here must be justified against recovery behavior and tested as a full round trip.

## Rule 4: Preserve operator isolation during import
- operator import must only replace keys ending with the imported operator suffix
- unrelated operators must remain untouched
- operator normalization and suffix logic must stay stable unless a migration is provided

## Rule 5: Legacy compatibility is not removable by accident
- the legacy import branch for older PIX backup shape still exists
- removing or tightening compatibility requires an explicit deprecation decision
- document the decision in `docs/import-export-map.md`

## Rule 6: New binary persistence needs explicit serialization rules
If new file-heavy data is added:
- define export format
- define import format
- define failure behavior
- define whether the data belongs in full backup or a dedicated export flow

## Rule 7: Module-specific exports must declare their purpose

### Round-trip capable flows
- full backup export/import
- operator import
- client JSON import/export

### One-way or display-only flows
- TSV clipboard exports
- PIX ZIP export
- boleto metadata import panel rendering

Do not let a one-way export silently become the only backup path for durable data.

## Rule 8: Compatibility notes are mandatory when payloads change
If a payload or restore path changes, update:
- `docs/data-contracts.md`
- `docs/import-export-map.md`
- `docs/change-rules.md` if the hard-rule surface changed
- `public/llms.txt` if the machine-readable contract changed

## High-Risk Change Examples
- changing `payloadType`
- changing key naming without migration
- altering operator import merge logic
- altering PIX serialization shape
- adding durable data but forgetting import support
- tightening required imported fields with no defaults

## Acceptance Guidance
Import/export work is only acceptable when:
- payload compatibility is explicit
- operator separation is preserved
- round-trip expectations are clear
- recovery-critical flows are tested or test gaps are stated

