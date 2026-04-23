# Cobtool Skill Index

## Status
- `VERIFIED` This folder is the permanent repo-specific skill layer for future Codex work in Cobtool.
- `VERIFIED` These skills are guidance files, not runtime code.

## Purpose
Provide a stable operating sequence so future changes start from the real repository constraints instead of re-deriving them from scratch.

## When To Use It
- Before any implementation, review, refactor, or debugging task in this repo
- When scoping a change that might affect storage, routing, operator behavior, import/export, or document flows

## Affected Project Areas
- Entire repository

## Required Checks
1. Read `AGENTS.md` first.
2. Read `skills/01-repo-identity-skill.md`.
3. Read `skills/02-github-pages-skill.md`.
4. Read `docs/execution/00-execution-index.md`, `docs/execution/03-safe-implementation-workflow.md`, and `docs/execution/11-codex-default-operating-sequence.md`.
5. Read the matching specialized skills for the task.
6. Read the canonical detail docs in `docs/`.

## Layer Roles
- `skills/` choose which topical guidance applies
- `docs/execution/` decides the action order and readiness checks
- `docs/` provides the detailed project facts and contracts

## Skill Routing
- Always use: `01-repo-identity-skill.md`
- Always use: `02-github-pages-skill.md`
- Use `03-persistence-skill.md` for `localStorage`, `IndexedDB`, keys, schemas, or recovery behavior
- Use `04-import-export-skill.md` for backup, restore, payloads, or operator data portability
- Use `05-pdf-and-document-skill.md` for boleto parsing, barcode extraction, PDF generation, templates, or fonts
- Use `06-operator-session-skill.md` for unlock flow, operator identity, scoped storage, or session timeout logic
- Use `07-ui-navigation-skill.md` for routes, sections, `data-page`, modals, DOM ids, or navigation shell work
- Use `08-feature-implementation-skill.md` for new features or additive business behavior
- Use `09-regression-skill.md` before closing work
- Use `10-modularization-skill.md` for refactor or extraction work

## Failure Risks
- Skipping the skill layer usually means missing cross-cutting contracts in `script.js`
- The biggest failure modes are broken operator separation, lost persistence, damaged backup compatibility, dead navigation, and broken document flows

## Do-Not Rules
- Do not treat these skills as optional if the touched area matches them
- Do not rely on only one doc when the change crosses multiple modules

## Implementation Guidance
- Use the skills as the topical routing layer, `docs/execution/` as the action layer, and `docs/` as the detailed source
- If two or more skills apply, follow the stricter one first

## Acceptance Guidance
- The task plan references the relevant skills
- The task plan references the relevant execution docs
- The change scope accounts for storage, routing, operator scope, import/export, and document impact when applicable
