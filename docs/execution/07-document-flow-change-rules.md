# Document Flow Change Rules

## Status
- `VERIFIED` Document and parser flows are among the most fragile parts of Cobtool.
- `VERIFIED` These rules apply to boleto parsing, barcode extraction, notification PDFs, cobranca letters, quitacao letters, templates, and font dependencies.

## Covered Flows
- boleto metadata PDF reading
- header barcode quick-reader
- extrajudicial notification generation
- cobranca letter generation
- quitacao letter generation
- font loading and fallback
- template fetch and path resolution

## Rule 1: Keep document flows browser-side
- no backend PDF generation
- no server-side parsing dependency
- no required API for template or document assembly
- all document features must remain static-hosting compatible

## Rule 2: Treat filenames, paths, and anchors as contracts
Do not casually change:
- template filenames
- the notification template folder path
- anchor strings used inside template PDFs
- layout offsets without visual verification
- Calibri asset paths

The code already tolerates accented and non-accented template folder URLs. Preserve that behavior unless a replacement is verified.

## Rule 3: Preserve parser and helper bridges
Do not break:
- boleto field extraction heuristics
- Itaú-specific fallback behavior
- barcode extraction behavior
- `window.__cobtoolRegistroBoletosExtractBarcode`

The topbar barcode helper depends on the parser exposing that global bridge.

## Rule 4: Template replacement is not a cosmetic change
If a base PDF is replaced, assume anchor coordinates and text matching may break even when the filename stays the same.

Required verification after template replacement:
- fetch succeeds
- overlays land in the correct place
- appended pages are still assembled in the right order
- output naming still matches the workflow

## Rule 5: Font behavior must degrade safely
- preserve local Calibri embedding where available
- preserve fallback to standard fonts if Calibri cannot load
- do not require authenticated or server-generated fonts

## Rule 6: Any document change requires explicit regression planning
Minimum regression set after touched document code:
- generate extrajudicial notification for `NDS`
- generate extrajudicial notification for `PRATI`
- generate notification with `a vencer` content if touched
- generate cobranca letter with and without uploaded PDF
- generate quitacao letter for both supported spheres
- parse at least one boleto PDF
- run the header barcode helper

## Rule 7: Document changes must stay isolated from unrelated refactor
Do not combine any of the following in one casual task:
- document flow changes
- parser changes
- template replacements
- storage migrations
- navigation rewrites

These changes are hard enough to verify on their own.

## Too Risky To Change Casually
- template filenames
- notification template folder conventions
- anchor lookup strings
- parser regex or grouping strategy without sample PDFs
- barcode heuristics
- cross-module globals used by document helpers
- operator-specific template selection rules

## Acceptance Guidance
Document-flow work is only acceptable when:
- the feature still runs fully in the browser
- static assets are still fetchable
- representative outputs are verified or verification gaps are stated
- parser and barcode helper compatibility are preserved

