---
applyTo: Web/packages/apps/mae-management/**
---
# M.App Enterprise Management

`mae-management` is the **system administration interface** for M.App Enterprise. It provides UI for managing tenants, usage metrics, charges, connections, languages, branding, logs, and system settings.

**Entry Point:** `src/index.tsx` → `src/App.tsx`  
**Build:** Built via `@mae/tasks` (gulp + webpack)  
**Package Name:** `@mae/management`

## Architecture

```
StudioApplicationMae2 (singleton - shared with Studio)
        ↓
    React Router (Hash-based)
        ↓
    Layout Component (sidebar + content area)
        ↓
    Admin Section Routes (tenants, usage, charges, connections, etc.)
```

### Key Differences from Studio

| Aspect | Studio | Management |
|--------|--------|------------|
| `clientId` | `'Studio'` | `'Management'` |
| `requireTenant` | `true` | `false` |
| Default route | `/apps` | `/tenants` |
| Sections | Content authoring | System administration |

## Routing System

```tsx
const router = createHashRouter([
    {
        path: '/',
        element: <InitializationWrapper studio={studioApplicationSingleton} />,
        children: [
            { index: true, element: <Navigate to={TENANT_PARENT_PATH} replace /> },
            { path: TENANT_PARENT_PATH, element: <Layout />, children: tenantsSection },
            { path: USAGE_PARENT_PATH, element: <Layout />, children: usageSection },
            { path: CHARGE_PARENT_PATH, element: <Layout />, children: chargesSection },
            // ... more sections
        ]
    }
]);
```

## Main Sections

| Section | Path | Purpose |
|---------|------|---------|
| Tenants | `/tenants` | Multi-tenant management |
| Usage | `/usage` | System usage metrics |
| Charges | `/charges` | Billing management |
| Connections | `/connections` | Data connections |
| Languages | `/languages` | Localization management |
| Branding | `/branding` | System branding |
| Logs | `/logs` | System logging/audit |
| Settings | `/settings` | System configuration |

## Initialization

```typescript
useEffect(() => {
    const options = {
        storageId: 'management_storage',
        clientId: 'Management',
        requireTenant: false  // Key difference from Studio
    };

    studio.addRoutesMetadata(tenantsMetadata);
    studio.addRoutesMetadata(usageMetadata);
    studio.initialize(options);
}, []);
```

## Custom Login Flow

Management overrides login for initial charge setup:

```typescript
const onRouterUtilsInit = useCallback((navigate: NavigateFunction) => {
    studio.initializeNavigation(navigate);
    
    const studioProceedLoginOriginal = studio.proceedLogIn.bind(studio);
    studio.proceedLogIn = (updateUserInfoSuccess: boolean) => {
        if (!updateUserInfoSuccess) {
            navigate('/initialCharge');
        } else {
            studioProceedLoginOriginal(updateUserInfoSuccess);
        }
    };
}, []);
```

## Initial Charge Component

Special onboarding flow:

```tsx
{ path: 'initialCharge', element: <InitialCharge studio={studioApplicationSingleton} /> }
```

## Key Dependencies

| Package | Purpose |
|---------|---------|
| `react-router-dom` | React Router 6 |
| `@mae/mvc-studio` | Shared infrastructure |
| `@mae/localization` | i18next localization |
| `@mae/mui-components` | MUI form/table components |
| `chart.js` | Usage charts |
| `knockout` | Legacy views |
| `mobx` | State management |

## Build

Management is built via `@mae/tasks`:

```bash
pnpm --filter @mae/tasks build  # Development
pnpm --filter @mae/tasks watch  # Watch mode
pnpm --filter @mae/management eslint # Lint
```

## Common Pitfalls

1. **No Tenant Required**: `requireTenant: false` for system-wide admin
2. **Storage ID**: Uses `'management_storage'` not `'studio_storage'`
3. **Build Dependency**: Built via `@mae/tasks`, not standalone
4. **Initial Charge**: Custom login redirects to `/initialCharge` on first use
5. **Shared Singleton**: Same `StudioApplicationMae2` as Studio
