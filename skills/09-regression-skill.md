# Regression Skill

## Status
- `VERIFIED` Manual regression is the primary safety net for Cobtool.
- `VERIFIED` High-risk changes require targeted testing before close-out.

## Purpose
Ensure future Codex work closes with the right verification claims and the right residual-risk notes.

## When To Use It
- Before closing any change
- Especially after changes to storage, routing, operator scope, import/export, negotiations, transfers, or document flows

## Affected Project Areas
- Entire repository

## Required Checks
1. Select the touched modules.
2. Select the minimum regression bundle from `docs/execution/08-regression-matrix.md`.
3. Run the minimum relevant checks from `docs/testing-checklist.md`.
4. If you cannot run a check, state that clearly in the final handoff.
5. For high-risk areas, prefer module-specific regression over generic smoke testing.

## Failure Risks
- Silent breakage in a different page or operator context
- Overstating confidence when runtime testing did not happen
- Shipping a backup or document regression that code review alone would miss

## Do-Not Rules
- Do not claim a flow was verified if it was only inspected in code
- Do not close a storage or document change without stating what was or was not tested
- Do not use only a homepage smoke test for multi-module work

## Implementation Guidance
- Minimum examples:
  - storage: unlock, reload, save/load, switch operator
  - import/export: backup export/import round trip
  - documents: generate PDFs, parse boleto PDF, test barcode helper
  - navigation: entry buttons, back path, touched modals
- Use `docs/execution/08-regression-matrix.md` to choose scope and `docs/testing-checklist.md` to execute detailed manual checks

## Acceptance Guidance
- The final handoff distinguishes code inspection from runtime verification
- The user can see the residual risk if some checks were not run
- High-risk touched areas have explicit regression coverage or explicit test gaps
