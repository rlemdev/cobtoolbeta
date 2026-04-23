[00-execution-index.md](https://github.com/user-attachments/files/26997098/00-execution-index.md)
# Cobtool Execution Index

## Status
- `VERIFIED` This folder is the action layer built on top of `AGENTS.md`, `skills/`, and the detailed maps in `docs/`.
- `VERIFIED` Its purpose is to turn passive understanding into a standard implementation system for future Codex tasks.

## Purpose
Use this pack before any feature, bug fix, refactor, or risky maintenance task that changes Cobtool behavior. The execution pack does not replace the detailed docs. It tells future Codex work how to use them in a repeatable order.

## Source Hierarchy
1. `AGENTS.md`
2. `skills/`
3. `docs/execution/`
4. `docs/`
5. direct inspection of `index.html`, `style.css`, `script.js`, and affected assets

If these layers disagree, inspect code and resolve the mismatch by updating the docs rather than guessing.

## Layer Responsibilities
- `AGENTS.md`: hard constraints and mandatory posture
- `skills/`: choose which topical guidance applies
- `docs/execution/`: decide implementation order, intake, risk handling, and regression scope
- `docs/`: supply the detailed project facts and contracts
- direct code inspection: confirm what the current runtime actually does

## Mandatory Read Order Before Implementation
1. Read `AGENTS.md`.
2. Read `skills/00-skill-index.md`, `skills/01-repo-identity-skill.md`, and `skills/02-github-pages-skill.md`.
3. Read `11-codex-default-operating-sequence.md`.
4. Read `03-safe-implementation-workflow.md`.
5. Read `01-module-priority-matrix.md`.
6. Fill `02-feature-intake-template.md`.
7. Read the task-specific execution rules:
   - storage: `04-storage-extension-rules.md`
   - UI and routes: `05-ui-extension-rules.md`
   - backup and payloads: `06-import-export-compatibility-rules.md`
   - documents and boleto parsing: `07-document-flow-change-rules.md`
   - regression scope: `08-regression-matrix.md`
   - refactor planning: `09-modularization-roadmap.md`
   - safe starting points: `10-first-safe-feature-lanes.md`
8. Read the relevant detailed docs in `docs/`.
9. Inspect the affected implementation code directly.

## What This Pack Controls
- module priority and risk classification
- the feature-intake shape for future prompts
- the mandatory implementation order
- browser-local storage extension rules
- UI and page-switching extension rules
- import/export compatibility rules
- PDF and document change rules
- regression planning
- safe modularization boundaries
- the first recommended feature lanes

## What This Pack Does Not Change
- the deployment model
- the local-first persistence model
- the existing business logic
- the static GitHub Pages requirement

## Execution Pack Routing

### If the task adds or changes durable data
- Read `04-storage-extension-rules.md`
- Read `06-import-export-compatibility-rules.md`
- Read `docs/persistence-map.md`
- Read `docs/data-contracts.md`

### If the task changes pages, controls, or navigation
- Read `05-ui-extension-rules.md`
- Read `docs/ui-map.md`

### If the task changes import, export, recovery, or operator portability
- Read `06-import-export-compatibility-rules.md`
- Read `docs/import-export-map.md`

### If the task changes PDFs, templates, fonts, boleto parsing, or barcode extraction
- Read `07-document-flow-change-rules.md`
- Read `docs/pdf-and-document-map.md`

### If the task proposes refactoring or code extraction
- Read `09-modularization-roadmap.md`
- Read `docs/refactor-boundaries.md`

## Acceptance Guidance
Future implementation work is not considered prepared until:
- the task has been classified by module priority
- the feature-intake template has been completed
- the relevant execution rules have been consulted
- the regression scope has been selected before coding
- the relevant skills have been applied alongside the execution docs
