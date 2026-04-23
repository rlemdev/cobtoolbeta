# Cobtool Feature Intake Template

## Status
- `VERIFIED` This is the required intake form for future feature and risky maintenance prompts.
- `VERIFIED` It is designed to force module, storage, compatibility, and regression thinking before code changes begin.

## Use Rule
Complete this template before implementing any non-trivial feature, behavior change, storage change, routing change, import/export change, or document-flow change.

## Copyable Template

```md
# Cobtool Feature Intake

## 0. Operating Inputs
- Relevant skills:
- Execution docs consulted:

## 1. Objective
- Requested outcome:
- Business reason:
- Explicit non-goals:

## 2. Affected Modules
- Primary module:
- Secondary modules:
- Highest module classification from `docs/execution/01-module-priority-matrix.md`:

## 3. Affected UI
- Existing pages or routes touched:
- New page required: yes / no
- DOM ids, `data-page` hooks, modals, or tables affected:

## 4. Storage Impact
- New durable data: yes / no
- Existing storage keys touched:
- New storage keys proposed:
- Operator-scoped: yes / no
- IndexedDB impact: yes / no
- Migration required: yes / no

## 5. Import / Export Impact
- Full backup impact: none / additive / breaking risk
- Operator import impact: none / additive / breaking risk
- Payloads touched:
- Compatibility notes required: yes / no

## 6. Document / PDF Impact
- Document flow touched: none / boleto parsing / notification / cobranca letter / quitacao letter / barcode helper
- Template or font impact:
- Parser or anchor impact:

## 7. GitHub Pages Compatibility Check
- Works with static hosting only: yes / no
- Requires backend, database, or required API: yes / no
- Persistence remains browser-local or file-based: yes / no

## 8. Risk Classification
- Overall risk level: safest-to-extend-first / medium-risk / high-risk / do-not-touch-without-explicit-plan
- Why:
- Stop conditions identified:

## 9. Regression Scope
- Minimum regression bundle:
- Extra manual checks required:
- Checks that cannot be run in this task:

## 10. Docs To Update
- `docs/`
- `docs/execution/`
- `AGENTS.md`
- `public/llms.txt`

## 11. Acceptance Criteria
- Functional acceptance:
- Compatibility acceptance:
- Regression acceptance:

## 12. Uncertainty
- `VERIFIED`:
- `INFERRED`:
- `UNKNOWN`:
```

## Required Fields
- operating inputs
- objective
- affected modules
- affected UI
- storage impact
- import/export impact
- GitHub Pages compatibility check
- risk classification
- regression scope
- acceptance criteria

## Rejection Triggers
Pause and redesign the task if the completed intake reveals any of the following:
- requires a backend or database
- changes a `Do-not-touch-without-explicit-plan` module with no plan
- adds durable data with no backup decision
- changes a document or parser flow with no verification plan
- changes operator scope with no dependency review
