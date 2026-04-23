# Cobtool PDF and Document Map

## Status
- `VERIFIED` Cobtool contains multiple document flows that depend on static PDF templates, client-side PDF libraries, and local font files.
- `VERIFIED` Document flows are among the most fragile parts of the repository because they rely on fixed filenames, layout heuristics, and text-anchor replacement.

## Shared Document Runtime

### Browser libraries
- `VERIFIED` `pdf.js` is loaded from CDN for PDF reading/text extraction.
- `VERIFIED` `pdf-lib` is loaded from CDN for PDF creation/editing.
- `VERIFIED` `fontkit` is loaded from CDN to enable custom font embedding in `pdf-lib`.

### Local font assets
- `VERIFIED` `calibri-regular.ttf`
- `VERIFIED` `calibri-bold.ttf`

### Font behavior
- `VERIFIED` Document generators try to embed local Calibri fonts first.
- `VERIFIED` If Calibri loading fails, the code falls back to Helvetica / HelveticaBold.

## Template Inventory

### Repository folder
- `VERIFIED` Templates live in `notificação/`.
- `VERIFIED` Code tries both `notificação/` and `notificacao/` URLs when fetching template PDFs.

### Extrajudicial notification bases
- `VERIFIED` `NOTIFICAÇÃO EXTRAJUDICIAL - NDS.pdf`
- `VERIFIED` `NOTIFICAÇÃO EXTRAJUDICIAL - NDS - A VENCER.pdf`
- `VERIFIED` `NOTIFICAÇÃO EXTRAJUDICIAL - PRATI.pdf`
- `VERIFIED` `NOTIFICAÇÃO EXTRAJUDICIAL - PRATI - A VENCER.pdf`
- `VERIFIED` `NOTIFICAÇÃO EXTRAJUDICIAL - BLANKPAGE.pdf`

### Cobrança letter bases
- `VERIFIED` Operator-specific files named like:
  - `NOTIFICAÇÃO DE BOLETOS EM ATRASO - <Operador>.pdf`
  - `NOTIFICAÇÃO DE BOLETOS EM ATRASO - WOPDF - <Operador>.pdf`

### Quitação letter bases
- `VERIFIED` Operator- and sphere-specific files named like:
  - `CARTA DE QUITAÇÃO - NDS - <Operador>.pdf`
  - `CARTA DE QUITAÇÃO - PRATI - <Operador>.pdf`

## 1. Header Boleto Barcode Extraction

### UI entry
- `VERIFIED` Topbar PDF button and hidden file input:
  - `#boletoBarcodeToggle`
  - `#boletoBarcodeFile`
  - `#boletoBarcodeValue`

### Flow
- `VERIFIED` The header helper accepts a boleto PDF.
- `VERIFIED` It calls `window.__cobtoolRegistroBoletosExtractBarcode`.
- `VERIFIED` That extractor is provided by the registro-boletos module.

### Risk
- `VERIFIED` If the registro-boletos module stops exposing the global extractor, the topbar helper breaks.

## 2. Registro-Boletos PDF Reading / Parsing

### Module
- `VERIFIED` `page-registro-boletos`

### Input
- `VERIFIED` One or more local boleto PDFs.

### Extraction targets
- `VERIFIED` CNPJ
- `VERIFIED` nota fiscal
- `VERIFIED` parcela
- `VERIFIED` emissão
- `VERIFIED` vencimento
- `VERIFIED` identificador / nosso número
- `VERIFIED` valor
- `VERIFIED` código de barras
- `VERIFIED` file-derived date/time/operator context

### Parsing strategy
- `VERIFIED` Reads text content from each PDF page with `pdf.js`.
- `VERIFIED` Builds line/group structures from text coordinates.
- `VERIFIED` Uses general extractors plus Itaú-specific field extraction fallback.

### Important heuristics
- `VERIFIED` The parser uses label matching such as:
  - documento
  - data do documento
  - vencimento original
  - processamento
  - nosso número
  - valor do documento
- `VERIFIED` Barcode extraction depends on digit-density and layout matching.
- `VERIFIED` Itaú extraction assumes barcode rows starting with `341`.

### Output contract
- `VERIFIED` Rows are normalized to string-oriented metadata objects for export/copy workflows.

## 3. Extrajudicial Notification PDF Generation

### Module
- `VERIFIED` `page-notificacao-extrajudicial`

### Input data
- razão social
- CNPJ
- esfera switch (`NDS` / `PRATI`)
- títulos text
- optional `a vencer` titles text

### Generation behavior
- `VERIFIED` Loads base template PDFs and blank page template.
- `VERIFIED` Uses `pdf.js` text extraction to locate text anchors in the base PDF.
- `VERIFIED` Rewrites header fields, date line, client info, and title tables.
- `VERIFIED` Can append extra pages for `a vencer` blocks.
- `VERIFIED` Copies/positions a signature block based on text-anchor bounds.

### Anchor dependency examples
- `VERIFIED` The code looks for reference strings such as:
  - `L R PEREIRA JUNIOR LTDA`
  - `CNPJ:`
  - `Atenciosamente`
  - email text on the signature page

### Output
- `VERIFIED` Downloads a generated PDF named with CNPJ and esfera.
- `VERIFIED` Also supports an Outlook email body flow from the same page.

## 4. Cobrança Letter Generation

### Modules
- `VERIFIED` `page-carta-cobranca`
- `VERIFIED` Alfa cobrança modal for letter generation

### Template selection
- `VERIFIED` Template filenames are chosen by operator name.
- `VERIFIED` For Karoline, the code prioritizes the Karoline-specific base.
- `VERIFIED` Fallback is Carlyle’s base if operator-specific fetch fails.
- `VERIFIED` If no upload PDF is provided, the `WOPDF` base prefix is used.

### Input data
- razão social
- CNPJ
- optional date
- optional uploaded PDF

### Generation behavior
- `VERIFIED` Copies the first page of the base template.
- `VERIFIED` Overlays client data and date on the first page.
- `VERIFIED` Appends uploaded PDF pages when provided.
- `VERIFIED` Appends remaining base template pages afterwards.

### Output
- `VERIFIED` Downloads `Carta de Cobranca - <cnpj>.pdf`

## 5. Quitação Letter Generation

### Modules
- `VERIFIED` `page-carta-quitacao`
- `VERIFIED` Alfa quitação modal

### Template selection
- `VERIFIED` Operator name and esfera determine which base filenames are tried first.
- `VERIFIED` Fallback is the Carlyle file for the selected esfera.

### Input data
- razão social
- CNPJ
- esfera (`NDS` / `PRATI`)
- optional date

### Generation behavior
- `VERIFIED` Copies base pages and overlays client/date on the first page.
- `VERIFIED` Does not append an uploaded external PDF in the current flow.

### Output
- `VERIFIED` Downloads `Carta de Quitacao - <cnpj> - <esfera>.pdf`

## 6. Transfer Proof Document Flow

### Module
- `VERIFIED` Transfer modal in `tr-andamento`

### Input
- `VERIFIED` Pasted image or PDF proof file

### Behavior
- `VERIFIED` Keeps pasted proof in memory only for the current modal session.
- `VERIFIED` Can download the pasted proof as:
  - PDF if pasted file is PDF
  - PNG if pasted file is an image
- `VERIFIED` Tries to copy proof/attachment to clipboard for Outlook composition flow.

### Important distinction
- `VERIFIED` This flow is not the same as PIX proof persistence.
- `VERIFIED` Unless the operator uses the PIX proof save workflow, transfer modal proof files are not persisted.

## 7. PIX Proof File Flow

### Module
- `VERIFIED` PIX table associated with transfer operations

### Behavior
- `VERIFIED` Each row can upload and save a proof file.
- `VERIFIED` Saved proofs go to IndexedDB, not localStorage.
- `VERIFIED` Saved proofs can be viewed, resent, and exported as ZIP.

## Verification Hotspots

### File path and naming sensitivity
- `VERIFIED` Template lookup depends on exact filenames and operator naming.
- `VERIFIED` Folder-name accent fallback exists, but filename mismatches still fail generation.

### Coordinate/text-anchor sensitivity
- `VERIFIED` Notification and letter generators depend on text anchors and coordinate deltas.
- `VERIFIED` Replacing template PDFs can break placement even if filenames stay the same.

### CDN dependency sensitivity
- `VERIFIED` PDF flows require CDN-loaded libraries at runtime.
- `INFERRED` Offline usage or blocked CDNs may degrade document functionality unless assets are vendored locally in the future.

## Required Regression Checks After Any Document Change
- Confirm template fetch still succeeds for `notificação/` assets.
- Generate extrajudicial notification for both `NDS` and `PRATI`.
- Generate notification with and without `a vencer` titles.
- Generate cobrança letter with and without uploaded PDF.
- Generate quitação letter for both `NDS` and `PRATI`.
- Parse at least one boleto PDF through registro-boletos and verify extracted fields.
- Use the topbar boleto barcode helper on a boleto PDF.
- Verify Calibri embedding still works or Helvetica fallback is acceptable.

## What Is Too Risky To Change Casually
- Template filenames
- folder path conventions for `notificação/`
- anchor strings searched inside base PDFs
- date/client offset constants
- barcode and field-extraction heuristics
- cross-module globals used by header helpers
