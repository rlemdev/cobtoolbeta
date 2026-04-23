# Safe Implementation Workflow

## Status
- `VERIFIED` This is the mandatory implementation order for future Codex work in Cobtool.
- `VERIFIED` The workflow is intentionally conservative because the app is monolithic, local-first, and contract-sensitive.

## Mandatory Order

### 1. Read the operating layer
- Read `AGENTS.md`
- Read `skills/00-skill-index.md`
- Read `docs/execution/00-execution-index.md`
- Read `docs/execution/11-codex-default-operating-sequence.md`

### 2. Identify the module and risk class
- Use `docs/execution/01-module-priority-matrix.md`
- Identify the highest-risk touched module
- If any touched module is `Do-not-touch-without-explicit-plan`, write the explicit plan before coding

### 3. Complete feature intake
- Fill `docs/execution/02-feature-intake-template.md`
- Record:
  - affected modules
  - affected routes
  - storage impact
  - import/export impact
  - operator impact
  - document impact
  - regression scope

### 4. Read the specialized execution rules
- storage change: `04-storage-extension-rules.md`
- UI or route change: `05-ui-extension-rules.md`
- import/export change: `06-import-export-compatibility-rules.md`
- document or parser change: `07-document-flow-change-rules.md`
- refactor change: `09-modularization-roadmap.md`

### 5. Read the detailed source docs
- `docs/module-map.md`
- `docs/ui-map.md`
- `docs/persistence-map.md`
- `docs/import-export-map.md`
- `docs/pdf-and-document-map.md`
- `docs/operator-session-map.md`
- `docs/risk-zones.md`
- `docs/change-rules.md`
- `docs/testing-checklist.md`
- `docs/github-pages-constraints.md`

Read only the subsets that actually apply, but do not skip the relevant ones.

### 6. Inspect the code and assets directly
- inspect `index.html` for UI structure
- inspect `style.css` for visual and modal patterns
- inspect `script.js` for the actual runtime paths
- inspect templates, fonts, or operator payload assets if the task touches them

### 7. Map compatibility impact before edits
- Which keys are read and written?
- Which routes and DOM ids are affected?
- Which payloads or file formats are affected?
- Does operator scoping apply?
- Does backup/import need updating?
- Does a document flow need extra regression?

### 8. Implement the smallest safe change
- prefer additive behavior
- avoid semantic rewrites
- do not mix feature work with opportunistic refactor unless explicitly requested

### 9. Verify against the regression matrix
- Use `docs/execution/08-regression-matrix.md`
- Use `docs/testing-checklist.md` for detailed manual steps
- If checks were not run, record that explicitly

### 10. Update the operating docs
- update `docs/` if contracts changed
- update `docs/execution/` if the default process changed
- update `AGENTS.md` if the mandatory operating rules changed
- update `public/llms.txt` if machine-readable project understanding changed

## Stop Conditions
Stop and re-plan before coding if:
- the task needs a backend, database, or server-only runtime
- the task touches operator scoping with no dependency map
- the task changes backup/import with no compatibility story
- the task changes PDF or parser behavior with no sample verification plan
- the task changes navigation shell or route maps without a full path review

## Completion Standard
An implementation task is only complete when:
- the workflow was followed in order
- the regression scope is explicit
- compatibility-sensitive docs are updated
- the final handoff distinguishes code inspection from runtime verification

