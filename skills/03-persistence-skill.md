# Persistence Skill

## Status
- `VERIFIED` Cobtool persists durable state through `localStorage`, `IndexedDB`, and exported/imported files.
- `VERIFIED` Most business data is operator-scoped by suffixing keys with `__<operator-slug>`.

## Purpose
Keep persistence changes safe, compatible, and recoverable.

## When To Use It
- Touching `localStorage`
- Touching `IndexedDB`
- Adding or changing durable entities
- Changing storage keys, loaders, savers, sanitizers, or migration logic

## Affected Project Areas
- Business entities
- Operator-scoped stores
- PIX proof storage
- Backup and restore behavior

## Required Checks
1. Identify the exact keys read and written.
2. Decide whether the data is global, operator-scoped, binary, ephemeral, or derived.
3. Preserve key names unless there is a migration path.
4. If fields are added, default them during load and sanitize paths.
5. Read `docs/execution/04-storage-extension-rules.md`.
6. Read `docs/execution/06-import-export-compatibility-rules.md` if the stored data affects recovery.
7. Read `docs/persistence-map.md` and `docs/data-contracts.md`.

## Failure Risks
- Data loss after reload
- Cross-operator data leakage
- Orphaned legacy data from key renames
- Full backup missing newly added durable records

## Do-Not Rules
- Do not rename keys without migration logic
- Do not change operator slugging casually
- Do not store durable operator-owned business data in unscoped keys without a documented reason
- Do not move durable data to a server-side store

## Implementation Guidance
- Preferred durable business key pattern: `cobtool_<domain>_v1`
- Use `cobtoolScopedStorageKey(...)` for operator-owned business data
- Prefer `IndexedDB` for binary or large payloads and define serialization rules before shipping
- Treat additive fields as backward-compatible only if missing values are safely defaulted

## Acceptance Guidance
- Reload preserves the intended data
- Switching operators does not leak or overwrite data
- Full backup implications are documented
- `docs/persistence-map.md`, `docs/data-contracts.md`, `docs/execution/04-storage-extension-rules.md` when needed, and `public/llms.txt` are updated if the contract changed
