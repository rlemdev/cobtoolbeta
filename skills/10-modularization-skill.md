# Modularization Skill

## Status
- `VERIFIED` Cobtool is currently monolithic in `script.js`.
- `VERIFIED` Some cleanup and extraction work is possible, but dangerous rewrites should be deferred.

## Purpose
Guide gradual client-side modularization without breaking current behavior or GitHub Pages compatibility.

## When To Use It
- Refactor requests
- Code extraction requests
- Efforts to split `script.js` into smaller browser-loaded modules

## Affected Project Areas
- `script.js`
- Shared helpers
- Navigation shell
- Persistence helpers
- Import/export logic
- Document flows

## Required Checks
1. Confirm the refactor preserves runtime behavior and deployment model.
2. Identify any affected storage keys, payloads, routes, globals, or asset paths.
3. Read `docs/execution/09-modularization-roadmap.md`.
4. Read `docs/refactor-boundaries.md`, `docs/module-map.md`, and `docs/risk-zones.md`.
5. Keep the work strictly client-side and static-hosting compatible.

## Failure Risks
- Breaking hidden dependencies inside the monolith
- Accidentally changing business behavior while moving code
- Introducing build-step or runtime assumptions that do not fit GitHub Pages

## Do-Not Rules
- Do not start with operator gate, import/export, or document flows unless there is an explicit plan
- Do not change deployment model as part of a refactor
- Do not combine modularization with unrelated feature work unless the user explicitly wants both

## Implementation Guidance
- Safer extraction targets:
  - pure formatting helpers
  - constants and key names
  - storage helper wrappers
  - low-coupling render helpers
- Medium-risk extraction targets:
  - registry table renderers
  - isolated utility modules
  - non-document formatter flows
- Dangerous monolith areas:
  - operator session gate
  - unified import/export
  - boleto parser
  - notification and letter generators
  - negotiation and transfer coupling

## Acceptance Guidance
- The refactor preserves existing routes, keys, payloads, and asset paths unless explicitly migrated
- Runtime still works as static client-side code
- Any changed boundaries are documented for future tasks
