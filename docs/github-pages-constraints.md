# GitHub Pages Constraints

## Status
- `VERIFIED` The current repository deploys to GitHub Pages by copying static files directly into `_site/` and publishing them as-is.
- `VERIFIED` These constraints are architectural requirements, not optional preferences.

## Hard Constraints

### 1. No database
- Do not add any dependency on:
  - SQL databases
  - NoSQL databases
  - hosted key/value stores
  - backend-managed persistence layers

### 2. No backend
- Do not introduce required server routes, server actions, or backend-only logic.
- Do not move core business functionality behind an API.

### 3. No server runtime assumptions
- Do not require Node.js, Bun, Python, PHP, or any host-side runtime for end-user operation.
- Build-time or local-dev tooling is not the same as production runtime, but the delivered app must remain usable as static files.

### 4. Browser-side persistence only
- Persistent application state must remain in:
  - `localStorage`
  - `IndexedDB`
  - downloaded/imported files

### 5. Static asset access only
- Templates, fonts, and supporting files must remain fetchable as static assets.
- Do not move required assets behind authenticated or server-generated endpoints.

## Allowed Implementation Patterns
- plain HTML/CSS/JS
- multiple static JS files loaded from `index.html`
- browser APIs:
  - `fetch`
  - `Blob`
  - `FileReader`
  - `Clipboard`
  - `localStorage`
  - `IndexedDB`
- client-side libraries loaded from static files or browser-safe CDNs

## Disallowed Implementation Patterns
- required backend APIs for CRUD
- required authentication providers
- server-side PDF generation only
- server-side import/export processing
- host-side filesystem assumptions beyond browser downloads
- server-render-only frameworks unless the final runtime is still plain static output and all core features remain browser-local

## Impact On Future Features

### New persistent features
- Must store data in browser-side storage or user-managed files.
- Must define export/import behavior if operationally important.

### New document features
- Must run fully in the browser.
- Must use static templates/assets or browser-generated output.

### New integrations
- Are only acceptable if the app still fully functions without a server dependency for its core job.
- If a third-party integration is optional, it must degrade gracefully and not replace the local-first baseline.

## Deployment Reality
- `VERIFIED` `.github/workflows/pages-from-release.yml` does not build application code into a server bundle.
- `VERIFIED` It copies repository contents directly and adds `.nojekyll`.
- `VERIFIED` This means production behavior must remain compatible with direct static file serving.

## Codex Rule
Any proposed change that requires a backend, database, or server-only runtime should be treated as incompatible with Cobtool unless the user explicitly authorizes a deployment-model change. Under the current repository rules, such a proposal should be rejected or redesigned into a static/browser-local alternative.
