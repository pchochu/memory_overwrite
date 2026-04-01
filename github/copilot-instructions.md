At the beginning of every session, you MUST read the following files and treat them as context:

1. ./.github/SOUL.md
2. ./.github/MEMORY.md
3. ./.github/AGENTS.md (shared agent rules, build commands, coding conventions)
<!-- The following are available skills -->
4. ./.github/skills/code-review.md
5. ./.github/skills/executing-plans.md
6. ./.github/skills/finishing-a-development-branch.md
7. ./.github/skills/subagent-drive-development.md
8. ./.github/skills/systematic-debugging.md
9. ./.github/skills/using-git-worktrees.md
10. ./.github/skills/verification-before-completion.md


# Copilot Instructions --- M.App Enterprise Web Monorepo

This file provides guidance for GitHub Copilot and AI coding assistants
working in the **M.App Enterprise (MAE) Web Monorepo**.

---

# 1. Platform Overview (M.App Enterprise)

## Purpose

M.App Enterprise (MAE) is a configurable geospatial application platform
for building enterprise operational applications centered around spatial
data.

Core paradigm:

Monitor → Evaluate → Act

- **Monitor**: Visualize spatial and business data
- **Evaluate**: Apply analytics, rules, workflows
- **Act**: Trigger workflows, edits, integrations

### Supported Application Types

- Operational GIS applications
- Monitoring dashboards
- Workflow-driven enterprise apps
- Decision-support systems
- Asset management portals

---

# 2. High-Level Architecture

Clients → MAE Apps → Platform Services → External Systems

## Core Components

- MAE Server
- Management Portal
- Studio
- Apps Runtime
- Workflow Engine
- Security Services
- Data Connectors

---

# 3. MAE Core Modules

## Management

Administrative configuration: - tenants - users - roles - connections -
deployments

## Studio

Low-code design environment: - app configuration - map composition - UI
layout - workflow binding

## Apps Runtime

- renders configured applications
- executes extensions
- hosts dashboards

## Dashboard

- widget-based visualization
- KPI monitoring
- spatial filtering

## Workflows

- task lifecycle
- approvals
- automation triggers

---

# 4. Development Model

## Configuration vs Code

Layer Method

---

UI Configured
Maps Configured
Workflows Modeled
Business Logic Extended
Integrations Implemented

Extension Layers:

Configuration\
↓\
Client Extensions (JS)\
↓\
Server Extensions (.NET)\
↓\
External Systems

---

# 5. Web Monorepo Architecture

This repository is a **pnpm monorepo** for M.App Enterprise web
applications.

## Structure

- `packages/apps/` -- Standalone applications
- `packages/libs/` -- Shared libraries (`mae-*`)
- `packages/statics/` -- Static/legacy/generated clients

### Key Applications

| App             | Purpose          | Build                                       |
| --------------- | ---------------- | ------------------------------------------- |
| mae-dashboard   | Data analysis    | `pnpm --filter @mae/dashboard build`        |
| mae-browsermap  | Map viewer       | `pnpm --filter @mae/browsermap build`       |
| mae-app-engine  | App scripting    | `pnpm --filter @mae/app-engine build`       |
| mae-custompanel | Custom panels    | `pnpm --filter @mae/custompanel build`      |
| mae-studio      | Authoring        | `pnpm --filter @mae/tasks build:studio`     |
| mae-management  | Admin            | `pnpm --filter @mae/tasks build:management` |
| mae-apps        | App launcher     | `pnpm --filter @mae/tasks build:apps`       |
| **All 3 above** | Studio+Mgmt+Apps | `pnpm --filter @mae/tasks build`            |

> **Note:** Studio, Management, and Apps are built via `@mae/tasks` (Gulp preprocessing + Webpack bundling).
> They do not have their own build scripts. See `.github/AGENTS.md` Build Reference for details.

---

# 6. Core Libraries

- `@mae/client-web` -- Generated API clients (.NET backend)
- `@mae/localization` -- i18next localization
- `@mae/components` -- Shared React components
- `@mae/utils` -- Utilities
- `@mae/config` -- Shared tooling configs

Generated folders MUST NOT be edited manually: - clients/ - models/ -
enums/

---

# 7. Developer Workflow

## Install

```bash
cd Web && pnpm install
```

## Build

```bash
pnpm --filter @mae/dashboard build
```

## Watch

```bash
pnpm --filter @mae/dashboard watch
```

## Lint

```bash
pnpm --filter @mae/<package> eslint
pnpm --filter @mae/<package> eslint:fix
```

---

# 8. Code Patterns

## Imports

```ts
import { i18NextStore } from "@mae/localization";
import { MetadataClient } from "@mae/client-web/metadata-client";
```

Avoid cross-package relative imports.

## State Management

- New: MobX + React Query
- Legacy: Knockout.js

Prefer modern stack for new code.

## Localization

```ts
const { t } = i18NextStore;
t("some.translation.key");
```

---

# 9. Configuration

## Webpack

Shared config: `@mae/config/webpack5`

## ESLint

Extends: `@mae/config/eslint-flat`

## TypeScript

Version: 5.7.3

---

# 10. Security Model

- Tenants (isolation)
- Roles (permissions)
- Claims (fine-grained access)
- Authentication:
  - Active Directory
  - SSO
  - OAuth/OpenID

Flow:

User → Identity Provider → MAE → Authorization

---

# 11. Deployment

Typical enterprise deployment: - IIS-hosted services - Database
backend - GIS services - Load balancing

Clients: - Browser - Desktop integrations - Mobile/offline

---

# 12. GIS & Data Concepts

Spatial sources: - WMS / WMTS - Feature services - Databases - External
APIs

Capabilities: - thematic rendering - spatial queries - aggregation -
workflow evaluation

---

# 13. AI Assistant Guidance

## Mental Model

MAE App = Configured App + Map + Data + Workflows + Extensions

### SHOULD

- Prefer configuration over coding
- Extend platform via SDKs
- Use REST integrations

### SHOULD NOT

- Replace runtime
- Bypass workflows
- Ignore tenant isolation

---

# Glossary

Term Meaning

---

MAE M.App Enterprise
App Configured application
Studio Design tool
Management Admin portal
Workflow Process automation
Dashboard Visualization workspace
Extension Custom module

---

# Copilot Instructions

Before generating code: - Check existing shared packages - Avoid
duplication - Extend existing patterns

## MEMORY.md policy

`.github/MEMORY.md` is for **rare, high-value, durable repository knowledge only**.

Write to `MEMORY.md` only when the information is likely to help in future work across multiple tasks or sessions, such as:

- stable architectural constraints
- non-obvious repository conventions
- recurring bug patterns or gotchas
- important workflow rules that should persist
- long-lived integration caveats
- durable design rules used repeatedly

Do **not** write to `MEMORY.md` for:

- per-task summaries
- brainstorm outputs
- temporary plans
- implementation progress
- PR summaries
- review comments
- one-off bug fixes unless they reveal a recurring pattern
- minor decisions relevant only to a single ticket/session
- anything that can be derived easily from code or the current PR

Default to **not writing**.  
If unsure, skip the memory update.
