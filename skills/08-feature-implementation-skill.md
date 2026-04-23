# Feature Implementation Skill

## Status
- `VERIFIED` Safe future work in Cobtool should be additive and contract-aware.
- `VERIFIED` New features must fit the current static, local-first operating model.

## Purpose
Provide the default implementation workflow for new features without skipping cross-cutting checks.

## When To Use It
- Any additive feature request
- Any workflow enhancement
- Any new UI action, helper, field, view, report, or module extension

## Affected Project Areas
- Varies by feature
- Always includes docs and regression planning

## Required Checks
1. Read the repo identity and GitHub Pages skills first.
2. Read `docs/execution/01-module-priority-matrix.md`.
3. Complete `docs/execution/02-feature-intake-template.md`.
4. Read `docs/execution/03-safe-implementation-workflow.md`.
5. Identify the owning module and affected pages.
6. Identify storage impact.
7. Identify import/export impact.
8. Identify operator-scope impact.
9. Identify document-flow impact.
10. Identify minimum regression scope.
11. Read `docs/feature-implementation-rules.md`, `docs/change-rules.md`, and `docs/testing-checklist.md`.

## Failure Risks
- Shipping an isolated UI change that breaks persistence or recovery
- Adding durable data without backup coverage
- Breaking operator scope through a seemingly local feature

## Do-Not Rules
- Do not start coding before mapping storage, UI, and compatibility impact
- Do not add durable data without deciding its recovery behavior
- Do not add a feature that only works with a backend or database
- Do not sneak in refactors that change business semantics unless explicitly requested

## Implementation Guidance
- Prefer extending existing modules before creating new ones
- Prefer additive fields and controls over semantic rewrites
- If new durable data is introduced, document it in `docs/persistence-map.md` and `docs/data-contracts.md`
- If routes, payloads, or risk zones change, update the relevant docs and `public/llms.txt`

## Acceptance Guidance
- The feature is implemented with no violation of static-hosting or no-database rules
- The affected docs are updated in the same task
- The intake and module classification are explicit
- The regression scope is explicit
