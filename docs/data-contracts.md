# Cobtool Data Contracts

## Status
- `VERIFIED` Contracts below are reverse-engineered from actual creation, sanitization, load/save, export, and import code.
- `INFERRED` Some optional fields may appear only after lifecycle transitions or legacy imports.
- `UNKNOWN` Any external data producer not present in this repository snapshot may add fields not documented here.

## Contract Conventions
- Currency values are mixed:
  - `VERIFIED` some modules store decimal numbers in BRL units
  - `VERIFIED` transfers store integer cents (`valorCentavos`)
  - `VERIFIED` boleto metadata exports store preformatted strings
- Dates are mixed:
  - `VERIFIED` ISO timestamps for persistence/audit fields
  - `VERIFIED` `DD/MM/YYYY` strings for many business fields

## 1. Client Entity

### Stored in
- `cobtool_clientes_v1__<slug>`

### Shape
```js
{
  id: string,
  razaoSocial: string,
  codigoCliente: string,   // normalized to 6 digits
  cnpj: string,            // normalized to 14 digits
  responsavel: string,
  contato: string,
  criadoEm?: string,       // ISO timestamp
  atualizadoEm?: string    // ISO timestamp
}
```

### Notes
- `VERIFIED` `codigoCliente` is normalized with left-padding to 6 digits.
- `VERIFIED` `cnpj` is normalized to 14 digits.
- `VERIFIED` Duplicate CNPJ is blocked at save time.

## 2. Network Entity

### Stored in
- `cobtool_redes_v1__<slug>`

### Shape
```js
{
  id: string,
  nome: string,
  clientes: NetworkMember[],
  criadoEm: string,        // ISO timestamp
  atualizadoEm: string     // ISO timestamp
}
```

### NetworkMember
```js
{
  clienteId: string,
  codigoCliente: string,
  cnpj: string,
  razaoSocial: string,
  responsavel: string,
  contato: string
}
```

### Notes
- `VERIFIED` Network members are snapshots of client data, not live references only.
- `VERIFIED` `clienteId` is required for persisted members after sanitization.

## 3. Negotiation Entity

### Stored in
- `cobtool_negociacoes_v1__<slug>`

### Shape
```js
{
  id: string,
  negociacaoTipo: "cliente" | "rede",
  clienteId: string | null,
  clienteSnapshot: {
    id?: string,
    razaoSocial: string,
    cnpj: string,
    codigoCliente: string,
    responsavel: string,
    contato: string
  },
  redeId: string | null,
  redeSnapshot: {
    id: string,
    nome: string,
    clientes: NetworkMember[]
  } | null,
  redeClientes: RedeNegotiationClient[],
  situacao: "andamento" | "finalizada" | "cancelada",
  valorDevido: number,
  multa: number,
  juros: number,
  encargos: number,
  numeroParcelas: number,
  entradaAtiva: boolean,
  valorEntrada: number,
  periodicidade: string,
  datasPrevistas: string[],    // BR dates
  dataEntrada: string,         // BR date or empty
  taxaBoletosAtiva: boolean,
  qtdBoletos: number,
  taxaAdmBoletoTotal: number,
  numeroNegociacao: string,
  textoNegociacao: string,
  valorParcela: number,
  valorTotalNegociado: number,
  preexistente: boolean,
  parcelasPagas: {
    paga?: boolean,
    pagoEm?: string            // ISO date/time string in practice
  }[],
  criadoEm: string,            // ISO timestamp
  atualizadoEm: string,        // ISO timestamp
  finalizadaEm?: string,       // ISO timestamp
  canceladaEm?: string         // ISO timestamp
}
```

### RedeNegotiationClient
```js
{
  clienteId: string,
  codigoCliente: string,
  cnpj: string,
  razaoSocial: string,
  responsavel: string,
  contato: string,
  valorDevido: number,
  multa: number,
  juros: number,
  taxaAdmBoleto: number,
  subtotal: number
}
```

### Notes
- `VERIFIED` `negociacaoTipo` drives whether single-client or network behavior is used.
- `VERIFIED` `parcelasPagas` is normalized to fit the current installment structure.
- `INFERRED` Manual/no-identify negotiations may store `clienteSnapshot.id` with a synthetic `manual:<codigo>:<cnpj>` value while `clienteId` stays `null`.

## 4. Transfer Entity

### Stored in
- `cobtool_transferencias_v1__<slug>`

### Shape
```js
{
  id: string,
  codigo: string,             // normalized client code
  cnpj: string,               // 14-digit string
  valorCentavos: number,      // integer cents
  razaoSocial: string,
  responsavel: string,
  contato: string,
  status: {
    email: boolean,
    creditado: boolean,
    compensacao: boolean,
    finalizado: boolean
  },
  createdAt: string           // ISO timestamp
}
```

### Notes
- `VERIFIED` Stage and situation are derived from `status`.
- `VERIFIED` Finalized transfers remain in the same store and are filtered into different tables by status.

## 5. PIX Row State Contract

### Stored in
- `cobtool_pix_state_v1__<slug>`

### Shape
```js
[
  {
    codigo: string,
    cnpj: string,
    valor: string,            // rendered/display value, not cents
    email: boolean,
    creditado: boolean,
    compensacao: boolean,
    finalizado: boolean
  }
]
```

### Notes
- `VERIFIED` This state tracks row checkboxes and visible PIX table values.
- `VERIFIED` It does not contain the actual proof file binary.

## 6. PIX Proof File Contract

### Stored in
- IndexedDB `cobtool_pix_db_v1` / `pix_store`

### Shape
```js
{
  id: string,
  codigo: string,
  cnpj: string,
  valor: string,
  filename: string,
  storedAt: string,           // ISO timestamp
  blob: Blob
}
```

### Notes
- `VERIFIED` File ids are generated from code, timestamp, and file size.
- `VERIFIED` Exported backup serializes `blob` into base64 plus `type`.

## 7. Boleto Issuance Request Contract

### Stored in
- `cobtool_emissao_boletos_v1__<slug>`

### Shape
```js
{
  id: string,
  tipo: "tv" | "rep" | "cli",
  nome: string,
  idLogin: string,
  email: string,
  telefone: string,
  estado: string,
  quantidade: number,
  meta: number,
  quais: string,
  createdAt: string           // ISO timestamp
}
```

### Notes
- `VERIFIED` `idLogin`, `email`, `estado`, and `telefone` are populated according to requester type.
- `INFERRED` `meta` appears to be an integer operational target/reference field, but business semantics are not explicitly documented in code.

## 8. Representative Lookup Contract

### Stored in
- `cobtool_representantes_v1__<slug>`

### Consumed shape
```js
{
  id: string,
  tipo: "tv" | "rep",
  nome: string,
  idLogin?: string,
  email?: string,
  estado?: string,
  telefone?: string
}
```

### Notes
- `INFERRED` This contract is consumed by `emissao-boletos`.
- `UNKNOWN` The source writer for this store is not present in the inspected repository.

## 9. Registro de Contato State Contract

### Stored in
- `cobtool_registro_contato_v1__<slug>`

### Shape
```js
{
  ultimos: string,
  historico: string
}
```

## 10. Boleto Metadata Row Contract

### Used by
- `registro-boletos`
- boleto metadata JSON export/import

### Shape
```js
{
  cnpj: string,
  notaFiscal: string,
  parcela: string,
  emissao: string,
  vencto: string,
  identificador: string,
  valor: string,
  codigoBarras: string,
  nome: string,              // source filename
  data: string,              // BR date
  hora: string,              // time string
  operador: string
}
```

### Notes
- `VERIFIED` Empty/missing fields are normalized to `"-"` during render/export/import.
- `VERIFIED` Row values are strings because they are meant for tabular export, copy, and email workflows.

## 11. Boleto Metadata Export Payload

### Payload type
- `cobtool_registro_boletos_metadados_v1`

### Shape
```js
{
  payloadType: "cobtool_registro_boletos_metadados_v1",
  exportedAt: string,        // ISO timestamp
  exportDate: string,        // BR date
  operator: string,
  rows: BoletoMetadataRow[]
}
```

### Notes
- `VERIFIED` Import accepts either this payload or a raw array of rows.
- `VERIFIED` Import is used for display/copy workflow, not persisted app storage.

## 12. Full Backup Payload

### Payload type
- `cobtool_backup_v2`

### Shape
```js
{
  payloadType: "cobtool_backup_v2",
  exportedAt: string,
  clientes: Client[],
  redes: Network[],
  negociacoes: Negotiation[],
  transferencias: Transfer[],
  localStorage: {
    [key: string]: string
  },
  uiSnapshot: {
    __currentPage?: string,
    __cMode?: string,
    [key: string]: string | boolean
  },
  pixFiles: {
    id: string,
    codigo: string,
    cnpj: string,
    valor: string,
    filename: string,
    storedAt: string,
    type: string,
    data: string             // base64
  }[]
}
```

### Notes
- `VERIFIED` `localStorage` is exported as raw string values.
- `VERIFIED` `pixFiles` is the binary-safe representation of PIX proofs.
- `VERIFIED` Import uses both `localStorage` and the normalized entity arrays.

## 13. Legacy Backup Import Shape

### Legacy/compatibility contract
```js
{
  clientes?: Client[],
  redes?: Network[],
  negociacoes?: Negotiation[],
  transferencias?: Transfer[],
  pix?: {
    files?: {
      codigo: string,
      cnpj: string,
      valor: string,
      filename: string,
      type: string,
      data: string
    }[],
    rows?: PIXRowState[]
  }
}
```

### Notes
- `VERIFIED` The import code still supports this branch.
- `INFERRED` This is an older backup shape because the current export path produces `cobtool_backup_v2`.

## 14. Operator Data Payload

### Payload type
- `cobtool_operator_data_v1`

### JS wrapper format
```js
window.COBTOOL_OPERATOR_DATA = {
  payloadType: "cobtool_operator_data_v1",
  exportedAt: string,
  operator: string,
  localStorage: {
    [key: string]: string
  }
};
```

### Notes
- `VERIFIED` Import accepts either JSON or JS-wrapped payload text.
- `VERIFIED` Import only applies keys ending with the target operator suffix.
- `VERIFIED` Files shipped in `operadores/` currently contain empty `localStorage` objects and blank `exportedAt`.

## 15. Operator Session Payload

### Shape
```js
{
  operator: string,
  lastActive: number
}
```

### Notes
- `VERIFIED` `lastActive` is epoch milliseconds.
- `VERIFIED` Session validity is checked against a 2-hour idle window.
