# PDF And Document Skill

## Status
- `VERIFIED` Cobtool has fragile browser-side PDF and document flows tied to static templates, fonts, and parsing heuristics.
- `VERIFIED` Boleto parsing and barcode extraction are high-risk areas.

## Purpose
Protect document generation, boleto parsing, and template-dependent flows from casual breakage.

## When To Use It
- Touching PDF libraries
- Touching template paths or filenames
- Touching Calibri font loading or fallback behavior
- Touching boleto parsing, barcode extraction, or metadata normalization
- Touching notification, cobranca-letter, or quitacao-letter generation

## Affected Project Areas
- Notification templates
- Letter generators
- Boleto metadata registry
- Header barcode helper
- Font assets

## Required Checks
1. Identify the exact flow being changed: parsing, generation, template lookup, or export.
2. Preserve template filenames and path assumptions unless there is explicit migration work.
3. Preserve anchor-string or coordinate logic unless verified against real output.
4. Preserve the barcode extractor global bridge if the header helper still uses it.
5. Read `docs/execution/07-document-flow-change-rules.md`.
6. Read `docs/pdf-and-document-map.md`.

## Failure Risks
- Broken generated PDFs
- Misaligned overlays
- Missing template fetches
- Empty or wrong boleto metadata extraction
- Broken topbar barcode utility

## Do-Not Rules
- Do not casually rename templates
- Do not move required assets behind authenticated or server-generated paths
- Do not replace parsing heuristics without testing against real boleto PDFs
- Do not remove cross-module globals used by document helpers without replacement

## Implementation Guidance
- Keep the flows browser-side and static-asset based
- Treat font embedding and template lookup as compatibility-sensitive
- After changes, generate representative outputs and parse representative boleto files
- If document behavior changes, update `docs/pdf-and-document-map.md` and `docs/testing-checklist.md` if needed

## Acceptance Guidance
- Notification PDFs still generate
- Cobranca letters still generate with and without uploaded PDFs
- Quitacao letters still generate for both supported spheres
- Boleto parsing and barcode extraction still produce usable results
