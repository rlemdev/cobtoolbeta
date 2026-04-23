# Cobtool Operator Session Map

## Status
- `VERIFIED` Operator identity is a first-class runtime boundary in Cobtool.
- `VERIFIED` Operator choice affects storage keys, session state, some document template selection, and exported metadata labels.

## Operator Inventory
- `VERIFIED` The operator list in code is:
  - Carlyle
  - Karoline
  - Lúcia
  - Pedro
  - Rafael
  - Renan
  - Vanderleia

## Core Keys

| Key | Purpose | Status |
| --- | --- | --- |
| `cobtool_alfa_operador_v1` | current operator identity | `VERIFIED` |
| `cobtool_operator_session_v1` | last active operator session | `VERIFIED` |

### Session payload
```js
{
  operator: string,
  lastActive: number
}
```

## Unlock Flow

### UI
- `VERIFIED` The unlock page is `page-operator-unlock`.
- `VERIFIED` The operator picker is a custom dropdown, not a native `<select>`.

### Unlock behavior
- `VERIFIED` On confirm:
  - selected operator is normalized
  - operator session is written to `cobtool_operator_session_v1`
  - current operator name is written to `cobtool_alfa_operador_v1`
  - `body.operator-locked` is removed
  - pending page is restored

### Default lock behavior
- `VERIFIED` On app startup, the code reads the stored session.
- `VERIFIED` If the session is missing or expired, the UI is locked and redirected to `operator-unlock`.

## Idle Session Rules

### Idle timeout
- `VERIFIED` `COBTOOL_OPERATOR_IDLE_MS = 2 * 60 * 60 * 1000`
- `VERIFIED` Sessions expire after two hours of inactivity.

### Activity refresh
- `VERIFIED` Activity is tracked on:
  - `click`
  - `keydown`
  - `input`
  - `pointerdown`
  - `touchstart`
- `VERIFIED` Session refresh also occurs when the tab becomes visible again.

### Write throttling
- `VERIFIED` Activity writes are throttled to avoid frequent storage writes.
- `VERIFIED` The current throttle threshold is 15 seconds between session writes unless forced.

## Operator Switching

### Top menu action
- `VERIFIED` `#top-menu-switch-operator` forces a return to the unlock page.

### What switching does
- `VERIFIED` Clears `cobtool_operator_session_v1`
- `VERIFIED` Sets lock state
- `VERIFIED` Sends the user back to `operator-unlock`

### What switching does not do
- `VERIFIED` It does not erase operator-scoped business data from localStorage.
- `VERIFIED` It does not clear all browser persistence.

## Operator Identity Effects

### 1. Storage scoping
- `VERIFIED` Most business keys use `cobtoolScopedStorageKey(baseKey)`.
- `VERIFIED` The resolved key depends on the current operator slug.

### 2. Alfa workspace behavior
- `VERIFIED` Alfa restores and then locks the operator select to the active operator.
- `VERIFIED` Generated cobrança messages include operator identity text.

### 3. Document templates
- `VERIFIED` Cobrança and quitação letter template selection depends on the operator name.

### 4. Registro-boletos metadata
- `VERIFIED` Exported boleto metadata payload includes the current operator label.
- `VERIFIED` Imported metadata panel visibility depends on the resolved operator label equaling `Rafael`.

### 5. Operator data file portability
- `VERIFIED` Operator payload import/export logic is based on operator name and operator-specific storage suffixes.

## Operator-Scoped Storage Contract

### Suffix format
- `VERIFIED` `__<operator-slug>`

### Example
- `cobtool_clientes_v1__rafael`
- `cobtool_negociacoes_v1__carlyle`

### Migration behavior
- `VERIFIED` If a new scoped key is empty but an old base key exists, the app copies base-key content into the scoped key automatically.

## What Cannot Be Broken

### Hard invariants
- `VERIFIED` Operator names must continue normalizing to the same slugs unless a migration is introduced.
- `VERIFIED` The session payload must remain parseable as `{ operator, lastActive }`.
- `VERIFIED` Unlock must still restore the pending destination page.
- `VERIFIED` Switch operator must not silently merge one operator’s scoped data into another operator’s keys.
- `VERIFIED` Alfa operator locking must remain aligned with the session gate.

### High-risk changes
- renaming `cobtool_alfa_operador_v1`
- renaming `cobtool_operator_session_v1`
- changing slug generation
- removing session expiry handling
- changing operator names without checking template filenames and stored suffixes

## Ambiguities
- `INFERRED` Decorative chess-piece icons on the unlock screen are UI-only and not part of the business contract.
- `VERIFIED` Stub files in `operadores/` define payload format but do not currently represent real operator backups.
