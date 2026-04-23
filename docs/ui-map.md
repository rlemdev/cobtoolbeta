# Cobtool UI Map

## Status
- `VERIFIED` Page names, DOM section ids, and navigation behavior below come from `index.html` and the `pages`, `PAGE_PARENT`, and `pageIds` structures in `script.js`.
- `INFERRED` Some route names are historical aliases and do not match section ids one-to-one.

## Global Shell

### Top-level shell structure
- `VERIFIED` `index.html` renders one static shell with a persistent header and a `<main class="page-shell">`.
- `VERIFIED` Every internal screen is a `<section id="page-...">`.
- `VERIFIED` Only one page is active at a time via the `active` class.

### Persistent topbar
- `VERIFIED` The topbar contains:
  - brand button `#nav-brand`
  - CNPJ converter
  - boleto barcode quick reader
  - message counter
  - back button `#nav-back`
  - top menu with import/export, theme toggle, and operator switch

### Global DOM assumptions
- `VERIFIED` The runtime directly queries many ids with `document.getElementById(...)`.
- `VERIFIED` Renaming ids or removing sections will break page initialization.

## Navigation Model

### Route source of truth
- `VERIFIED` `pages` contains the allowed logical page keys.
- `VERIFIED` `PAGE_PARENT` controls back navigation.
- `VERIFIED` `pageIds` maps route names to actual DOM section ids when names diverge.

### Route alias exception
- `VERIFIED` Route key `negociacoes` maps to DOM section `page-neg-menu`.
- `VERIFIED` Back-navigation also understands `neg-menu` as a parent alias.

### Navigation entry mechanisms
- `VERIFIED` Most entry buttons use `data-page="..."`.
- `VERIFIED` `#nav-back` uses `PAGE_PARENT`.
- `VERIFIED` `#nav-brand` returns to the main menu context.
- `VERIFIED` When operator lock is active, `setPage` redirects to `operator-unlock` and stores a pending destination.

## Page Inventory

| Logical Route | DOM Section | Module | Primary Entry Points | Parent | Status |
| --- | --- | --- | --- | --- | --- |
| `operator-unlock` | `page-operator-unlock` | Operator session gate | forced lock, app startup without valid session, switch operator | none | `VERIFIED` |
| `home` | `page-home` | App shell | initial landing page after unlock | none | `VERIFIED` |
| `menu-cadastro` | `page-menu-cadastro` | Navigation menu | home card | `home` | `VERIFIED` |
| `menu-formatacao` | `page-menu-formatacao` | Navigation menu | Esfera menu card | `menu-esfera` | `VERIFIED` |
| `menu-cartas` | `page-menu-cartas` | Navigation menu | Esfera menu card | `menu-esfera` | `VERIFIED` |
| `menu-esfera` | `page-menu-esfera` | Navigation menu | home card | `home` | `VERIFIED` |
| `registro-boletos` | `page-registro-boletos` | Boleto metadata registry | home card | `home` | `VERIFIED` |
| `registro-contato` | `page-registro-contato` | Contact record helper | Esfera menu card | `menu-esfera` | `VERIFIED` |
| `emissao-boletos` | `page-emissao-boletos` | Boleto issuance logging | Esfera menu card | `menu-esfera` | `VERIFIED` |
| `negociacoes` | `page-neg-menu` | Negotiation menu | Esfera menu card | `menu-esfera` | `VERIFIED` |
| `neg-andamento` | `page-neg-andamento` | Negotiations | negotiation menu | `negociacoes` | `VERIFIED` |
| `neg-finalizadas` | `page-neg-finalizadas` | Negotiations | negotiation menu | `negociacoes` | `VERIFIED` |
| `neg-canceladas` | `page-neg-canceladas` | Negotiations | negotiation menu | `negociacoes` | `VERIFIED` |
| `formatacao` | `page-formatacao` | Title formatting | formatting menu | `menu-formatacao` | `VERIFIED` |
| `formatacao-texto` | `page-formatacao-texto` | Rich text editor | formatting menu | `menu-formatacao` | `VERIFIED` |
| `formatacao-cobrancas` | `page-formatacao-cobrancas` | Cobrança-formatting tools | formatting menu | `menu-formatacao` | `VERIFIED` |
| `neg-cadastro` | `page-neg-cadastro` | Client registry | cadastro menu | `menu-cadastro` | `VERIFIED` |
| `redes` | `page-redes` | Network registry | cadastro menu | `menu-cadastro` | `VERIFIED` |
| `neg-cad-negociacoes` | `page-neg-cad-negociacoes` | Negotiation registration | cadastro menu | `menu-cadastro` | `VERIFIED` |
| `compensacoes` | `page-compensacoes` | Compensation engine | Esfera menu card, negotiation/transfer handoffs | `menu-esfera` | `VERIFIED` |
| `transferencias` | `page-transferencias` | Transfer menu | Esfera menu card | `menu-esfera` | `VERIFIED` |
| `tr-andamento` | `page-tr-andamento` | Transfers | transfer menu | `transferencias` | `VERIFIED` |
| `tr-finalizadas` | `page-tr-finalizadas` | Transfers | transfer menu | `transferencias` | `VERIFIED` |
| `notificacao-extrajudicial` | `page-notificacao-extrajudicial` | Extrajudicial notification generator | cartas menu | `menu-cartas` | `VERIFIED` |
| `carta-cobranca` | `page-carta-cobranca` | Cobrança letter generator | cartas menu | `menu-cartas` | `VERIFIED` |
| `carta-quitacao` | `page-carta-quitacao` | Quitação letter generator | cartas menu | `menu-cartas` | `VERIFIED` |
| `alfa` | `page-alfa` | Alfa cobrança workspace | home card | `home` | `VERIFIED` |
| `import-export` | `page-import-export` | Unified import/export | top menu action | `home` | `VERIFIED` |

## Page-to-Module Notes

### Home
- `VERIFIED` Main launchpad for:
  - Cobrança
  - Esfera - Operação
  - Cadastro
  - Registro de Boletos

### Cadastro menu cluster
- `VERIFIED` `page-menu-cadastro` routes to:
  - client registry
  - network registry
  - negotiation registration

### Esfera menu cluster
- `VERIFIED` `page-menu-esfera` routes to:
  - negotiations
  - compensations
  - transfers
  - cartas menu
  - formatação menu

### Negotiations cluster
- `VERIFIED` There is a dedicated menu page plus three lifecycle list pages.
- `VERIFIED` Registration is not inside the lifecycle menu; it lives under the cadastro menu.

### Transfers cluster
- `VERIFIED` There is a transfer menu page and two list pages.
- `VERIFIED` Transfer entry and PIX proof controls live inside `tr-andamento`.

### Document cluster
- `VERIFIED` The cartas menu routes to extrajudicial notification, cobrança letter, and quitação letter.
- `VERIFIED` The Alfa page also opens cobrança and quitação modal flows through additional buttons.

## Important DOM Relationships

### Navigation relationships
- `VERIFIED` `data-page` buttons are the primary declarative navigation link.
- `VERIFIED` Missing `data-page` values or mismatched route names will break navigation.

### Modals and overlays
- `VERIFIED` Multiple modules create modal overlays with shared modal styling:
  - negotiation modal
  - network compensation modal
  - notification email modal
  - Alfa registro modal
  - Alfa cobrança letter modal
  - Alfa quitação modal
  - transfer modal
  - transfer KPI modal

### Shared table/list rendering patterns
- `VERIFIED` Negotiation and transfer pages render tbody contents dynamically.
- `VERIFIED` Client, network, boleto issuance, and boleto metadata pages also depend on generated rows rather than static HTML.

### Header tool dependency
- `VERIFIED` The boleto barcode reader in the header depends on the registro-boletos parser exposing a global extractor function.

## UI Coupling Rules
- `VERIFIED` Adding a new page requires changes in HTML and `script.js` navigation maps.
- `VERIFIED` Adding a new action inside an existing page usually also requires new ids/classes for direct event binding.
- `VERIFIED` DOM ids should be treated as part of the application contract, not as cosmetic markup.

## Known UI Naming Mismatches
- `VERIFIED` `negociacoes` route -> `page-neg-menu`
- `INFERRED` This mismatch is legacy naming retained for compatibility; do not normalize it casually without updating navigation maps and testing all parent routes.
