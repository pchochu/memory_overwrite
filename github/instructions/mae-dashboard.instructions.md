---
applyTo: Web/packages/apps/mae-dashboard/**
---
# M.App Enterprise Dashboard

`mae-dashboard` is the **data analysis and visualization tool** for M.App Enterprise. It provides interactive charts, maps, and data tables powered by **Crossfilter** and **DC.js** for cross-filtering analytics, plus **LuciadRIA** for map visualizations.

**Entry Point:** `src/fa-ria-main.js` (LuciadRIA mode) or `src/fa-wp-main.js` (WebGL mode)  
**Build:** Standalone webpack + Grunt via `scripts/`  
**Package Name:** `@mae/dashboard`  
**Alias:** "Analyzer" in code comments and globals

## Core Concepts

| Concept | Description |
|---------|-------------|
| **StageModel** | Crossfilter-powered data container with dimensions and groups |
| **Widget** | Visual component (chart, map, table) bound to StageModel data |
| **Connector** | Data source adapter (Enterprise, ESRI, WFS, Shapefile) |
| **Analyzer API** | Global API for widget management and data operations |

## Data Flow

```
Connector (EnterpriseConnector, WFSConnector, etc.)
        ↓
    DataLoaderCore
        ↓
    StageModel (Crossfilter instance)
        ↓
    Widget (DC.js chart or custom React component)
        ↓
    User interaction → dimension.filter() → dc.redrawAll()
```

## StageModel

`StageModel` (`src/core/stage-model/index.ts`) is the central data management class:

```typescript
class StageModel {
    stageId: string;
    crossfilter: crossfilter.Crossfilter<IAnalyzerField>;
    state: StageState;  // OK, NEW, LOADING, ERROR, UNLOADED, DISPOSING
    widgets: Widget[];
}
```

## Widget System

`Widget` (`src/core/widget/widget.ts`) is the base class for all visualizations:

```typescript
class Widget implements IWidget {
    type: string;
    stageId: string;
    fieldName: string;
    title: string;
    dock: DockType;
    setDimension: (dimension: crossfilter.Dimension) => void;
}
```

### Widget Registry

```typescript
const WidgetClass = Analyzer.widgets.registry.getWidgetClass('barChart');
const widget = createWidget({ type: 'barChart', /* config */ });
```

### Built-in Widget Types

| Type | Class | Description |
|------|-------|-------------|
| `barChart` | `BarChartWidget` | Vertical bar chart |
| `rowChart` | `RowChartWidget` | Horizontal bar chart |
| `pieChart` | `PieChartWidget` | Pie/donut chart |
| `lineChart` | `LineChartWidget` | Line/area chart |
| `dateChart` | `DateLineChartWidget` | Time series chart |
| `scatterChart` | `ScatterChartWidget` | Scatter plot |
| `gaugeChart` | `GaugeChartWidget` | Gauge/meter |
| `heatChart` | `HeatChartWidget` | Heat calendar |
| `dataTableChart` | `DataTableChartWidget` | Paginated table |
| `luciadRia` | `LuciadRiaWidget` | LuciadRIA map |

Widget implementations are in `src/core/widget/extensions/`.

## Connectors

Connectors (`src/core/connectors/`) load data from various sources:

```typescript
const connector = ConnectorFactory.create({
    mode: 'EnterpriseConnector',
    // connector-specific options
});
```

| Mode | Source |
|------|--------|
| `EnterpriseConnector` | M.App Enterprise API |
| `ESRIWebServiceConnector` | ArcGIS services |
| `WFSConnector` | OGC WFS services |
| `ShapeFileConnector` | Shapefile uploads |

## DC.js and Crossfilter

Dashboard uses DC.js 4.x with Crossfilter 2.x:

```typescript
declare const dc: any;

const dimension = stage.crossfilter.dimension((d) => d.category);
const group = dimension.group().reduceCount();

chart.dimension(dimension).group(group).render();

// After filter changes
dc.redrawAll();
```

## Analyzer API

Global `Analyzer` object (`src/core/api/analyzer.ts`):

```typescript
declare const Analyzer: AnalyzerAPI;

Analyzer.widgets.registry.getWidgetClass(type);
Analyzer.createStageModel(options);
Analyzer.getStageModel(stageId);
Analyzer.workflows.execute(workflowId);
```

## Chart Base Component

`ChartBase` (`src/views/widgets/base/chart-base.tsx`) is the React base class:

```tsx
class ChartBase<TProps extends IWidgetBaseProps, TState extends IWidgetBaseState> 
    extends React.Component<TProps, TState> {
    get widget(): Widget { return this.props.data; }
}
```

## Key Dependencies

| Package | Purpose |
|---------|---------|
| `crossfilter2` | Multi-dimensional filtering |
| `dc` | DC.js charting |
| `d3` | D3.js primitives |
| `@luciad/ria` | LuciadRIA mapping |
| `@tinymce/tinymce-react` | HTML widget editor |
| `@turf/turf` | Geospatial operations |

## Build Commands

```bash
pnpm --filter @mae/dashboard build       # Development
pnpm --filter @mae/dashboard build:prod  # Production (grunt)
pnpm --filter @mae/dashboard watch       # Watch mode
```

## Creating New Widget Type

1. Create folder in `src/core/widget/extensions/my-widget/`
2. Implement `MyWidget extends Widget`
3. Create `MyChart extends ChartBase`
4. Register in `WidgetRegistry`

## Common Pitfalls

1. **Global DC Object**: DC.js is globally available, not ES6 imported
2. **Crossfilter NaN**: Never pass NaN values - crashes browsers
3. **Widget Lifecycle**: Call `widget.dispose()` to clean up dimensions
4. **Legacy Code**: Many `.js` files - gradual migration in progress
5. **Chart Redraw**: Always call `dc.redrawAll()` after programmatic filter changes

---

## Feature Layer Styling

### featureColor Mode (`IPrimaryFeatureWidgetOptions.featureColor`)

Controls how the feature layer colors features. Four modes:

| Value | Behavior |
|-------|----------|
| `'none'` | Uniform fill using `polygonStyle.fillColor` |
| `'theme'` | Per-value color from a linked `ThemeChartWidget` |
| `'measure'` | Quantitative color ramp via `FeatureMeasureHelper` + `widget.color` |
| `'styleset'` | OGC/SE external painter from the MAE Map View styleset (requires `EnterpriseConnector`) |

### Internal Styling Properties

Set through the configuration UI and persisted in `widget.options`:

- **`widget.opacity`** (0–100): Master opacity applied at mount time:
  - → `polygonStyle.fillOpacity = opacity / 100` (0–1 scale)
  - → `lineStyle.opacity = opacity` and `pointStyle.opacity = opacity` (stored at 0–100 but **not read in `paintBody`**)
- **`polygonStyle` (`IPolygonStyle`)**: `fillColor`, `fillOpacity` (0–1), `clearColor/Opacity`, `focusOpacity`, `outlineColor/Opacity`, `noValueColor`, `autosetOpacity`
- **`lineStyle` (`ILineStyle`)**: `width` (string, px or meters), `isMeters`, `opacity` (stored, unused)
- **`pointStyle` (`IPointStyle`)**: `pointSize`, `isMeters`, `displayEmphasis`, `opacity` (stored, unused), `maxMeters`

> **Note**: `lineStyle.opacity` and `pointStyle.opacity` are stored on a 0–100 scale but are not applied by `paintBody`. Only `polygonStyle.fillOpacity` drives the actual opacity rendering.

### External Styleset (OGC/SE)

When `featureColor === 'styleset'`, styling is driven by an OGC Symbology Encoding (SE) style fetched from the MAE server:

```typescript
// Fetched once and cached per mapViewId (never invalidated during session)
const styleSet = await mapViewWidget.getStyleSet();
const style = styleSet.styles[stageModel.connector.datasetName];
polyLayer.styleSetSymbols(styleSetSymbols);
polyLayer.styleSet(style.toXml());
```

The styleset is always fetched and stored on the dc chart object, even when `featureColor` is not `'styleset'`. It only becomes the active painter when `featureColor === 'styleset'`.

### Style Precedence in `paintBody` (AnalyzerFeaturePainter)

1. Feature enabled + `externalPainter` (styleset) present → **external painter handles everything** — internal `polygonStyle`, `lineStyle`, `pointStyle` are bypassed entirely.
2. Feature disabled + `useCustomDisabledPainter` true → external painter handles disabled appearance too.
3. Otherwise → internal painter uses `polygonStyle.fillColor/fillOpacity`, `lineStyle.width`, `pointStyle.pointSize`.

### Custom Disabled Painter (`useCustomDisabledPainter`)

`LuciadRiaWidget.layerSupportsCustomDisabledStyle()` inspects the styleset XML for any OGC rule referencing the `fa_disabled` property. If found, the styleset drives both the enabled and disabled (crossfiltered out) feature appearance. If not found, disabled features render as grey with the internal painter.

### Hardware Painter Does NOT Support Styleset

`useHardwarePainter = true` (default for `PrimaryFeatureLayerWidget`) uses `AnalyzerParameterizedPointPainter` (GPU-accelerated). This painter **ignores `externalPainter`** — the styleset has no visual effect. Styleset only works when `useHardwarePainter = false` (software `AnalyzerFeaturePainter`).

### Styleset Cache

Module-level `_styleSet[mapViewId]` and `_styleSetXml[mapViewId]` dictionaries in `luciad-ria-widget.ts` are never cleared during the page session. Styleset changes on the server are invisible until the browser page is reloaded.

### featureColor Change Requires Full Remount

Changing `featureColor` in the configuration UI only updates `widget.options.featureColor`. The dc-luciad-ria chart's internal `_featureColor` closure is not updated. The painter is only recreated when the widget is fully remounted (i.e., the stage is reloaded or `widget.reload()` is explicitly called).
