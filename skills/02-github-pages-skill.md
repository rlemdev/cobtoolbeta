# GitHub Pages Skill

## Status
- `VERIFIED` Cobtool must remain deployable as static files on GitHub Pages.
- `VERIFIED` No backend or database is allowed under the current repository rules.

## Purpose
Protect the static-hosting architecture and reject or redesign changes that would break GitHub Pages compatibility.

## When To Use It
- Any time a task adds dependencies, persistence, integrations, background processing, or runtime assumptions

## Affected Project Areas
- Deployment model
- Runtime dependencies
- Asset loading
- Persistence architecture
- Any new feature proposal

## Required Checks
1. Confirm the feature works with plain static hosting.
2. Confirm no backend, database, or required API is introduced.
3. Confirm no host-side runtime is required in production.
4. Confirm all required assets remain fetchable as static files or browser-safe CDN assets.
5. Read `docs/execution/03-safe-implementation-workflow.md` so GitHub Pages compatibility is checked before implementation, not after.
6. Read `docs/github-pages-constraints.md`.

## Failure Risks
- A feature that works locally but fails on GitHub Pages
- Hidden dependence on server credentials, APIs, or background processes
- Breaking disaster recovery by moving state out of the browser

## Do-Not Rules
- Do not add a required backend
- Do not add a database
- Do not add server-only authentication
- Do not move core PDF or import/export work server-side
- Do not assume Node exists on the production host

## Implementation Guidance
- Prefer plain HTML, CSS, and browser-side JS
- Browser APIs such as `fetch`, `Blob`, `FileReader`, `localStorage`, `IndexedDB`, and `Clipboard` are compatible patterns
- Optional third-party integrations must degrade gracefully and must not replace the local-first baseline

## Acceptance Guidance
- The feature still runs as static files
- Core workflows still function with no backend and no database
- Any new dependency is browser-safe and static-hosting compatible
