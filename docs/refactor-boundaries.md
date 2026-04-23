# Cobtool Refactor Boundaries

## Status
- `VERIFIED` The repository is currently a static monolith centered on `script.js`.
- `VERIFIED` Refactoring is possible, but only if the local-first runtime, operator scoping, import/export safety, and document flows remain intact.

## Guiding Principle
Refactor only where the behavioral surface is well understood and regression cost is low. Avoid rewriting the monolith before the module contracts are proven stable through documentation and regression testing.

## 1. Safe Cleanup

### Safe now
- add documentation and governance files
- add comments around fragile code paths
- extract repeated pure helpers inside the same file
- normalize small naming inconsistencies in internal variables only
- centralize repeated string/format helpers without changing call behavior
- add non-invasive guards and null checks
- clean obviously dead UI text or duplicated styling only after visual verification

### Why safe
- These changes do not alter storage shapes, route maps, or business semantics.

## 2. Medium-Risk Modularization

### Possible next, with explicit verification
- split pure utilities into additional static JS files loaded by `index.html`
- extract storage helpers into a dedicated static file
- extract import/export helpers into a dedicated static file
- extract boleto parsing helpers into a dedicated static file without changing algorithms
- extract PDF shared helpers into a dedicated static file
- extract page-init blocks by logical module while preserving global bridges

### Conditions for safety
- no bundler requirement
- no Node runtime requirement
- no change to public/static hosting model
- `index.html` still works as a static entrypoint
- all current globals used cross-module remain available or are intentionally re-exported

## 3. Dangerous Rewrites

### Defer unless there is an explicit plan
- rewriting navigation/state management
- changing all page ids or route names
- replacing operator-scoped storage model
- redesigning entity contracts for negotiations or transfers
- rewriting backup/import orchestration
- changing PIX persistence model from dual localStorage/IndexedDB
- replacing document generators or boleto parser logic wholesale
- migrating to a backend, API, or database
- moving to a framework/bundler that changes deployment assumptions

### Why dangerous
- These areas have multi-module coupling and high regression cost.

## 4. Monolith Hotspots

### Safest extraction targets
- generic string/date/format helpers
- localStorage/normalization helpers
- clipboard/export helper functions
- non-persistent formatting modules

### Medium-risk extraction targets
- client registry
- network registry
- boleto issuance logging
- contact record helper
- topbar utilities

### Dangerous monolith areas
- operator gate and route shell
- unified backup/import
- negotiations
- transfers + PIX proofs
- extrajudicial notification generator
- cobrança/quitação letter generators
- boleto PDF parser

## 5. Refactor Stop Conditions
Stop and re-plan if a refactor would require any of the following:

- changing persisted key names
- changing payload shapes
- changing operator slugs or session keys
- changing template filenames or folder paths
- changing `pages`, `PAGE_PARENT`, or `pageIds`
- changing cross-module globals before all dependents are mapped

## 6. Recommended Refactor Order Later
1. document and test
2. extract pure helpers
3. extract shared storage/import/export utilities
4. extract isolated page modules
5. revisit high-risk workflow extraction only after repeated successful feature work
