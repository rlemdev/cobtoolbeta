# Cobtool Module Priority Matrix

## Status
- `VERIFIED` This matrix is derived from `docs/module-map.md`, `docs/risk-zones.md`, `docs/feature-implementation-rules.md`, and `docs/testing-checklist.md`.
- `VERIFIED` It is the default risk-classification tool for future Codex implementation work.

## Rating Model
- Business criticality: how directly the module affects day-to-day operations
- Coupling risk: how many other modules or contracts it can break
- Implementation risk: how easy it is to introduce a regression while changing it
- Test complexity: how broad or difficult the regression scope is
- Safe extensibility: how safely additive work can be attached without rewiring contracts

Ratings use `Low`, `Medium`, `High`, and `Critical`.

## Classification Meaning
- `Safest to extend first`: additive work is usually local, low-coupling, and easy to verify
- `Medium-risk`: additive work is possible, but storage, routing, or workflow checks are required
- `High-risk`: likely cross-module, contract-sensitive, or expensive to test
- `Do-not-touch-without-explicit-plan`: change can break recovery, operator separation, navigation shell, or document output

## Matrix

| Module | Business Criticality | Coupling Risk | Implementation Risk | Test Complexity | Safe Extensibility | Classification | Notes |
| --- | --- | --- | --- | --- | --- | --- | --- |
| App shell and navigation | High | Critical | High | High | Low | Do-not-touch-without-explicit-plan | Any route or DOM id mistake can break the whole app shell |
| Operator session gate | Critical | Critical | Critical | High | Low | Do-not-touch-without-explicit-plan | Controls operator identity, scoped storage, and unlock flow |
| Theme and header utilities | Low | Medium | Low | Low | Medium | Medium-risk | Barcode helper is coupled to boleto parser globals |
| Alfa cobranca workspace | High | High | Medium | High | Medium | Medium-risk | Core operator workflow with document and operator dependencies |
| Extrajudicial notification generator | High | High | High | High | Low | Do-not-touch-without-explicit-plan | Template, anchor, and layout sensitive |
| Cobranca letter generator | High | High | High | High | Low | Do-not-touch-without-explicit-plan | Operator-specific template selection and overlay flow |
| Quitacao letter generator | High | High | High | High | Low | Do-not-touch-without-explicit-plan | Operator and sphere template matrix is fragile |
| Formatting: titulos | Medium | Low | Low | Low | High | Safest to extend first | Pure formatter behavior with low persistence impact |
| Formatting: cobranca text helpers | Medium | Low | Low | Low | High | Safest to extend first | Good lane for additive outputs and copy helpers |
| Rich text editor | Low | Low | Low | Low | High | Safest to extend first | Mostly isolated UI behavior |
| Compensation engine | High | High | High | High | Low | High-risk | Coupled to negotiation semantics and business calculations |
| Client registry | High | Medium | Medium | Medium | Medium | Medium-risk | Safe for additive UX, risky for contract changes |
| Network registry | Medium | Medium | Medium | Medium | Medium | Medium-risk | Depends on client shape and negotiation use |
| Negotiations | Critical | Critical | High | High | Low | High-risk | Data model changes cascade into compensation and transfers |
| Transfers | Critical | High | High | High | Low | High-risk | Coupled to clients, negotiations, PIX, and modal flows |
| PIX proof persistence | High | High | High | High | Low | Do-not-touch-without-explicit-plan | Dual persistence in localStorage and IndexedDB |
| Boleto issuance logging | Medium | Low | Low | Low | High | Safest to extend first | Operationally useful and relatively isolated |
| Boleto metadata registry / parser | High | High | High | High | Low | Do-not-touch-without-explicit-plan | Heuristic parser plus cross-module barcode exposure |
| Contact record helper | Medium | Low | Low | Low | High | Safest to extend first | Lightweight persistence and limited coupling |
| Unified import/export | Critical | Critical | Critical | High | Low | Do-not-touch-without-explicit-plan | Recovery-critical and touches almost every persisted module |
| Static assets and templates | High | High | Medium | High | Low | High-risk | Filenames and fetch paths are compatibility contracts |

## Recommended Extension Order

### First lane
- Formatting: titulos
- Formatting: cobranca text helpers
- Rich text editor
- Contact record helper
- Boleto issuance logging

### Second lane
- Client registry
- Network registry
- Alfa cobranca workspace
- Theme and header utilities excluding parser contract changes

### Defer until a stronger plan exists
- operator session gate
- app shell and navigation
- unified import/export
- PIX proof persistence
- boleto metadata parser
- document generators
- negotiation data model changes
- transfer status or proof model changes

## Use Rule
Every future feature request must identify its highest-touched classification before implementation starts. If any touched module is `Do-not-touch-without-explicit-plan`, coding should not begin until the plan, compatibility surface, and regression scope are written down.

