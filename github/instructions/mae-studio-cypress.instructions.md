````instructions
---
applyTo: Web/packages/apps/mae-studio/cypress/**
---
# M.App Enterprise Studio — Cypress E2E Testing

## Overview

`mae-studio` uses **Cypress 15.7+** for E2E testing against a **live local server** at `http://localhost/mae-dev17/Studio/`. Tests run against real API endpoints (some tests stub responses via `cy.intercept`). The test suite covers security, content management, mobile client credentials, branding, and geoservices.

## Project Structure

```
Web/packages/apps/mae-studio/
├── cypress.config.ts                  # Cypress configuration (e2e + component)
├── cypress.tsconfig.json              # TS config for Cypress (extends mae-config)
├── webpack.cypress.config.js          # Custom webpack config for Cypress preprocessor
├── cypress/
│   ├── fixtures/
│   │   └── example.json               # Sample fixture data
│   ├── support/
│   │   ├── e2e.ts                     # Auto-loaded before every E2E spec (imports commands.ts)
│   │   ├── commands.ts                # Global custom commands (login, snackbar, navigation)
│   │   ├── form-commands.ts           # Reusable form interactions (input/textarea/select/submit/errors)
│   │   ├── table-commands.ts          # Reusable table row and list interactions
│   │   ├── navigation-commands.ts     # Reusable Studio hash-route navigation helpers
│   │   ├── dialog-commands.ts         # Reusable confirm/cancel dialog helpers
│   │   ├── name-title-commands.ts     # Name field validation commands
│   │   ├── functions.ts               # Shared selector helpers (getSelectors, getRowKey)
│   │   ├── const.ts                   # Shared constants (pwd, validationWaitMs)
│   │   ├── index.d.ts                 # Custom command type declarations
│   │   └── geoservices/
│   │       └── commands.ts            # Geoservice-specific custom commands
│   └── e2e/
│       ├── client-credentials.cy.ts   # Mobile client credential CRUD (stubbed)
│       ├── security/
│       │   ├── branding.cy.ts         # Login branding/theming (stubbed)
│       │   ├── api-keys.cy.ts         # API key CRUD (live + assertion on real responses)
│       │   ├── quotes.cy.ts           # Login quotes CRUD (live)
│       │   └── password-pattern.cy.ts # Password pattern CRUD (live)
│       └── content/
│           ├── connections/
│           │   └── index.cy.ts        # DB connection CRUD (Oracle, SQL Server, PostgreSQL)
│           ├── vector-data/
│           │   └── index.cy.ts        # Vector data CRUD, inline form validation, edit workflow
│           └── geoservices/
│               ├── tms.cy.ts          # TMS geoservice CRUD + URL validation
│               ├── here.cy.ts         # HERE3 geoservice CRUD + layer persistence
│               └── google.cy.ts       # Google geoservice CRUD + field validation
```

## Configuration

### cypress.config.ts

- **E2E**: Uses `@cypress/webpack-preprocessor` with the custom webpack config (`webpack.cypress.config.js`).
- **Component Testing** (unused for Studio currently): Configured with React + Webpack dev server; spec pattern `cypress/components/**/*.cy.{ts,tsx}`, support file `cypress/support/component.ts`.
- The webpack config uses `ts-loader` with `cypress.tsconfig.json` which extends `../../libs/mae-config/tsconfig.json` and includes only `cypress/**/*.ts`.
- The webpack config uses `TsconfigPathsPlugin` to resolve monorepo path aliases (e.g., `@mae/*`).

### Running Tests

```bash
# From Web/packages/apps/mae-studio/
pnpm cy:open     # Opens Cypress interactive runner
```

**Prerequisites:**
- The local M.App Enterprise server must be running at `http://localhost/mae-dev17/`
- A tenant named `Cypress` (or `postgis` for quotes tests) must exist
- An `Admin`/`Admin` user must exist in the tenant

## Custom Commands

All custom commands are declared in `cypress/support/index.d.ts` and must be kept in sync.

### Global Commands (cypress/support/commands.ts)

| Command | Purpose |
|---------|---------|
| `cy.loginAsAdmin(tenant?)` | Logs in as Admin/Admin to the given tenant (default: `'Cypress'`). Visits the login page, fills credentials, submits, and waits for the OAuth token response. |
| `cy.closeSnackbars()` | Dismisses all visible notistack snackbar notifications (clicks dismiss button or hides via style). |
| `cy.resetSessionState()` | Clears localStorage and cookies. |
| `cy.openNewForm()` | Clicks the `[data-cy=labeled-icon-button-new-button]` button and asserts `/new` is in the URL. |

### Form Commands (cypress/support/form-commands.ts)

| Command | Purpose |
|---------|---------|
| `cy.fillTextInput(dataCy, value, typeOptions?)` | Clears + types into visible input under a `data-cy` field wrapper and blurs. |
| `cy.fillTextareaInput(dataCy, value, typeOptions?)` | Clears + types into visible textarea under a `data-cy` field wrapper and blurs. |
| `cy.selectDropdownValue(dataCy, optionText)` | Opens MUI select and picks matching listbox option text. |
| `cy.setCheckboxValue(dataCy, checked)` | Checks/unchecks checkbox by `data-cy` with state assertion. |
| `cy.waitForValidationDebounce(waitMs?)` | Wait helper for debounced validations (default `validationWaitMs`). |
| `cy.submitForm()` | Clicks `[data-cy="form-submit-button"]`. |
| `cy.assertRequiredFieldErrors(fields)` | Asserts required error styles/helper text for form fields. |

### Table Commands (cypress/support/table-commands.ts)

| Command | Purpose |
|---------|---------|
| `cy.assertTableHasRows(rowSelector, minCount?)` | Asserts table rows count is at least expected minimum. |
| `cy.assertRowContainsText(rowSelector, text, timeoutMs?)` | Asserts row matching text exists. |
| `cy.clickRowAction(rowSelector, text, actionSelector)` | Executes a row-scoped action (edit/delete) for a row identified by text. |
| `cy.deleteRowAndConfirm(rowSelector, text, deleteAlias?)` | Deletes a row and confirms dialog, optionally waiting alias. |
| `cy.assertRowNotExists(rowSelector, text)` | Asserts row text no longer exists in table. |

### Navigation Commands (cypress/support/navigation-commands.ts)

| Command | Purpose |
|---------|---------|
| `cy.visitStudioPath(hashPath, tenant?)` | Visits Studio URL with hash route and tenant query. |
| `cy.navigateSidebarItem(dataCy)` | Clicks a sidebar/menu item by `data-cy`. |

### Dialog Commands (cypress/support/dialog-commands.ts)

| Command | Purpose |
|---------|---------|
| `cy.confirmDialog()` | Confirms open dialog via accept button. |
| `cy.cancelDialog()` | Cancels open dialog via cancel button (fallback Esc). |

### Name Validation Commands (cypress/support/name-title-commands.ts)

| Command | Purpose |
|---------|---------|
| `cy.nameAlreadyExists()` | Asserts the `[data-cy="name"]` field shows an "already exists" error. |
| `cy.validateName(validNames, invalidNames)` | Tests the name field against length limits, special character patterns, and backend `isvalid` validation. |

### Geoservice Commands (cypress/support/geoservices/commands.ts)

| Command | Purpose |
|---------|---------|
| `cy.beforeEachGeoserviceTest()` | Sets up all geoservice-related intercepts, resets state, and logs in. |
| `cy.visitGeoservicesPage()` | Navigates to `/content/geoservices` and waits for list data. |
| `cy.openNewGeoservice(type)` | Opens the dropdown and selects a geoservice type (`tms`, `here`, `google`). |
| `cy.openExistingGeoservice(name)` | Clicks a geoservice row by name and waits for detail fetch. |
| `cy.deleteGeoservice(name)` | Deletes a geoservice by name from the list. |
| `cy.geoserviceNameAlreadyExists(type, name)` | Opens a new geoservice form, types a duplicate name, and asserts the error. |
| `cy.geoserviceInList(name)` | Asserts a geoservice appears in the list table. |

### Shared Helpers (cypress/support/functions.ts)

```typescript
// Returns a map of common data-cy selectors for a given row key
getSelectors(rowKey?: string) → { row, newButton, submitButton, deleteButton, editButton, acceptButton }

// Builds the row key from a table ID: `row-${list}`
getRowKey(list: string) → string
```

### Constants (cypress/support/const.ts)

```typescript
export const pwd = 'Australia.1';          // Shared test password
export const validationWaitMs = 310;       // Debounce wait for async validation
```

## Key Patterns & Conventions

### 1. Base URL Pattern

All test URLs follow the pattern:
```
http://localhost/mae-dev17/Studio/?tenant={TENANT}#/{section}/{subsection}
```
- Hash-based routing (`#/`)
- Tenant is passed as a query parameter
- Most tests use `tenant=Cypress`; some use `tenant=postgis`

### 2. data-cy Attribute Selectors

The app uses `data-cy` attributes for test targeting. Key patterns:

| Selector | Purpose |
|----------|---------|
| `[data-cy="field-name"]` | Form field wrapper (contains MUI input) |
| `[data-cy="field-name"] input` | Actual input element inside MUI field |
| `[data-cy="field-name"] textarea:visible` | Textarea inside MUI field |
| `[data-cy="form-submit-button"]` | Form submit button |
| `[data-cy="new-button"]` | New item button (simple pages) |
| `[data-cy="labeled-icon-button-new-button"]` | New item button (icon-labeled variant) |
| `[data-cy="dropdown-button"]` | Dropdown trigger for type selection |
| `[data-cy="dropdown-item-{type}"]` | Dropdown item for specific type |
| `[data-cy="edit-row-button"]` | Edit button within a table row |
| `[data-cy="delete-row-button"]` | Delete button within a table row |
| `[data-cy="accept-button"]` | Confirmation dialog accept button |
| `[data-cy="row-{TableId}"]` | Table row identified by table list ID |
| `[data-cy="cell-{TableId}-{column}"]` | Cell within a table row |
| `[data-cy="true-or-null-icon"]` | Boolean indicator icon in cells |
| `[data-cy="radio-option-{group}-{value}"]` | Radio button option |
| `[data-cy="edit-text-field-{rowIndex}-{field}"]` | Inline editable text field in form tables |
| `[data-cy="edit-boolean-field-{rowIndex}-{field}"]` | Inline editable boolean field in form tables |
| `[data-cy="edit-text-field-error-icon-{rowIndex}-{field}"]` | Error icon for inline field |
| `[data-cy="{field}-error"]` | Error message element for a form field |

### 3. Table Row Identification via IDs

Table rows use `data-cy` attributes constructed from `Ids` constants from `@mae/mvc-studio/table`:

```typescript
import { Ids } from '@mae/mvc-studio/table';

const rowKey = `row-${Ids.Studio.Content.VectorData.List}`;
// → 'row-Studio.Content.VectorData.List'
const rowSelector = `[data-cy="${rowKey}"]`;
```

Common IDs used in tests:
- `Ids.Studio.Content.VectorData.List`
- `Ids.Studio.Content.Connections.List`
- `Ids.Studio.Content.Geoservices.List`
- `Ids.Studio.Security.PasswordPatterns.List`
- `Ids.Studio.Security.Quotes.List`
- `Ids.Studio.Security.ApiKeys.List`
- `Ids.Studio.Mobile.ClientCredentials.List`

### 4. API Intercept Pattern

Tests use `cy.intercept()` to alias and optionally stub API calls. Two approaches coexist:

**Live responses (wait only):**
```typescript
cy.intercept('GET', '**/api/v1/studio/content/vector').as('getVectorData');
// ... perform action ...
cy.wait('@getVectorData');
```

**Stubbed responses (mock data):**
```typescript
cy.intercept('GET', '**/api/v1/studio/quotes', {
    statusCode: 200,
    body: mockedData
}).as('getMockedQuotes');
```

**Dynamic stubs with assertions:**
```typescript
cy.intercept('POST', '**/api/v1/studio/mobile/clientcredentials', (req) => {
    expect(req.body).to.deep.equal({ clientId: 'expected', ... });
    credentials.push(createdItem);
    req.reply({ statusCode: 201, body: {} });
}).as('createClientCredential');
```

### 5. Test Structure Conventions

**CRUD tests** typically follow this flow:
1. Visit list page → wait for list API
2. Open new form → fill fields → submit → wait for create API
3. Assert item appears in list
4. Test duplicate name validation
5. Open item for edit → modify fields → submit → wait for update API
6. Assert updated values in list or re-opened form
7. Delete item → wait for delete API → assert removed

**beforeEach pattern:**
```typescript
beforeEach(() => {
    setupIntercepts();         // Register cy.intercept aliases
    cy.resetSessionState();    // or cy.clearLocalStorage() + cy.clearCookies()
    cy.loginAsAdmin();         // Login flow
});
```

### 6. Validation Debounce Handling

Many fields have debounced async validation (typically 310ms). Tests use:
```typescript
cy.wait(310);                           // Explicit debounce wait
cy.wait(validationWaitMs);              // Using constant from const.ts
cy.wrap(null).then(() => Cypress.Promise.delay(310));  // Alternative delay
```

### 7. MUI Component Interaction Patterns

**Select/Dropdown:**
```typescript
cy.get('[data-cy="fieldName"]').click();
cy.get('ul[role="listbox"]').contains('Option Text').click();
```

**Checkbox:**
```typescript
cy.get('[data-cy="fieldName"] input[type="checkbox"]').check().should('be.checked');
cy.get('[data-cy="fieldName"] input[type="checkbox"]').should('not.be.checked');
```

**Text Input:**
```typescript
cy.get('[data-cy="fieldName"] input').clear().type('value').blur();
```

**Textarea:**
```typescript
cy.get('[data-cy="fieldName"] textarea:visible').clear().type('value');
```

**Error Assertion:**
```typescript
cy.get('[data-cy="fieldName"] .MuiOutlinedInput-root').should('have.class', 'Mui-error');
cy.get('[data-cy="fieldName"]').parent().find('p.MuiFormHelperText-root').should('contain.text', 'error message');
```

### 8. Special Characters in Type Input

When typing values that contain `{` or `}` characters (e.g., TMS URLs with `{z}/{x}/{y}`), use:
```typescript
cy.get('input').type(value, { parseSpecialCharSequences: false });
```

### 9. Snackbar Handling

Snackbar notifications can obscure UI elements. Call `cy.closeSnackbars()` after operations that trigger notifications, especially before interacting with elements that may be covered.

### 10. TinyMCE Rich Text Editor

For tooltip/rich-text fields using TinyMCE:
```typescript
// Write bold content
cy.get('iframe.tox-edit-area__iframe').its('0.contentDocument.body').should('not.be.empty').then(cy.wrap).as('editorBody');
cy.get('button[aria-label="Bold"]').click();
cy.get('@editorBody').click().type(content);

// Assert bold content
cy.get('iframe.tox-edit-area__iframe').its('0.contentDocument.body').within(() => {
    cy.get('strong, b').should('contain.text', expectedContent);
});
```

## Test Categories

### Stubbed Tests (mock all API responses)

- **client-credentials.cy.ts** — Full CRUD with dynamic intercept handlers that mutate an in-memory array
- **branding.cy.ts** — Branding preset selection, custom color inputs, preview toggle, persistence

### Live Tests (real API, intercepts for waiting/assertion only)

- **api-keys.cy.ts** — Token generation, CRUD, creation time validation
- **quotes.cy.ts** — Simple CRUD for login quotes
- **password-pattern.cy.ts** — CRUD with boolean `isActivated` toggle
- **connections/index.cy.ts** — Multi-provider DB connection CRUD (Oracle, SQL Server, PostgreSQL), required field validation, provider switching, duplicate detection
- **vector-data/index.cy.ts** — Complex multi-row inline form, import-based creation, field validation (name, SRID, minScale/maxScale), edit form with rich-text tooltip
- **geoservices/tms.cy.ts** — TMS URL pattern validation, CRUD
- **geoservices/here.cy.ts** — HERE3 API integration, layer selection persistence, tile type clearing behavior
- **geoservices/google.cy.ts** — Google Maps geoservice CRUD with language selection

## Adding a New E2E Test

### Step-by-step

1. **Create the test file** at `cypress/e2e/{section}/{subsection}.cy.ts`
2. **Import shared helpers:**
   ```typescript
   import { Ids } from '@mae/mvc-studio/table';
   import { getRowKey, getSelectors } from '../../support/functions';
   ```
3. **Set up constants:**
   ```typescript
   const rowKey = getRowKey(Ids.Studio.Section.SubSection.List);
   const selectors = getSelectors(rowKey);
   ```
4. **Register intercepts** in `beforeEach` or a setup function
5. **Use the standard CRUD pattern** (visit → create → verify → edit → verify → delete → verify)
6. **Add new custom commands** to `cypress/support/commands.ts` (or domain-specific file) and update `cypress/support/index.d.ts`

### New Command Checklist

When adding a custom Cypress command:
1. Implement in `cypress/support/commands.ts` (or a domain-specific file under `support/`)
2. Import the file in `cypress/support/commands.ts` (if separate file)
3. Add the TypeScript declaration in `cypress/support/index.d.ts`

## Abstraction Conventions

1. **Form interactions**
    - Use `cy.fillTextInput`, `cy.fillTextareaInput`, `cy.selectDropdownValue`, `cy.setCheckboxValue`, and `cy.submitForm`.
    - Keep spec-level helpers only for domain-specific flows (e.g., HERE layer multiselect logic, TinyMCE workflows).

2. **Table interactions**
    - Use `cy.assertTableHasRows`, `cy.assertRowContainsText`, `cy.clickRowAction`, `cy.deleteRowAndConfirm`, `cy.assertRowNotExists` for generic CRUD list operations.
    - Keep row-key creation via `Ids` + `getRowKey` centralized per spec.

3. **Navigation interactions**
    - Prefer `cy.visitStudioPath('/section/subsection')` over inline full URL literals.
    - Keep tenant override explicit only when tests target non-default tenants.

4. **Dialog interactions**
    - Use `cy.confirmDialog` / `cy.cancelDialog` instead of inline accept/cancel selector clicks.

5. **Shared constants/selectors**
    - Store cross-spec selectors and common wait timings in `cypress/support/const.ts`.
    - Avoid re-declaring selectors like submit, accept, listbox, or base URL in specs.

## Best Practices for New Tests

- Prefer support-level abstractions for repeatable UI operations; keep specs focused on scenario intent.
- Keep API intercept setup in spec-level `beforeEach`, but reuse navigation/form/table/dialog commands for interactions.
- Preserve deterministic waits by waiting on aliases first and only using debounce waits (`validationWaitMs`) when required.
- When introducing a reusable command, update implementation + `index.d.ts` + this instruction file in the same change.

## Differences from mae-mui-components Cypress Setup

`mae-mui-components` has a **component testing** setup (mount individual React components in isolation):
- Config: `packages/libs/mae-mui-components/cypress.config.ts`
- Specs: `cypress/components/**/*.cy.tsx`
- Support: `cypress/support/component.ts` (uses `cypress/react` mount)

`mae-studio` uses **E2E testing** (full app running in browser against local server):
- Config: `packages/apps/mae-studio/cypress.config.ts`
- Specs: `cypress/e2e/**/*.cy.ts`
- Support: `cypress/support/e2e.ts`

## Common Pitfalls

1. **Tenant must exist** — Tests assume `Cypress` (or `postgis`) tenant with `Admin`/`Admin` credentials
2. **Server must be running** — E2E tests hit `http://localhost/mae-dev17/` directly
3. **Snackbar occlusion** — Always call `cy.closeSnackbars()` between actions that trigger notifications and subsequent UI interactions
4. **Debounce timing** — Use `cy.wait(310)` or `validationWaitMs` for fields with async validation
5. **MUI Select interaction** — Click the `[data-cy]` wrapper first, then select from `ul[role="listbox"]`; close with `cy.get('body').click(0, 0)` or `type('{esc}')`
6. **Form submit race** — After filling fields, wait for validation debounce before clicking submit
7. **`parseSpecialCharSequences: false`** — Required when typing `{`, `}` characters literally
8. **`it.only`** — The vector-data test has an `it.only` on the edit workflow test; remove before committing if not debugging
9. **Flaky row assertions** — Use `{ timeout: 15000 }` on `cy.contains(rowSelector, name)` for slow list re-renders
10. **Import paths** — Tests can import from app source (e.g., `../../../../src/models/content/...`) via the webpack alias config
11. **Inline duplication drift** — Prefer `form-commands`, `table-commands`, `navigation-commands`, and `dialog-commands` for generic UI flows; keep specs focused on scenario-specific assertions.
````
