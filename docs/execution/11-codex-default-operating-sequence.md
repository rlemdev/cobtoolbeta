# Codex Default Operating Sequence

## Status
- `VERIFIED` This is the default future sequence Codex should follow for implementation work in Cobtool.
- `VERIFIED` It is the shortest mandatory path through `AGENTS.md`, `skills/`, `docs/execution/`, and the detailed docs.

## Sequence

### 1. Confirm repo constraints
- read `AGENTS.md`
- confirm: GitHub Pages only, no database, browser-local persistence, no backend dependency

### 2. Read the repo skill layer
- `skills/00-skill-index.md`
- `skills/01-repo-identity-skill.md`
- `skills/02-github-pages-skill.md`
- plus any task-specific skill that applies

### 3. Read the execution baseline
- `docs/execution/00-execution-index.md`
- `docs/execution/03-safe-implementation-workflow.md`
- `docs/execution/01-module-priority-matrix.md`
- `docs/execution/08-regression-matrix.md`

### 4. Classify the task before coding
- identify touched modules
- identify highest-risk classification
- if `Do-not-touch-without-explicit-plan` appears, write the explicit plan first

### 5. Complete feature intake
- use `docs/execution/02-feature-intake-template.md`
- record storage, UI, import/export, operator, and document impact

### 6. Read the specialized execution rules
- storage: `04-storage-extension-rules.md`
- UI: `05-ui-extension-rules.md`
- import/export: `06-import-export-compatibility-rules.md`
- documents: `07-document-flow-change-rules.md`
- refactor: `09-modularization-roadmap.md`

### 7. Read the detailed source docs
- pull in only the detailed docs needed by the touched surfaces
- do not skip:
  - `docs/risk-zones.md`
  - `docs/change-rules.md`
  - `docs/testing-checklist.md`

### 8. Inspect code and assets directly
- inspect the touched HTML
- inspect the touched JS logic
- inspect any touched templates, fonts, or import/export files

### 9. Implement the smallest safe change
- prefer additive work
- preserve contracts
- avoid opportunistic rewrites

### 10. Verify before closing
- use `docs/execution/08-regression-matrix.md`
- use `docs/testing-checklist.md` for detailed manual coverage
- separate code-inspection confidence from actual runtime verification

### 11. Update the operating layer when needed
- update `docs/` for contract changes
- update `docs/execution/` for process changes
- update `AGENTS.md` if the mandatory workflow changes
- update `public/llms.txt` if machine-readable project understanding changes

## Mandatory Stop Conditions
Stop and ask for alignment before implementation if:
- the task requires a backend or database
- the task changes operator scoping with no migration or dependency review
- the task changes import/export contracts with no compatibility notes
- the task changes parser or document logic with no verification plan
- the task changes navigation shell behavior with no route-map review

## Final Handoff Standard
Every implementation handoff should state:
- what changed
- which modules were touched
- what was verified in runtime
- what was only inspected in code
- what residual risk remains

