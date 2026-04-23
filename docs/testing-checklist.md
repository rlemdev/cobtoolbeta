# Cobtool Manual Testing Checklist

## Status
- `VERIFIED` This checklist is based on actual modules, routes, storage behavior, and document flows in the current repository.
- `VERIFIED` Because the app is static and monolithic, manual regression remains the primary safety net.

## Test Environment Assumptions
- Open the app as a static site or local static file host equivalent to GitHub Pages behavior.
- Use a browser with `localStorage`, `IndexedDB`, clipboard, and download support enabled.
- Use at least one test operator identity.

## 1. Cold Start / Shell
- Open the app from the static entrypoint.
- Confirm the unlock screen appears when there is no valid operator session.
- Confirm the topbar renders correctly.
- Confirm the theme toggle still works.
- Confirm the back button appears only away from top-level pages.

## 2. Operator Unlock / Session
- Unlock with one operator.
- Confirm the app opens normally after unlock.
- Use the top menu to switch operator and confirm the unlock screen returns.
- Re-open the previous operator and confirm their scoped data still exists.
- Confirm Alfa operator select is locked to the active operator.
- If session behavior was touched, verify the app re-locks when the session is invalid/expired.

## 3. Navigation
- Navigate from `home` to each top-level destination:
  - Cobrança
  - Esfera - Operação
  - Cadastro
  - Registro de Boletos
- From Esfera menu, open:
  - Negociações
  - Compensações
  - Transferências
  - Cartas
  - Formatação
- Use `VOLTAR` from child pages and confirm `PAGE_PARENT` behavior is correct.
- Use the top-menu import/export action and confirm the page opens.

## 4. Client Registry
- Create a client with valid razão social, code, and CNPJ.
- Edit the client.
- Confirm duplicate CNPJ protection still works.
- Reload the page and confirm the client persists for the current operator.

## 5. Network Registry
- Find an existing client by code or CNPJ.
- Add the client to a new network.
- Save the network.
- Reload and confirm the network persists for the current operator.

## 6. Negotiations
- Create a client negotiation.
- Create a network negotiation.
- Confirm automatic calculations, parcel dates, and text generation still work.
- Open the negotiation modal and toggle parcel paid/unpaid behavior.
- Move a negotiation through:
  - andamento
  - finalizada
  - cancelada
- Export tables from all three negotiation list pages.

## 7. Compensation
- Run compensation in:
  - créditos mode
  - pós-vendas mode
  - negociação mode
- Confirm output text is generated and copy works.
- From a negotiation modal, jump into compensation and confirm the form is prefilled correctly.

## 8. Transfers and PIX
- Register a transfer with identified client.
- Register a transfer using no-identify mode if touched by changes.
- Open the transfer modal and toggle all status switches.
- Confirm finalized transfers move to the finalized table.
- Paste an image proof in the transfer modal and download it.
- Paste a PDF proof in the transfer modal and download it.
- Save a PIX proof file in the PIX table.
- View/resend a saved PIX proof.
- Export PIX proofs as ZIP.

## 9. Cobrança / Alfa Workspace
- Generate each main message type:
  - cold contact
  - previsão Serasa
  - risco bloqueio
  - regularização urgente
- Copy the generated message.
- Confirm the registro modal opens with matching contact-history text.
- Toggle UF and warning-related inputs and confirm warnings update.

## 10. PDF / Document Flows
- Generate extrajudicial notification for `NDS`.
- Generate extrajudicial notification for `PRATI`.
- Generate notification with `a vencer` content when relevant.
- Open the notification email modal and validate Outlook draft launch.
- Generate cobrança letter without uploaded PDF.
- Generate cobrança letter with uploaded PDF.
- Generate quitação letter for `NDS`.
- Generate quitação letter for `PRATI`.
- Confirm downloaded PDFs open and contain overlaid client/date data in the correct place.

## 11. Registro-Boletos / Barcode Parsing
- Upload at least one boleto PDF in registro-boletos.
- Confirm rows are extracted with populated metadata fields.
- Export boleto metadata JSON.
- Import boleto metadata JSON into the import panel and confirm rows render.
- Use the header boleto barcode helper on a boleto PDF and confirm a barcode value appears.

## 12. Emissão de Boletos / Registro de Contato
- Create a boleto issuance log entry for:
  - tele vendedor(a)
  - representante externo
  - cliente
- Confirm entries persist after reload.
- Generate a registro de contato history block and confirm it persists after reload.

## 13. Import / Export
- Export a full backup.
- Make visible changes in several modules.
- Import the full backup and confirm the prior state is restored.
- Confirm theme and current-page restoration behave acceptably after import.
- Import a valid operator JS payload and confirm only that operator’s scoped keys are affected.
- If client import/export was changed, round-trip a client JSON file.

## 14. Static Hosting / GitHub Pages Assumptions
- Confirm the app still works without any backend running.
- Confirm all required assets are fetched via static relative paths or browser-side CDN scripts.
- Confirm no feature now requires server credentials, API tokens, or database access to function.
- Confirm downloads still work in-browser with no host-side file write assumptions.

## 15. Minimum Regression Set For High-Risk Changes

### If storage was changed
- operator unlock/switch
- client save/load
- negotiation save/load
- transfer save/load
- full backup export/import

### If document code was changed
- extrajudicial notification generation
- cobrança letter generation
- quitação letter generation
- boleto parsing
- header barcode extraction

### If navigation or DOM was changed
- route entry buttons
- back behavior
- top-menu actions
- all modals used by touched modules
