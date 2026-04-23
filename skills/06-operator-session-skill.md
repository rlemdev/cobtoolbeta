# Operator Session Skill

## Status
- `VERIFIED` Operator identity is a core data boundary in Cobtool.
- `VERIFIED` Operator identity affects session state, scoped storage, some document selection, and import/export behavior.

## Purpose
Prevent operator-related changes from breaking data isolation or unlock/session behavior.

## When To Use It
- Touching unlock flow
- Touching operator session timeout logic
- Touching operator names or operator-specific behavior
- Touching scoped storage helpers
- Touching operator import/export logic

## Affected Project Areas
- Unlock page
- Session keys
- Scoped storage
- Alfa workspace
- Document template selection
- Boleto metadata operator labeling

## Required Checks
1. Preserve `cobtool_alfa_operador_v1` and `cobtool_operator_session_v1` unless a migration exists.
2. Preserve slug stability for `__<operator-slug>` suffixes.
3. Preserve pending-page restore after unlock.
4. Trace operator identity impact on Alfa, document flows, and operator payload handling.
5. Read `docs/execution/04-storage-extension-rules.md` if operator-owned data is touched.
6. Read `docs/execution/06-import-export-compatibility-rules.md` if operator payloads or restore behavior are touched.
7. Read `docs/operator-session-map.md` and `docs/persistence-map.md`.

## Failure Risks
- Mixed data between operators
- Permanent lock or failed unlock restore
- Wrong template selection by operator
- Operator import/export affecting the wrong records

## Do-Not Rules
- Do not change operator slug generation casually
- Do not merge operator data spaces
- Do not remove idle-session handling without explicit replacement
- Do not rename operators without checking template filenames and stored suffixes

## Implementation Guidance
- Treat operator identity as a first-class runtime contract
- When adding new durable operator-owned data, scope it consistently
- When touching operator lists or labels, inspect any template naming and export labeling that depends on them

## Acceptance Guidance
- Unlock still works
- Switch operator still returns to the lock screen
- Each operator still sees only their own scoped data
- Any operator-related contract changes are documented
