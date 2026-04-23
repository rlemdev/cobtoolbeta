# Cobtool Modularization Roadmap

## Status
- `VERIFIED` Cobtool is currently a static monolith centered on `script.js`.
- `VERIFIED` Modularization is allowed only if the GitHub Pages deployment model and browser-local behavior remain unchanged.

## Goal
Reduce risk gradually by extracting safe client-side seams first. This roadmap is intentionally static-hosting compatible and does not propose any backend migration.

## Non-Negotiable Constraints
- no backend migration
- no database migration
- no required bundler for production use
- no runtime Node dependency on the host
- `index.html` must remain a valid static entrypoint

## Recommended Packaging Model
When extraction begins later, prefer:
- additional static JS files
- browser-loaded scripts referenced from `index.html`
- explicit dependency order
- preserved global bridges where cross-module contracts still need them

Avoid introducing a packaging model that only works after a build step unless the final shipped output is still plain static files and existing runtime behavior remains intact.

## Phase 0: Hold the contracts steady
Prerequisites before meaningful modularization:
- governance docs exist
- execution pack exists
- repeated successful feature tasks have validated the contracts
- regression expectations are stable

This phase is now `VERIFIED` as documented, but runtime extraction should still be cautious.

## Phase 1: Safe extraction targets
These are the first client-side extractions to consider later:
- pure string and date format helpers
- constant lists and key names
- clipboard and download helpers
- non-persistent formatter helpers
- small render helpers that do not own business state

Why these are safe:
- low storage sensitivity
- low route sensitivity
- minimal operator coupling
- easy regression surface

## Phase 2: Medium-risk supporting extractions
Consider only after Phase 1 works cleanly:
- storage helper wrappers with no semantic change
- client registry rendering helpers
- network registry rendering helpers
- boleto issuance logging helpers
- contact-record helper rendering and save helpers
- shared PDF utility wrappers that do not alter generation algorithms

Safety conditions:
- no key rename
- no payload change
- no route-map change
- no template or parser logic change

## Phase 3: Controlled extractions with explicit plan
Plan-required areas:
- navigation shell
- operator session gate
- import/export orchestration
- negotiation lifecycle logic
- transfer lifecycle logic
- PIX proof persistence
- boleto parser
- notification and letter generators

Do not start here until:
- the exact boundary is mapped
- the regression bundle is written
- global bridge requirements are identified
- the task is isolated from product feature work

## Dangerous Monolith Areas To Defer
- `pages`, `PAGE_PARENT`, `pageIds`
- operator unlock and scoped storage resolution
- full backup restore order
- PIX dual persistence
- boleto parser heuristics
- PDF template assembly and anchor logic
- negotiation and transfer data-model semantics

## Refactor Order Later
1. extract pure helpers
2. extract support utilities
3. extract isolated page helpers
4. extract supporting persistence helpers
5. revisit high-risk business flows only after repeated success on earlier phases

## Acceptance Guidance
Any future modularization step is acceptable only when:
- it preserves static hosting
- it preserves browser-local persistence
- it does not change contracts accidentally
- it updates docs when boundaries actually change

