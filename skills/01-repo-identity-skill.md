# Repo Identity Skill

## Status
- `VERIFIED` Cobtool is a browser-only operational worktool, not a marketing site or server-backed app.
- `VERIFIED` The app is monolithic in code shape but multi-module in business behavior.

## Purpose
Anchor every future Codex task in the real identity of Cobtool before any coding starts.

## When To Use It
- Every task in this repository

## Affected Project Areas
- All modules
- `index.html`
- `style.css`
- `script.js`
- `docs/`
- `public/llms.txt`

## Required Checks
1. Confirm the task preserves Cobtool as an operator-facing worktool.
2. Identify the real logical module or modules involved.
3. Identify whether the task touches persistence, import/export, operator scope, routing, or document flows.
4. Read `docs/execution/01-module-priority-matrix.md` to classify the touched modules.
5. Read `docs/project-overview.md`, `docs/module-map.md`, and `docs/risk-zones.md`.

## Failure Risks
- Turning an operational screen into generic marketing UI
- Removing or weakening business logic while trying to "clean up"
- Missing cross-module coupling because the code is monolithic

## Do-Not Rules
- Do not assume Cobtool is a generic CRUD app
- Do not assume common SaaS patterns apply
- Do not introduce architecture decisions that ignore local-first operation

## Implementation Guidance
- Start from the logical modules documented in `docs/module-map.md`
- Preserve current business semantics unless the user explicitly asks for semantic change
- Treat existing behaviors as contracts until the code proves otherwise

## Acceptance Guidance
- The final implementation plan names the touched modules
- The work preserves Cobtool as a client-side operational tool
- Any uncertainty is marked `VERIFIED`, `INFERRED`, or `UNKNOWN`
