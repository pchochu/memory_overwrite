---
applyTo: Web/packages/libs/mae-styleeditor/**
---
# M.App Enterprise StyleEditor

`mae-styleeditor` is a **React-based style authoring library** for M.App Enterprise. It provides an interactive UI for designing and editing geospatial feature styles using OGC Symbology Encoding (SE) standards, with live LuciadRIA map preview.

**Entry Point:** `src/style-editor.ts` (StyleEditor class)  
**Build:** Rollup bundler via `rollup.config.js`  
**Package Name:** `@mae/styleeditor`

## Architecture

```
StyleEditor (main class)
        ↓
    StyleEditorComponent (React root)
        ↓
┌───────────────────────────────────────────────────────────────────┐
│  NavigationPanel          │  RuleComponent / SymbolizerComponent  │
│  - StyleNavigator         │  - Rule editing                       │
│  - RuleNavigator          │  - Symbolizer editing                 │
│  - Layer visibility       │  - Filter configuration               │
└───────────────────────────────────────────────────────────────────┘
        ↓
    RIA Map Preview (LuciadRIA WebGLMap)
```

### Key Components

| Component | Location | Purpose |
|-----------|----------|---------|
| `StyleEditor` | `src/style-editor.ts` | Main entry class, handles initialization and rendering |
| `StyleEditorComponent` | `src/components/style-editor.tsx` | Root React component with state management |
| `NavigationPanelComponent` | `src/components/navigation-panel/` | Style/rule tree navigation sidebar |
| `RuleComponent` | `src/components/rule-component/` | Rule editing (name, filter, scale) |
| `SymbolizerComponent` | `src/components/symbolizer-component/` | Symbolizer property editing |
| `RuleNavigatorComponent` | `src/components/rule-navigator/` | Rule list with drag-and-drop |
| `StyleNavigatorComponent` | `src/components/style-navigator/` | Style/layer list navigation |

## Core Concepts

| Concept | Description |
|---------|-------------|
| **Style** | OGC SE Style object from `@mae/symbologyencoding` |
| **Rule** | Styling rule with filter, scale constraints, and symbolizers |
| **Symbolizer** | Visual representation (Point, Line, Polygon, Text) |
| **StyleSet** | Dictionary mapping layer names to style XML strings |
| **DataSource** | Adapter for loading/saving styles and layer metadata |
| **MapSource** | Adapter for LuciadRIA map and feature layer creation |

## Initialization

```typescript
import { StyleEditor } from '@mae/styleeditor';

const styleEditor = new StyleEditor({
    mapSource: {
        getMap: (container, options) => { /* return WebGLMap instance */ },
        getFeatureLayer: (map, layer) => { /* return FeatureLayer */ },
        getFeaturePainter: (layer, style) => { /* return IFeaturePainterResult */ }
    },
    dataSource: {
        getStyleSet: () => Promise<IStyleSet>,
        saveStyleSet: (styleSet) => Promise<boolean>,
        getVectorLayers: () => Promise<IStyleEditorLayer[]>,
        getSymbols: () => Promise<SymbolList>,
        addSymbol: (file) => Promise<void>,
        deleteSymbol: (fileName) => Promise<void>,
        getFonts: () => Promise<string[]>,
        getPropertyNames: (layerName) => Promise<IPropertyNames>,
        // Optional methods for wizards
        getPropertyValues: (layerName, propertyName, filter, isString) => Promise<string[]>,
        getMostFrequentPropertyValues: (layerName, propertyName, filter, isString) => Promise<string[]>,
        getMinMaxPropertyValues: (layerName, propertyName, filter, isString) => Promise<string[]>
    },
    title: 'Style Editor',
    debug: false,  // Enable XML view panel
    gridLayer: true
}, '#container-element');

// Wait for initialization
await styleEditor.Ready;
styleEditor.render();
```

## Configuration Interfaces

### IStyleEditorOptions

```typescript
interface IStyleEditorOptions {
    mapSource?: IMapSourceOptions;
    dataSource: IDataSourceOptions;
    editor?: IEditorOptions;
    debug?: boolean;           // Enable XML view
    title?: string;
    gridLayer?: boolean;       // Show grid layer (default: true)
    storageId?: string;        // LocalStorage key for branding
    i18n?: I18NInitOptions;    // i18next configuration
}
```

### IEditorOptions

```typescript
interface IEditorOptions {
    navigationPanelWidth?: number;
    editorPanelWidth?: number;
    menu?: boolean | IMenuOptions;
}

interface IMenuOptions {
    logo?: boolean;
    view?: boolean;
    generate?: boolean;
    background?: boolean;
    save?: boolean;
}
```

## View Panels

The editor supports two panel types controlled via `PanelType` enum:

```typescript
import { PanelType } from '@mae/styleeditor';

// Switch to XML view
styleEditor.togglePanelType(PanelType.Xml);

// Switch to map view
styleEditor.togglePanelType(PanelType.Map);
```

| Panel | Description |
|-------|-------------|
| `PanelType.Map` | Live LuciadRIA map preview with styled features |
| `PanelType.Xml` | Monaco editor showing raw SE XML |

## Style Generation Wizards

Built-in wizards for automatic rule generation:

```typescript
import { WizardType } from '@mae/styleeditor';

// Open classify wizard
styleEditor.toggleWizardType(WizardType.Classify);

// Open categorize wizard
styleEditor.toggleWizardType(WizardType.Categorize);
```

| Wizard | Purpose |
|--------|---------|
| `Classify` | Generate rules based on numeric value ranges |
| `Categorize` | Generate rules based on unique property values |

## Utility Classes

### Branding

Customize primary/secondary colors:

```typescript
import { Branding } from '@mae/styleeditor';

Branding.updateBranding('#0078d4', '#ffffff', false);
```

### Theming

Apply light/dark themes:

```typescript
import { Theming } from '@mae/styleeditor';

Theming.appendThemeFile(
    ['path/to/light-theme.css'],
    ['path/to/dark-theme.css'],
    '/assets/'
);
```

## Dependencies

The StyleEditor relies on these internal libraries:

| Package | Purpose |
|---------|---------|
| `@mae/symbologyencoding` | OGC SE parsing and serialization |
| `@mae/filterencoding` | OGC Filter encoding for rule filters |
| `@mae/luciadria-utils` | LuciadRIA map utilities |
| `@mae/localization` | i18next-based localization |
| `@mae/components` | MAE theme provider |
| `@mae/icons` | FontAwesome icon wrappers |
| `@mae/utils` | Utility functions (TokenUtils, etc.) |

## State Management

The `StyleEditorComponent` uses React component state with Immer for immutable updates:

```typescript
interface IState {
    styles: IStyleDictionary;              // All styles keyed by layer name
    activeStyle?: { key: string; style: Style };
    activeRule?: Rule;
    activeSymbolizer?: Symbolizer;
    panelType: PanelType;                  // Map or Xml view
    wizard: WizardType;                    // Active wizard
    vectorLayerProps: IStyleEditorExtendedLayerDictionary;
    // ... additional state
}
```

## Localization

Translations are under `src/locales/` and use the `Apps.StyleEditor` namespace:

```typescript
import { withTranslation } from '@mae/localization';

// In component
const { t } = this.props;
t('Apps.StyleEditor.someKey');
```

## Build Commands

```bash
# Development build with watch
pnpm --filter @mae/styleeditor watch

# Production build
pnpm --filter @mae/styleeditor build:prod:manual

# Lint
pnpm --filter @mae/styleeditor eslint
pnpm --filter @mae/styleeditor eslint:fix
```

## File Organization

```
src/
├── style-editor.ts          # Main StyleEditor class
├── index.ts                 # Public exports
├── context.ts               # React contexts
├── enums/                   # PanelType, WizardType, etc.
├── interfaces/              # TypeScript interfaces
├── options/                 # Default options
├── utils/                   # Branding, Theming, DataSource, MapSource
├── locales/                 # i18n translations
└── components/
    ├── style-editor.tsx     # Root React component
    ├── navigation-panel/    # Layer/style/rule navigation
    ├── rule-component/      # Rule editing
    ├── rule-filter/         # Filter autocomplete
    ├── rule-navigator/      # Rule list
    ├── style-navigator/     # Style list
    ├── symbolizer-component/# Symbolizer editing
    ├── xml-editor/          # Monaco XML editor
    ├── share/               # Shared UI components
    └── utils/               # Wizards, resize, etc.
```

## Best Practices

1. **Use DataSource abstraction** - Never access backend APIs directly; implement `IDataSourceOptions`
2. **Use MapSource abstraction** - Provide custom map/layer creation via `IMapSourceOptions`
3. **Immutable updates** - State changes use Immer's `produce()` function
4. **Type-safe translations** - Use `@mae/localization` withTranslation HOC
5. **CSS Variables** - Use branding CSS variables (`--hxg-primary-*`, `--hxg-secondary-*`)
6. **SE Standards** - Styles follow OGC Symbology Encoding specification via `@mae/symbologyencoding`
