---
description: >-
  Shared rules and context for all agents working in the MAE Web Monorepo.
  Every agent must read this file before starting work.
---

# Shared Agent Rules — MAE Web Monorepo

This document defines the shared rules, policies, and conventions that
**all agents** must follow. Individual agent files define role-specific
responsibilities only — shared rules live here.

---

## Agent Roles

| Agent | Responsibility | May Ask User Questions |
|-------|---------------|----------------------|
| **Orchestrator** | Task coordination, delegation, quality enforcement | Yes |
| **Planner** | Research, planning, acceptance criteria | Yes |
| **Designer** | UX/UI design, accessibility, visual consistency | Yes |
| **Coder** | Implementation, debugging, code review | No — escalate to Orchestrator |
| **FastCoder** | Small, trivial, well-scoped changes | No — escalate to Coder/Orchestrator |
| **MemoryAgent** | Durable repository knowledge maintenance | No |

---

## Shared Policies

### MEMORY.md Policy

`.github/MEMORY.md` is for **rare, high-value, durable repository knowledge only**.

**Write** only when the information is likely to help across multiple future tasks:
- Stable architectural constraints
- Non-obvious repository conventions
- Recurring bug patterns or gotchas
- Important workflow rules that should persist
- Long-lived integration caveats

**Do NOT write**:
- Per-task summaries, plans, or implementation notes
- PR summaries or review comments
- Brainstorm outputs or temporary ideas
- One-off bug fixes (unless they reveal a recurring pattern)
- Obvious facts derivable from code or config

**Default to not writing.** If unsure, skip.

### Testing Policy

Do not run automated test suites (`jest`, `cypress`) in any agent workflow.
Verification is done via **build commands** and **manual checks** only.

### Branch Naming

Branches must follow: `{type}/{version}/{ticket}-{description}`

- Types: `feature/`, `fix/`, `hotfix/`
- Versions: `16` or `17`
- Example: `feature/17/SME-5689-improve-eslint`

Never merge directly into `main`, `master`, or `develop` locally.

---

## Build Reference

This monorepo uses **pnpm** as its package manager. All commands run from the `Web/` directory.

### Install

```bash
pnpm install
```

### Application Build Commands

| Application | Package | Build Command | Watch Command |
|------------|---------|--------------|--------------|
| **Studio** | `@mae/studio` | `pnpm --filter @mae/tasks build:studio` | `pnpm --filter @mae/tasks watch:studio` |
| **Management** | `@mae/management` | `pnpm --filter @mae/tasks build:management` | `pnpm --filter @mae/tasks watch:management` |
| **Apps** | `@mae/apps` | `pnpm --filter @mae/tasks build:apps` | `pnpm --filter @mae/tasks watch:apps` |
| **All 3 above** | `@mae/tasks` | `pnpm --filter @mae/tasks build` | `pnpm --filter @mae/tasks watch` |
| **Dashboard** | `@mae/dashboard` | `pnpm --filter @mae/dashboard build` | `pnpm --filter @mae/dashboard watch` |
| **BrowserMap** | `@mae/browsermap` | `pnpm --filter @mae/browsermap build` | `pnpm --filter @mae/browsermap watch` |
| **App Engine** | `@mae/app-engine` | `pnpm --filter @mae/app-engine build` | `pnpm --filter @mae/app-engine watch` |
| **CustomPanel** | `@mae/custompanel` | `pnpm --filter @mae/custompanel build` | `pnpm --filter @mae/custompanel watch` |

> **Important:** Studio, Management, and Apps do NOT have their own build scripts.
> They are compiled through `@mae/tasks` which runs Gulp preprocessing (LESS, icons, constants, templates)
> followed by Webpack bundling of all three entry points.

### Library Build Commands

| Library | Package | Build Command | Notes |
|---------|---------|--------------|-------|
| **Localization** | `@mae/localization` | `pnpm --filter @mae/localization build` | Custom Node script; compiles distributed `en.json` files |
| **StyleEditor** | `@mae/styleeditor` | `pnpm --filter @mae/styleeditor build` | Rollup bundler (UMD + ESM) |
| **Client Web (legacy)** | `@mae/client-web-legacy` | `pnpm --filter @mae/client-web-legacy build` | Generated API clients |

Most libraries (`@mae/components`, `@mae/utils`, `@mae/mui-components`, `@mae/luciadria-utils`, `@mae/config`)
are **source-only** — they export TypeScript directly and have no build step.

### Lint Commands

```bash
pnpm --filter @mae/<package> eslint        # Check
pnpm --filter @mae/<package> eslint:fix    # Auto-fix
```

### Build Pipeline: @mae/tasks

The `@mae/tasks` package is the central build hub for Studio, Management, and Apps:

1. **Phase 1 — Gulp preprocessing** (`pre:build:ts`):
   - Compiles LESS themes to CSS
   - Generates TypeScript constants from JSON
   - Generates icon enums from SVG sets
   - Processes HTML templates into TypeScript modules

2. **Phase 2 — Webpack bundling** (`build:webpack`):
   - Bundles Studio (`mae-studio/src/index.tsx`)
   - Bundles Management (`mae-management/src/index.tsx`)
   - Bundles Apps (`mae-apps/src/studio.ts`)
   - Uses `ts-loader`, `ForkTsCheckerWebpackPlugin`, `MonacoWebpackPlugin`

### Build Verification Checklist

When verifying any change, build the **affected app(s)**:

1. Identify which app(s) your change affects
2. Run the corresponding build command from the table above
3. Confirm zero build errors
4. Run `pnpm --filter @mae/<package> eslint` for lint checks

---

## Coding Conventions

### Folders and Files

- **kebab-case only** — lowercase alphanumerics, no spaces, numbers as postfix only
- Keep names **short**
- Use **plural** for container/namespace folders
- Do **not** repeat parent folder names
- Use `index.ts` for single-file folders or component folders

### Package Structure

```
mae-{package-name}/
├── @types/          # Declaration files
├── scripts/         # Build configs
├── src/             # Source code (ts, tsx, json, scss)
└── package.json
```

- Package folder prefix: `mae-`
- Package name in `package.json`: `"@mae/{package-name}"` (kebab-case)

### Classes and Components

- **PascalCase** for class/component names
- Abbreviations in **CAPS** (e.g., `HTTPClient`, `URLParser`)

### Imports

- No explicit `index.ts` imports (`./folder/index`)
- No absolute path imports via `tsconfig baseUrl`
- No alias imports — create a package instead if needed
- No cross-package relative imports

### Exports

- Prefer **inline exports** (`export function`, `export const`)
- Avoid wrapping/encapsulation — create a component instead

### Types, Interfaces, and Enums

| Kind | Convention | Example |
|------|------------|---------|
| Types | Postfix `Type` | `UserType` |
| Props | Postfix `Props` | `ButtonProps` |
| State | Postfix `State` | `FormState` |
| Options | Postfix `Options` | `FetchOptions` |
| Enums | Postfix plural `Types` | `StatusTypes` |
| Interfaces | Prefix `I` | `IUserService` |

> Do not prefix file names with `I` — only interface declarations.

### Functions

Prefer **function declarations** over arrow functions (unless `this` or `arguments` binding is needed).

### Code Organization

Organize code top-to-bottom by order of interest. Use `// #region` comments.
Within each region, place **public members first**, then private.

### State Management

| Stack | Usage |
|-------|-------|
| **MobX + React Query** | New code (preferred) |
| **Knockout.js** | Legacy code (`@mae/mvc`, `@mae/mvc-studio`) |

Prefer the modern stack for all new code.

---

## Localization Architecture

The localization system compiles distributed `en.json` files into a single `localization.json` in `@mae/localization`.

### Usage

```ts
const { t } = i18NextStore;
t("some.translation.key");
```

### File Locations

**Shared** (`@mae/localization/src/locales/`):

| File | Index | Purpose |
|------|-------|---------|
| `common.en.json` | `""` (root) | Common UI labels |
| `errors.en.json` | `Errors` | Error messages |
| `validation.en.json` | `Validation` | Form validation |
| `confirm.en.json` | `Confirm` | Confirmation dialogs |
| `progress.en.json` | `Progress` | Loading states |

**Application-specific**:

| App | File | Index |
|-----|------|-------|
| Dashboard | `mae-dashboard/src/locales/en.json` | `Apps.Dashboard` |
| Studio | `mae-studio/src/locales/en.json` | `Studio` |
| Management | `mae-management/src/locales/en.json` | `Management` |
| App Engine | `mae-app-engine/src/locales/en.json` | `Apps.AppEngine` |

### Where to Add Keys

1. **Generic UI terms** → `common.en.json`
2. **Error messages** → `errors.en.json`
3. **Validation** → `validation.en.json`
4. **Confirmations** → `confirm.en.json`
5. **App-specific** → the app's own `src/locales/en.json`

---

## Mandatory Coding Principles

1. **Structure** — Consistent layout. Group by feature. Reuse existing structures.
2. **Architecture** — Flat, explicit code. Avoid unnecessary abstraction or indirection.
3. **Control Flow** — Linear and readable. No deep nesting. Pass state explicitly.
4. **Naming** — Descriptive, intention-revealing names.
5. **Comments** — Only for invariants, assumptions, and external constraints.
6. **Errors** — Explicit, actionable, deterministic. Fail clearly.
7. **Platform** — Follow standard TypeScript/React conventions. Use framework-native solutions.
8. **Modifications** — Follow existing patterns. Prefer modifying over adding. Reuse internal libraries.
9. **Quality** — Deterministic, testable behavior. Minimize risk and side effects.

---

## Generated Code — Do Not Edit

The following folders contain auto-generated API clients and must never be edited manually:
- `@mae/client-web/clients/`
- `@mae/client-web/models/`
- `@mae/client-web/enums/`
