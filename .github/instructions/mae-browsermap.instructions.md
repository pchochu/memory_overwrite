---
applyTo: Web/packages/apps/mae-browsermap/**
---
# M.App Enterprise BrowserMap

`mae-browsermap` is the **LuciadRIA-based map viewer client** for M.App Enterprise. It renders interactive maps with layers, bookmarks, measurements, effects, and more.

**Entry Point:** `src/app.tsx`  
**Build:** Standalone webpack via `scripts/webpack.config.js`  
**Package Name:** `@mae/browsermap`

## Architecture

### Application Lifecycle

```
URL Parameters (appId, runtime, id, mapviewid)
        ↓
    ClientUtil.getConfigIds()
        ↓
┌───────────────────────────────────────┐
│  AppEngine Mode (appId param)         │
│  - Load IApp config                   │
│  - Get runtime config                 │
│  - Process script/style attachments   │
└───────────────────────────────────────┘
        OR
┌───────────────────────────────────────┐
│  Legacy Mode (id param)               │
│  - Load legacy app config             │
│  - Run legacy custom scripts          │
└───────────────────────────────────────┘
        ↓
    RuntimeMap creation
        ↓
    RIAContext.Provider + BrowserMapUI
```

### Key Components

| Component | Location | Purpose |
|-----------|----------|---------|
| `BrowserMapClient` | `src/app.tsx` | Main app component, manages map lifecycle |
| `BrowserMapUI` | `src/app/ria-client.tsx` | UI shell with RIA menu tabs |
| `RuntimeMap` | `@mae/luciadria-utils` | LuciadRIA map wrapper with layer management |
| `RIAContext` | `@mae/components` | Shared context for map instance and action sets |
| `RIAClientContext` | `src/app/utils/ria-client-context.tsx` | App-specific context (appId, mapViewId, client) |

## Context System

### RIAContext (from @mae/components)

Provides the map instance and action set configuration to all child components:

```typescript
<RIAContext.Provider value={{
    map: riaMap,
    actionSet: mergedActionSet,
    actionSetIcons: actionSetIcons,
    options: calculatedOptions
}}>
```

Access in components:
```typescript
import { RIAContext } from '@mae/components/ria-components/ria-context';

const { map, actionSet, options } = React.useContext(RIAContext);
```

### RIAClientContext (app-specific)

Provides API client and configuration identifiers:

```typescript
type RIAClientContextType = {
    appId: string;
    mapViewId: string;
    mappClient: MAppClient;
    mapConfig: IMapConfig;
};
```

## Action Sets

Action sets define which features are enabled for a map view. Features are conditionally rendered:

```typescript
const { options } = React.useContext(RIAContext);

{options.layertree && <LayerTree />}
{options.bookmarks && <BookmarksControl />}
{options.createedit && <CreateEditHandler />}
```

## RuntimeMap

`RuntimeMap` from `@mae/luciadria-utils` is the core map abstraction:

```typescript
import { RuntimeMap } from '@mae/luciadria-utils/map/runtime-map';

const mapInstance = new RuntimeMap(
    parentNode,           // DOM element
    mapConfig,            // IMapConfig from server
    progressCallback,     // Optional loading progress
    { usePanelSupport: true }
);
```

## Script Attachments

BrowserMap supports script and style attachments:

```typescript
import { processAttachments } from './app/utils/util';
processAttachments(mapInstance, config.runtime, config.attachments);
```

## Bookmarks System

Managed via React Query:

```typescript
import { useGetBookmarks, useDeleteBookmark } from './bookmarks-queries';
const bookmarks = useGetBookmarks();
```

## UI Components

### RIA Menu Tabs (in `ria-client.tsx`)

| Tab | Component | Condition |
|-----|-----------|-----------|
| LayerTree | `@mae/ria-components/layer/layer-tree-control` | `options.layertree` |
| Bookmarks | `BookmarksControl` (local) | `options.bookmarks` |
| Camera | `CameraPanelContent` | `options.camera` |
| Measurement | `MeasurementPanelContent` | `options.measurement` |
| Effects | `EffectsPanelContent` | `options.effects` |
| Create/Edit | `CreateEditPanelContent` | `options.createedit` |

## Key Dependencies

| Package | Purpose |
|---------|---------|
| `@luciad/ria` | LuciadRIA 2D/3D mapping library |
| `@mae/luciadria-utils` | RuntimeMap, map utilities |
| `@mae/ria-components` | Map-specific React components |
| `@mae/components` | RIAContext, RIA menu, theme |
| `@mae/app-engine-api` | Script transpiler and message broker |
| `@tanstack/react-query` | Server state for bookmarks |

## Build Commands

```bash
pnpm --filter @mae/browsermap watch  # Development
pnpm --filter @mae/browsermap build  # Production
pnpm --filter @mae/browsermap eslint # Lint
```

## Common Pitfalls

1. **Context Order**: RIAContext must wrap all components that need map access
2. **Action Set Null Check**: Always check `options.feature` before rendering
3. **LuciadRIA Imports**: Use specific imports to avoid bundle bloat:
   ```typescript
   // Good
   import { Map } from '@luciad/ria/view/Map';
   // Bad
   import { Map } from '@luciad/ria';
   ```
