# UI Extension Rules

## Status
- `VERIFIED` Cobtool uses a static page-switching model, direct DOM ids, and shared modal patterns.
- `VERIFIED` UI changes can easily break runtime behavior if route keys or DOM ids drift from `script.js`.

## Rule 1: Extend an existing page before adding a new page
Prefer adding controls, panels, filters, helper sections, or modals inside the current workflow before creating a new route.

Add a new page only when:
- the workflow is operationally distinct
- the page needs its own table, form, or modal surface
- adding it to an existing page would overload navigation or confuse operators

## Rule 2: If you add a page, update the whole navigation contract
New pages require all of the following:
- a `<section id="page-...">` in `index.html`
- a logical route entry in `pages`
- a parent mapping in `PAGE_PARENT`
- a `pageIds` entry if the route name and section id differ
- at least one `data-page="..."` entry point
- manual verification of `nav-back`

## Rule 3: Treat DOM ids and route keys as contracts
- do not rename critical ids casually
- do not remove `data-page` hooks without replacement
- do not normalize legacy naming mismatches unless every reference is updated
- preserve the existing alias contract for `negociacoes` and `page-neg-menu` unless reworked deliberately

## Rule 4: Prefer additive controls over structural rewrites
Safer UI extensions include:
- filters
- sort controls
- export buttons
- helper panels
- read-only metrics
- non-destructive action buttons

Higher-risk UI changes include:
- page splits or merges
- table structure rewrites in core modules
- modal lifecycle changes
- topbar behavior changes

## Rule 5: Follow existing modal conventions
If a new modal is added:
- use a unique id namespace
- ensure close on explicit close action
- ensure close on click-outside if the module expects it
- ensure `Escape` behavior is preserved if that pattern exists
- ensure body scroll state is restored on close

## Rule 6: Preserve dynamic rendering assumptions
Many pages render table bodies or panels dynamically. Before changing markup:
- identify the render function in `script.js`
- identify selectors used by event listeners
- confirm row actions still bind after rendering

## Rule 7: Keep UI changes static-hosting safe
- no framework router assumptions
- no server-rendered fragments
- no backend-provided UI state required at runtime
- all assets must remain fetchable as static files

## UI Change Checklist
- affected routes listed
- affected DOM ids listed
- affected `data-page` hooks listed
- page or modal ownership identified
- navigation impact reviewed
- touched tables or dynamic renderers reviewed
- regression scope selected from `docs/execution/08-regression-matrix.md`

## Acceptance Guidance
UI work is only acceptable when:
- navigation still works
- touched controls still bind
- touched modals still open and close safely
- the app shell remains compatible with static HTML plus browser JS only

