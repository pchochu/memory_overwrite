---
applyTo: Web/packages/apps/mae-apps/**
---
# M.App Enterprise Apps

The Apps portal - a public-facing launcher for M.App Enterprise applications (Browser, Desktop, Mobile).

**Entry Point:** `src/studio.ts` - Extends `StudioApplication` from `@mae/mvc-studio`  
**Build:** Built via `@mae/tasks`, NOT standalone webpack  
**Package Name:** `@mae/apps`

## Build Commands

```bash
# From Web/ directory
pnpm --filter @mae/tasks build:apps  # Build apps only
pnpm --filter @mae/tasks build       # Build all (Apps, Studio, Management)
```

## Hybrid State Management

This app uses **both legacy Knockout.js and modern React/React Query**:

| Pattern | Usage |
|---------|-------|
| **Knockout.js** | Views, templates, routing (`@mae/mvc`, `@mae/mvc-studio`) |
| **React + MUI** | UI components, list rendering |
| **React Query** | Data fetching (`MAppList` uses `useSelectableDataSource`) |
| **MobX** | Shared state stores |

## Directory Structure

```
src/
├── studio.ts              # Main Studio class extending StudioApplication
├── login.ts               # AppLogin - handles public/private login flow
├── app-client-wrapper.ts  # Wraps AppClient with caching & public app support
├── compile/               # Template exports (extends @mae/mvc-studio)
├── const/                 # Constants, icons, view IDs
├── models/
│   └── mapps/             # MApp list feature
│       ├── section.ts     # MAppSection - Knockout SectionView
│       ├── list-view.tsx  # MAppListView - React wrapper with QueryClient
│       ├── list.tsx       # MAppList - Main React component
│       ├── list-item.tsx  # MAppListItem - Card component
│       └── utils/         # Launcher, dialogs, skeletons
└── studio/
    └── studio-section.tsx # Main authorized section with header
```

## Public vs Private Apps

The app supports both public (no auth) and authenticated access:

```typescript
// app-client-wrapper.ts
export class AppClientWrapper extends AppClient {
  static get IsPublic(): boolean {
    return !!AppClientWrapper.studio?.IsPublic;
  }

  static async getMApps(): Promise<IMAppItem[]> {
    if (AppClientWrapper.IsPublic) {
      return this.hasCache ? this.cache : super.getPublicMApps();
    }
    return super.getMApps();
  }
}
```

## View Layer (Knockout + React Hybrid)

Views extend `@mae/mvc` classes but render React components:

```typescript
// list-view.tsx
export class MAppListView extends ReactView {
  getReactElement(): React.ReactElement {
    return (
      <QueryClientProvider client={this.queryClient}>
        <MAEDefaultThemeProvider>
          <MAppList navigateToLogin={() => this.studio.loginClicked()} />
        </MAEDefaultThemeProvider>
      </QueryClientProvider>
    );
  }
}
```

## Data Fetching with useSelectableDataSource

```typescript
const selectableDataSource = useSelectableDataSource({
  keyField: 'id',
  queryKey: [MAppListQueryKey],
  queryFn: () => AppClientWrapper.getMApps(),
  sort: [{ key: 'name', order: 'ASC' }],
  filter: { appType: { value: [...], operator: 'isIn' } }
});
```

## App Launching

Three app types with different launch mechanisms (`utils/hexagon-app-launcher.tsx`):

| AppType | Launch Method |
|---------|---------------|
| `Browser` | Opens AppEngine URL in new window |
| `Desktop` | Uses `hnlp://` protocol with Hexagon App Launcher |
| `Mobile` | Shows QR code dialog |

## Templates & Compilation

Templates are inherited from `@mae/mvc-studio`:

```typescript
// compile/templates.ts
export const Templates: ITemplate = _.extend({}, Templates_MvcStudio, {});
```

## Dependencies to Understand

- **`@mae/mvc-studio`** - Base `StudioApplication`, `AuthorizedStudioSectionView`, login flow
- **`@mae/mvc`** - Core MVC patterns, `SectionView`, `ReactView`, routing
- **`@mae/studio-components`** - Shared React components
- **`@mae/utils/hooks/use-data-source`** - Data fetching hook with filtering/sorting

## Common Tasks

### Add a New Filter to MApp List
1. Add state in `list.tsx`
2. Add to `selectableDataSource.filter` configuration
3. Add UI control in the `ToolbarContainer`

### Modify MApp Card Layout
Edit `list-item.tsx` - uses MUI styled components:
- `MAPPLISTITEMCLASS` - Base card class
- `HEADLINECLASS` - Title overlay
- `SHAREBUTTONCLASS` - Share action

### Add New App Type
1. Add to `AppType` enum in `@mae/client-web/enums`
2. Add launch logic in `hexagon-app-launcher.tsx`
3. Add filter toggle in `list.tsx`
4. Add icon in `list-item.tsx`
