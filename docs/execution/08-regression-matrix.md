# Cobtool Regression Matrix

## Status
- `VERIFIED` This matrix is the practical execution-layer version of `docs/testing-checklist.md`.
- `VERIFIED` Use it to select the minimum regression scope before implementation starts and again before closing the task.

## How To Use This Matrix
1. Find the touched module or modules.
2. Run at least the listed minimum checks.
3. If a task crosses multiple rows, combine the check bundles.
4. If a listed check cannot be run, state that clearly in the final handoff.
5. Use `skills/09-regression-skill.md` together with this matrix when closing implementation work.

## Matrix

| Module | Affected Pages | Storage Dependencies | Import/Export Dependencies | Document Dependencies | Minimum Regression Checks |
| --- | --- | --- | --- | --- | --- |
| App shell and navigation | `home`, menu pages, all child routes | `ch_last_page` | import-export page route access | none | open each top-level destination, verify `VOLTAR`, verify top-menu import/export entry |
| Operator session gate | `operator-unlock`, top-menu switch operator | `cobtool_alfa_operador_v1`, `cobtool_operator_session_v1`, scoped keys | operator import depends on stable suffix logic | operator name affects document selection | unlock, switch operator, reopen previous operator, confirm scoped data isolation |
| Theme and header utilities | topbar | `ch_theme`, `ct_message_counter_v1` | none | barcode helper depends on boleto parser global | toggle theme, reload, use header utilities touched by the task |
| Alfa cobranca workspace | `alfa` | `cobtool_alfa_ufs_v1__<slug>` | none directly | opens contact and letter modal flows | generate touched message types, copy output, validate warnings and operator-linked output |
| Formatting modules | `formatacao`, `formatacao-cobrancas`, `formatacao-texto` | usually none or low | none | none | run touched formatter inputs, verify copy/output formatting, reload if persistence was added |
| Contact record helper | `registro-contato` | `cobtool_registro_contato_v1__<slug>` | none | none | generate history block, reload, confirm persisted helper state |
| Boleto issuance logging | `emissao-boletos` | `cobtool_emissao_boletos_v1__<slug>`, representative lookup store | none | none | create entries for relevant requester types, reload, validate list/search behavior |
| Client registry | `neg-cadastro` | `cobtool_clientes_v1__<slug>` | client JSON export/import if touched | none | create, edit, reload, validate duplicate CNPJ protection, round-trip client import/export if touched |
| Network registry | `redes` | `cobtool_redes_v1__<slug>` plus client lookup | none directly | none | create or edit network, lookup client, reload, validate persistence |
| Negotiations | `neg-cad-negociacoes`, `neg-andamento`, `neg-finalizadas`, `neg-canceladas` | `cobtool_negociacoes_v1__<slug>` | TSV export flows | none directly | create negotiation, move through touched status paths, open modal actions, export touched tables |
| Compensation engine | `compensacoes` and negotiation or transfer handoffs | usually derived from negotiation inputs | none directly | none | run touched compensation mode, verify generated text, verify prefill handoff if touched |
| Transfers | `transferencias`, `tr-andamento`, `tr-finalizadas` | `cobtool_transferencias_v1__<slug>` and client linkage | TSV export flows | transfer modal proof download flow | create transfer, toggle touched status fields, move to finalized if applicable, test touched proof actions |
| PIX proof persistence | PIX table within transfer workflow | `cobtool_pix_state_v1__<slug>`, IndexedDB `cobtool_pix_db_v1` | full backup and ZIP export | proof file handling | save PIX proof, reopen/view, export ZIP, verify backup impact if changed |
| Boleto metadata registry / parser | `registro-boletos` | imported rows are not main persistent storage | metadata JSON export/import | parser plus header barcode helper | parse boleto PDF, inspect extracted fields, export metadata JSON, import metadata JSON, test header barcode helper |
| Document generators | `notificacao-extrajudicial`, `carta-cobranca`, `carta-quitacao`, Alfa document modals | none primary | none primary | templates, fonts, anchors, PDF libs | generate touched PDFs, open outputs, confirm overlay placement, test affected template permutations |
| Unified import/export | `import-export` | all major localStorage keys and PIX IndexedDB | full backup, operator import, restore order | indirectly depends on document and operator labels | export full backup, mutate visible state, import full backup, verify restore, test operator import if touched |
| Static assets and templates | notification template folder, fonts, `operadores/` | indirect | operator payload import may depend on asset availability | direct dependency for generators | verify asset fetches, generator outputs, and any touched operator payload import path |

## Minimum Bundles By Risk Type

### Storage-related changes
- operator unlock and switch
- touched module save and reload
- full backup export and import if durable state changed

### Navigation-related changes
- every touched entry button
- `VOLTAR`
- top-menu actions
- every touched modal open and close path

### Document-related changes
- touched generator outputs
- boleto parsing if parser-adjacent
- header barcode helper if parser globals were touched

### Import/export-related changes
- export path
- import path
- recovery outcome after reload
- operator isolation if operator payload logic changed

## Acceptance Guidance
The regression plan for a task is incomplete unless the relevant rows have been selected before coding starts.
