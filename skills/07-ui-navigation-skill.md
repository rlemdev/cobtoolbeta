# UI Navigation Skill

## Status
- `VERIFIED` Cobtool uses direct DOM bindings and route maps rather than a framework router.
- `VERIFIED` Page ids, route keys, `data-page` attributes, and parent maps are runtime contracts.

## Purpose
Keep navigation, DOM bindings, and page-level behavior stable while UI changes are made.

## When To Use It
- Editing `index.html`
- Adding or removing pages
- Changing route keys or `data-page` values
- Adding modals, controls, tables, or event-bound ids
- Changing navigation shell behavior

## Affected Project Areas
- `index.html`
- Page sections
- `pages`
- `PAGE_PARENT`
- `pageIds`
- Modal overlays
- Topbar controls

## Required Checks
1. Identify the logical route and the DOM section id.
2. If a new page is added, update HTML plus all needed navigation maps.
3. If an id or `data-page` hook changes, trace every direct query or event binding.
4. Preserve the known route alias contract for `negociacoes` to `page-neg-menu` unless deliberately reworked.
5. Read `docs/execution/05-ui-extension-rules.md`.
6. Read `docs/ui-map.md`.

## Failure Risks
- Buttons that stop navigating
- Broken back behavior
- Sections that never become active
- Event listeners silently failing because ids changed

## Do-Not Rules
- Do not rename critical DOM ids blindly
- Do not remove `data-page` hooks without replacement
- Do not change `pages`, `PAGE_PARENT`, or `pageIds` in isolation

## Implementation Guidance
- Prefer extending existing pages before adding new ones
- If you add a new modal, use a unique id namespace and keep close behavior consistent
- Treat DOM structure as application contract, not cosmetic markup

## Acceptance Guidance
- Route entry buttons still work
- Back navigation still matches the intended parent path
- Touched modals open and close correctly
- Any new page is reachable and recoverable through navigation
