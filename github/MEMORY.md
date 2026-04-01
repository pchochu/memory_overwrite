# M.App Enterprise — Accumulated Knowledge

> This file stores hard-acquired knowledge from deep research sessions.
> It is read at the start of every Copilot session to provide persistent context.

---

## Dashboard Layer Tree — Ordering Conventions

### API Layer Order vs LuciadRIA Runtime Order

- **Root-level `ILayerTreeConfig.layers`**: Top-to-bottom order (index 0 = highest z-order, visually on top).
- **Theme-level `ITheme.layers`**: Bottom-to-top order (index 0 = lowest z-order, visually on bottom).
- **LuciadRIA `LayerTree.children`**: Painting order — bottom-layer first (index 0 = lowest z-order).

The map factory (`Web/packages/libs/mae-luciadria-utils/map/factory.ts`, `instantiateTreeLayers`) **reverses** root layers before calling `addChild`, but iterates theme layers **without reversal**. This establishes that the API conventions differ between root and theme levels.

### The `reverseLayerOrder` Flag

When computing insertion positions in `findPositionInLayerTree` (`map-view-utilities.ts`), theme layers must be reversed to normalize them to top-to-bottom convention. The parameter `reverseLayerOrder: true` is used in recursive calls into themes. Root-level calls use the default `false`.

### LuciadRIA `addChild` / `moveChild` Semantics

- `addChild(layer, 'top')` → highest z-order (visually on top, last in painting order).
- `addChild(layer, 'bottom')` → lowest z-order (visually at bottom, first painted).
- `addChild(layer, 'above', ref)` → visually above the reference (higher z-order).
- `addChild(layer, 'below', ref)` → visually below the reference (lower z-order).

---

## Dashboard Layer Tree — Anchor-Based Positioning

### How Layer Ordering Works in Dashboards

When a Dashboard loads a map view with Analyzer Layer Selection enabled:

1. The initial map tree is built **without** analyzer-selected layers (they are filtered out by `feature-analyzer-map-factory.ts` via `LayerFactory.filterLayers`).
2. Each analyzer-selected layer is re-added individually as an `AnalyzerFeatureLayer` by `primary-feature-layer.tsx`.
3. The insertion position is computed by `MapViewUtilities.findPositionInLayerTree()`, which uses **anchor-based positioning**: it finds existing sibling layers already in the tree and positions the new layer relative to the closest neighbor.

### Critical: `layerName` Property for Anchor Matching

- `buildExistingLayerOrder` matches tree children using `child['layerName']` against `ILayer.name` from the config.
- `layerName` is a **custom property** (not native LuciadRIA). It is set by:
  - All layer model `createLayerInstance` methods (vector, WMS, WFS, WMTS, etc.) in `mae-luciadria-utils/layer/models/`.
  - `analyzer-feature-layer-factory.ts` (line ~221) when `options.layerConfig` exists.
- If `layerName` is missing on a tree node, the anchor matching fails silently (node gets `orderIndex: -1` and is filtered out).

### Edge Cases

- **First layer inserted into an empty theme group**: No anchors exist → falls back to `'bottom'` position.
- **Non-analyzer layers as anchors**: These are present from the initial map build and DO have `layerName` set → they serve as valid anchors.
- **Unmanaged dashboard layers** (non-legend, e.g., user-added layers): Excluded from anchor matching via `filterChildrenForOrdering` (checks `isUnmanagedDashboardLayer`).
- **Spatial filter layer**: Always excluded from ordering, always moved to top after insertion.
- **Layers not found in any config level**: Fall back to `{ position: 'top' }`.

---

## Dashboard Layer Tree — Key Files and Flow

### Layer Insertion Flow (Dashboard with Analyzer Layers)

1. **Map config fetch**: `luciad-ria-widget.ts` → `getMAppClient().getMapViewConfig(mapViewId)` → stored as `mapViewConfig`.
2. **Initial map build**: `feature-analyzer-map-factory.ts` filters out analyzer layers → `Factory.instantiateTreeLayers()` builds tree with remaining layers/themes.
3. **Analyzer layer re-insertion**: `primary-feature-layer.tsx` → for each enabled analyzer layer:
   - Checks `Factory.isFilteredLayer(mvConfig, vectorDataName)` to determine if MAE-managed.
   - If MAE-managed: calls `MapViewUtilities.findPositionInLayerTree()` → `layerTree.addChild()`.
   - If unmanaged: calls `MapViewUtilities.updateDashboardLayer()` (separate ordering path using `displayOrder`).

### Key Files

| File                                                                                                   | Role                                                                                       |
| ------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------ |
| `Web/packages/apps/mae-dashboard/src/core/utilities/map-view-utilities.ts`                             | Layer ordering logic: `findPositionInLayerTree`, `updateDashboardLayer`, visibility config |
| `Web/packages/apps/mae-dashboard/src/core/widget/extensions/primary-feature/primary-feature-layer.tsx` | Mounts analyzer feature layers into the map, decides managed vs unmanaged path             |
| `Web/packages/libs/mae-luciadria-utils/map/factory.ts`                                                 | `instantiateTreeLayers` — builds initial LuciadRIA layer tree from config                  |
| `Web/packages/libs/mae-luciadria-utils/map/feature-analyzer-map-factory.ts`                            | Filters analyzer layers from initial map, creates map instance                             |
| `Web/packages/libs/mae-luciadria-utils/layer/factory.ts`                                               | `isFilteredLayer` (recursive theme search), `filterLayers`                                 |
| `Web/packages/libs/mae-luciadria-utils/analyzer/feature-layer/analyzer-feature-layer-factory.ts`       | Creates `AnalyzerFeatureLayer` instances, sets `layerName`                                 |
| `Web/packages/libs/mae-luciadria-utils/map/interfaces/layer-tree-config.ts`                            | `ILayerTreeConfig { layers: ILayer[]; themes: ITheme[] }`                                  |

### Key Interfaces

- **`ILayerTreeConfig`** (`mae-luciadria-utils/map/interfaces/layer-tree-config.ts`): `{ layers: ILayer[]; themes: ITheme[] }`
- **`ILayer`** (`mae-client-web/models/clients/layer.ts`): has `name`, `label`, `visible`, `isBasemap`, `type`, `id`, `datasetId`, etc.
- **`ITheme`** (`mae-client-web/models/clients/theme.ts`): has `id`, `name`, `layers: ILayer[]`, `themes: ITheme[]`, `isExpanded`, `isVisible`, `isClosed` — supports arbitrary nesting.
- **`ILayerTreePosition`** (`mae-luciadria-utils/layer/factory.ts`): `{ layerTree, position, positionReference?, label? }`

---

## Dashboard — Unmanaged vs Managed Layer Ordering

### Two Distinct Ordering Paths

1. **Managed layers** (appear in MAE Legend definition):
   - Identified by `Factory.isFilteredLayer(mvConfig, vectorDataName)` returning `true`.
   - Positioned by `findPositionInLayerTree` using the legend config structure.
   - Marked with `options.isMAEFeatureLayer = true`.

2. **Unmanaged layers** (not in legend, dashboard-only):
   - Positioned by `updateDashboardLayer` using `displayOrder` property.
   - Marked with `layer['isUnmanagedDashboardLayer'] = true`.
   - Sorted by `getWidgetsDockedInMapView` (sorted by `displayOrder`, then reversed).
   - Default ordering assigned by `setDefaultFeatureLayerOrdering`: non-legend on top, legend in middle, heatmaps at bottom.

---

## Dashboard — Layer Styling Architecture

### featureColor Modes (stored in `widget.options.featureColor: string`)

| Value        | Behavior                                                                      |
| ------------ | ----------------------------------------------------------------------------- |
| `'none'`     | Uniform color using `polygonStyle.fillColor`                                  |
| `'theme'`    | Color per theme dimension value (requires ThemeChartWidget)                   |
| `'measure'`  | Quantitative color ramp via `FeatureMeasureHelper` + `widget.color`           |
| `'styleset'` | OGC/SE external painter from MAE Map View styleset (EnterpriseConnector only) |

### Internal Styling Properties

- **`widget.opacity`** (0–100 integer): Master opacity. Applied at mount time:
  - `polygonStyle.fillOpacity = opacity / 100` (0–1 scale ✓)
  - `lineStyle.opacity = opacity` (stored as 0–100, **not actively used in paintBody**)
  - `pointStyle.opacity = opacity` (stored as 0–100, **not actively used in icon drawing**)
- **`polygonStyle`** (`IPolygonStyle`): `fillColor`, `fillOpacity` (0–1), `clearColor/Opacity`, `focusOpacity`, `outlineColor/Opacity`, `noValueColor`, `autosetOpacity` (stored but not read by current painter)
- **`lineStyle`** (`ILineStyle`): `width` (string), `isMeters`, `opacity` (stored only)
- **`pointStyle`** (`IPointStyle`): `pointSize`, `isMeters`, `displayEmphasis`, `opacity` (stored only), `maxMeters`

### External Styleset Flow

1. `LuciadRiaWidget.getStyleSet()` → `FeatureAnalyzerClient.getMapViewStyleSet(mapViewId)` → `connector.getStyleSet(styleSetName)` → cached in module-level `_styleSet[mapViewId]`.
2. `getStyleSetSymbols(styleSetName)` fetches symbol images (base64); cached in `_styleSetSymbolsCache`.
3. Both set on dc-luciad-ria chart **unconditionally** at mount time, regardless of `featureColor`.
4. `createAnalyzerPainter()`: only if `_featureColor === 'styleset' && _styleSet` does the styleset become an OGC/SE `FeaturePainter` via `createPainterFromString()`.
5. `layerSupportsCustomDisabledStyle(vectorLayerName)`: checks styleset XML for `fa_disabled` property → if found, styleset painter also renders disabled features.

### Style Precedence in `paintBody` (AnalyzerFeaturePainter)

1. External painter present + feature enabled → **external painter only** (all internal styles ignored).
2. External painter present + `useCustomDisabledPainter` + feature disabled → **external painter only**.
3. Otherwise → internal painter: `polygonStyle`, `lineStyle`, `pointStyle`.

### Hardware Painter Does Not Support Styleset

- `useHardwarePainter = true` → `AnalyzerParameterizedPointPainter` (GPU). **ExternalPainter (styleset) is ignored**. No warning shown.
- `useHardwarePainter = false` → `AnalyzerFeaturePainter` (CPU). Full styleset support.

### Styleset Cache Never Invalidated

- Module-level `_styleSet[mapViewId]` and `_styleSetXml[mapViewId]` persist for entire page session. Styleset changes on server are invisible until page reload.

### featureColor Change Does Not Update Painter Without Reload

- Config UI `optionsUpdated('featureColor', value)` only calls `forceUpdate()` on the config component.
- The dc-luciad-ria chart's `_featureColor` closure is **not updated**.
- Visual effect only happens if the layer is fully remounted (widget.reload() or stage reload).

---

## Dashboard — State Management Patterns

### Widget and Stage Model Architecture

- `Analyzer.getAllStageModels()` returns all stage models with states: `OK`, `LOADING`, etc.
- Each stage model has `features` (widgets). Widgets are docked to map views via `dock` matching `mapView.groupKey` or `mapView.title`.
- Widget ordering: `displayOrder` property, sorted ascending then reversed (highest displayOrder = top of map).
- LuciadRIA widgets accessed via `Analyzer.widgets.getWidgetInstanceByType('luciad')`.
