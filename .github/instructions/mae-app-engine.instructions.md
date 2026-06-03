---
applyTo: Web/packages/apps/mae-app-engine/**
---
# M.App Enterprise App Engine

A visual scripting IDE for building custom applications with drag-and-drop panels and TypeScript code editing.

**Entry Point:** `src/main.ts` → `MultiViewerComponent`  
**Build:** Standalone webpack via `scripts/webpack.config.js`  
**Package Name:** `@mae/app-engine`

## Architecture

- **Editor mode** (`?edit=true`): `src/editor/` - GoldenLayout panels + Monaco code editor
- **Runtime mode**: `src/runtime/` - Executes saved app configurations

### Panel Types (`RuntimeType` enum)

| Type | Purpose |
|------|---------|
| `map-view` | LuciadRIA map display |
| `dashboard` | Analytics/charts panel |
| `custom-panel` | User-defined TypeScript panel |
| `workflow-panel` | Workflow execution |
| `recipes` | Pre-built analysis tools |

## Key Dependencies

- **GoldenLayout 1.5.9** - Panel layout management
- **Monaco Editor** - TypeScript editing with LuciadRIA autocompletion
- **`@mae/app-engine-api`** - Shared interfaces, transpiler, message broker

## Message Broker Pattern

Panels communicate via publish/subscribe (`@mae/app-engine-api/message-broker`):

```typescript
import { Broker, Agent } from '@mae/app-engine-api/message-broker';

// In runtime, broker is on window.__broker
const agent = broker.createAgent('my-panel');
agent.ready(() => {
  agent.listen((from, payload) => { /* handle messages */ });
  agent.post({ type: 'update', data: value }, 'target-panel');
});
```

## Code Snippets

Editor sidebar provides snippets in `src/editor/side-bar/snippets/`:
- `map.ts`, `layer.ts`, `vector.ts` - Map/layer operations
- `communication.ts` - Broker messaging patterns
- `transformation.ts` - Geometry transforms
- `recipes.ts` - Pre-built analysis workflows

## Monaco Autocompletion System

The Monaco editor provides LuciadRIA autocompletion via `@mae/app-engine-api/autocompletion`:

### Generated Definition Files

Two JSON files in `@mae/app-engine-api/src/json/` provide type information:
- **`luciad-definitions.json`** - TypeScript `.d.ts` from `@luciad/ria`
- **`monaco-editor-auto-imports.json`** - Export metadata for auto-import

Regenerate when updating LuciadRIA:
```bash
cd Web/packages/libs/mae-app-engine-api
pnpm run generate-defs         # luciad-definitions.json
pnpm run generate-auto-imports # monaco-editor-auto-imports.json
```

### Architecture

```
src/autocompletion/
├── init.ts              # initializeMonacoAutoCompletion(), attachAutoCompletion()
├── import-store.ts      # ImportStore - caches available imports
├── import-completion.ts # CompletionItemProvider for Monaco
├── import-action.ts     # CodeActionProvider for auto-import fixes
├── import-fixer.ts      # Inserts import statements
└── resolution/          # Kind resolution for completion icons
```

### Usage in Monaco Setup

```typescript
import { initializeMonacoAutoCompletion, attachAutoCompletion } from '@mae/app-engine-api/autocompletion';
import { getImportStore } from '@mae/app-engine-api/luciad-autocompletion-store';

// Initialize once globally
initializeMonacoAutoCompletion();

// Attach to each editor instance
const importStore = getImportStore();
attachAutoCompletion(editorInstance, importStore);
```

### Additional Type Definitions

Monaco also loads definitions for internal APIs (in `src/editor/monaco.tsx`):
- `@mae/app-engine-api/message-broker/broker.d.ts` - Broker/Agent types
- `@mae/client-web/clients-external/query-client-api.d.ts` - Query wrapper
- `@mae/app-engine-api/apis/warehouse-api/index.d.ts` - Warehouse API
- `@mae/app-engine-api/apis/workflow-api/index.d.ts` - Workflow API
- `@mae/app-engine-api/apis/point-cloud-api/index.d.ts` - Point cloud API

## Webpack Config

Uses `@mae/config/webpack5` with Monaco plugin:

```javascript
// scripts/webpack.config.js
config.plugins.push(new MonacoWebpackPlugin());
```

## Build Commands

```bash
pnpm --filter @mae/app-engine watch  # Development
pnpm --filter @mae/app-engine build  # Production
pnpm --filter @mae/app-engine eslint # Lint
```
