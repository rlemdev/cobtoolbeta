# Import Export Skill

## Status
- `VERIFIED` Backup and portability are part of Cobtool's operational safety model.
- `VERIFIED` The app currently depends on stable `payloadType` contracts for import dispatch.

## Purpose
Preserve recovery, portability, and backward compatibility when import/export behavior changes.

## When To Use It
- Changing backup/export/import code
- Adding durable persisted data
- Changing payload structure
- Touching operator data import/export
- Touching PIX serialization or boleto metadata payloads

## Affected Project Areas
- `import-export` page
- Full backup payloads
- Operator payloads
- Boleto metadata export/import
- PIX proof export/import

## Required Checks
1. Identify which payloads are affected.
2. Preserve `payloadType` unless a deliberate compatibility strategy exists.
3. Decide whether the new durable data belongs in full backup export/import.
4. Preserve operator isolation during operator payload import.
5. Read `docs/execution/06-import-export-compatibility-rules.md`.
6. Read `docs/import-export-map.md`, `docs/data-contracts.md`, and `docs/change-rules.md`.

## Failure Risks
- Unrecoverable data after import
- Older backups no longer restoring correctly
- Operator imports overwriting the wrong data
- PIX or binary payloads failing to round-trip

## Do-Not Rules
- Do not change `cobtool_backup_v2` casually
- Do not change `cobtool_operator_data_v1` casually
- Do not change `cobtool_registro_boletos_metadados_v1` casually
- Do not clear storage in a new order without understanding restore dependencies
- Do not remove legacy import handling without an explicit deprecation decision

## Implementation Guidance
- Keep payload extensions additive when possible
- If new durable data matters to recovery, update both export and import in the same task
- Document compatibility notes in `docs/import-export-map.md`
- Update `docs/data-contracts.md` when payload shapes change

## Acceptance Guidance
- A full backup round trip is still valid in principle and in test when possible
- Operator-specific import still affects only the intended scoped keys
- Any changed payload contract is documented
