---
applyTo: Web/packages/apps/mae-studio/**
---
# M.App Enterprise Studio

`mae-studio` is the **content authoring and administration tool** for M.App Enterprise. It provides UI for managing maps, datasets, styles, workflows, geoservices, and client configurations.

**Entry Point:** `src/index.tsx` → `src/App.tsx`  
**Build:** Built via `@mae/tasks` (gulp + webpack)  
**Package Name:** `@mae/studio`

## Architecture

```
StudioApplicationMae2 (singleton)
        ↓
    React Router (Hash-based)
        ↓
    Layout Component (sidebar + content area)
        ↓
    Section Routes (apps, content, browser, mobile, desktop, security, cache)
```

### Key Components

| Component | Location | Purpose |
|-----------|----------|---------|
| `StudioApplicationMae2` | `@mae/mvc-studio` | Singleton application manager |
| `Layout` | `@mae/mvc-studio/layout-mae` | Main layout with sidebar |
| `RouterUtils` | `@mae/mvc-studio/utils/router-tools` | Navigation helper |
| `App` | `src/App.tsx` | Root component with routing |

## Routing System

Studio uses **React Router 6** with hash-based routing (`createHashRouter`):

```tsx
const router = createHashRouter([
    {
        path: '/',
        element: <InitializationWrapper studio={studioApplicationSingleton} />,
        children: [
            { path: '', element: <Navigate to="apps" replace /> },
            { path: MAPPS_PARENT_PATH, element: <Layout />, children: mAppSection },
            { path: CONTENT_PARENT_PATH, element: <Layout />, children: contentSection },
            // ... more sections
        ]
    }
]);
```

### Section Pattern

```typescript
// Simple section
export const { menuNavigationData, sectionRouting, PARENT_PATH } = createSectionRouting({
    name: 'License.MApps',
    path: 'apps',
    icon: maeThLargeLight,
    Default: MAppStatsList
});

// Section with subsections
export const { menuNavigationData, sectionRouting, PARENT_PATH } = createSectionWithSubsectionsRouting({
    name: 'Studio.Content',
    subSections: contentRoutes,
    path: 'content',
    icon: maeContent
});
```

## Main Sections

### Content Section (`/content/*`)

| Path | Purpose |
|------|---------|
| `/content/imagery` | Imagery datasets |
| `/content/vector-data` | Vector datasets |
| `/content/vector-sets` | Vector sets |
| `/content/queries` | Saved queries |
| `/content/geoservices` | External geoservices |
| `/content/legends` | Map legends |
| `/content/stylesets` | Style definitions |
| `/content/workflows` | Workflow definitions |

### Client Sections

| Section | Path | Purpose |
|---------|------|---------|
| Desktop | `/desktop/*` | Desktop client configs |
| Browser | `/browser/*` | Browser client configs |
| Mobile | `/mobile/*` | Mobile client configs |

## StudioApplicationMae2

```typescript
import studioApplicationSingleton from '@mae/mvc-studio/studio-application-mae2';

// Initialization
useEffect(() => {
    const options = {
        storageId: 'studio_storage',
        clientId: 'Studio',
        resetPassword: true
    };
    studio.addRoutesMetadata(mAppMetadata);
    studio.initialize(options);
}, []);
```

## SubSection Pattern

```typescript
import { SubSection } from '@mae/mvc-studio/utils';

const IMAGERY: SubSection = {
    name: 'Studio.Content.Imagery',
    path: 'imagery',
    icon: maeImageryIcon,
    List: ImageryList,
    Edit: ImageryEdit,
    Create: ImageryCreate
};
```

## Authentication Routes

| Route | Component | Purpose |
|-------|-----------|---------|
| `/login` | `LoginMae` | Login form |
| `/token` | `Token2` | Token auth |
| `/blocked` | `Blocked2` | Blocked account |
| `/activation/:token` | `Activation2` | Account activation |

## Key Dependencies

| Package | Purpose |
|---------|---------|
| `react-router-dom` | React Router 6 |
| `@mae/mvc-studio` | Shared Studio infrastructure |
| `@mae/localization` | i18next localization |
| `@mae/mui-components` | MUI form/table components |
| `knockout` | Legacy Knockout.js (some views) |
| `mobx` | State management |

## Build

Studio is built via `@mae/tasks`:

```bash
pnpm --filter @mae/tasks build  # Development
pnpm --filter @mae/tasks watch  # Watch mode
pnpm --filter @mae/studio eslint # Lint
```

## Adding New Content Section

1. Create folder in `src/models/content/my-section/`
2. Create `SubSection` definition
3. Add to `contentRoutes` array in `routes.tsx`

## Common Pitfalls

1. **Hash Router**: URLs use hash (`#/content/imagery`)
2. **Section Registration**: Call `studio.addRoutesMetadata()` before `initialize()`
3. **Build Dependency**: Built via `@mae/tasks`, not standalone
4. **Localization Keys**: Section names are i18n keys like `'Studio.Content.Imagery'`
