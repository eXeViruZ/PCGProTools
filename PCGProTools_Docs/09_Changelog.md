# 09 — Changelog

---

## v1.1.0

### New nodes (7)

| Node | Class | Description |
|---|---|---|
| Clump Scatter | `UPCGClumpScatterSettings` | Converts each input point into an organic cluster of child points with optional scale falloff |
| Relax Points | `UPCGRelaxPointsSettings` | Lloyd-relaxation for evenly spaced, non-clustered point distributions |
| Noise Mask Filter | `UPCGNoiseMaskFilterSettings` | Perlin-noise mask filter for organic density variation and clearings |
| Spline Avoidance | `UPCGSplineAvoidanceSettings` | Removes or attenuates points near spline actors; supports soft falloff and invert mode |
| Water Body Avoidance | `UPCGWaterBodyAvoidanceSettings` | Removes or attenuates points near UE Water body splines; no hard Water plugin dependency |
| Landscape Layer Sampler | `UPCGLandscapeLayerSamplerSettings` | Filters and modulates points based on Landscape paint-layer weight |
| Print Stats | `UPCGPrintStatsSettings` | Passthrough debug node that logs point count, bounds, Z range, and density to the Output Log |

### Renamed (v1.0 → v1.1)

| Old name | New name | Notes |
|---|---|---|
| `SurfaceSlopeFilter` (node) | **PCG Pro: Slope Filter** | Class `UPCGCurvatureFilterSettings` |
| `PCGT_VillageCorner` (template) | `PCGT_ForestSetup` | Reworked into a full forest pipeline |
| `DemoVillageCorner` (map) | `Demo_ForestSetup` | — |
| `PCGT_GridBuildings` (template) | `PCGT_GridSnap` | — |

> **Breaking change:** Graphs from v1.0 that contained the old `SurfaceSlopeFilter` node will show a broken node after upgrading. Replace the broken node with the new **PCG Pro: Slope Filter** node and re-enter the property values. Level or graph references to `PCGT_VillageCorner` / `DemoVillageCorner` must also be updated.

### New editor tools

- **Debug Overlay** — viewport tick-based overlay that draws bounding boxes around PCG actors. Scope configurable via Project Settings (Selected Only / All In Viewport / All In Level). Uses a delegate-based component cache instead of per-tick world scans.
  - First activation with `AllVisible` or `AllInLevel` scope shows a one-time performance warning dialog. Confirming saves the acknowledgement to config so it never fires again.
- **Template Library** — dockable panel listing all 17 templates. Spawns PCGVolume + helper actors (splines, etc.) with one click.
  - **Refresh** button rescans `/PCGProKit/Templates/` without restarting the editor.
  - Empty-state message shown when no templates are found (e.g. plugin content not visible).
- **Graph Inspector** — dockable panel showing all `PCG_Overridable` node parameters of the selected PCG actor. Includes preset dropdown, Auto-Regenerate toggle, and Save as Preset.
  - **Regenerate Selected** button regenerates **all** currently selected PCG actors at once (bulk regenerate).
  - **Auto-Regenerate** toggle — when enabled, any parameter edit automatically regenerates the primary selected actor.
  - Every parameter edit is wrapped in a named transaction (`PCG Quick Tune: Edit {PropertyName}`) — full Undo/Redo via Ctrl+Z.
- **Actor context menu** — right-clicking a PCG actor in the level now shows **Open in PCG Graph Inspector**.

### New preset system

- `UPCGProKitPresetAsset` — DataAsset storing named property overrides (Float / Int32 / Bool).
- `FPCGProKitPresetEntry` — one override entry targeting a settings class + property name via FProperty reflection.
- **Apply Preset** — writes overrides into matching nodes; operation is undoable.
- **Save as Preset (Snapshot)** — opens a modal name dialog pre-filled with `ActorLabel_Preset`, captures all current `PCG_Overridable` values, saves the asset immediately to `/PCGProKit/Presets/`, and refreshes the dropdown. Shows an error dialog if no overridable parameters are found.
- **Refresh Presets (↻)** — rescans `/PCGProKit/Presets/` and updates the dropdown without reopening the Inspector.
- 4 included presets: Beach Sparse, Forest Dense, Mountain Rocky, Urban Grid.

### New Project Settings

Accessible via **Edit → Project Settings → Plugins → PCG Pro Tools**:

| Setting | Default | Description |
|---|---|---|
| `OverlayScope` | SelectedOnly | Controls which PCG actors the debug overlay draws |
| `OverlayViewportMaxDistance` | 100 000 cm | Draw distance in viewport scope mode |
| `MaxPointsPerComponent` | 5 000 | Max points processed per component for overlay labels |
| `DensityCapWarningThreshold` | 1 000 000 | Editor notification threshold for high point counts. 0 = off |
| `bDeterministicMode` | false | Forces all random nodes to use `DefaultRandomSeed` |
| `DefaultRandomSeed` | 42 | Global seed for deterministic mode |

### New templates (12)

Added on top of the templates carried over from v1.0:

Boundary Detect, Clump Scatter, Curvature Filter, Distance Tag, Grid Snap, Landscape Layer Sampler, Noise Mask Filter, Print Stats, Relax Points, Spline Avoidance, Water Body Avoidance, Weighted Selection

*Carried over from v1.0:* Natural Forest Scatter, Biome Transition, Hillside Vegetation, Spline Road.

### Demo maps reorganized (15 total)

The demo map set was reorganized and expanded from 5 maps in v1.0 to **15 maps** in v1.1. Each map showcases a specific feature or pipeline; some maps combine multiple nodes to demonstrate a complete workflow rather than a single node in isolation.

Demo_BiomeTransition, Demo_BoundaryDetect, Demo_ClumpScatter, Demo_CurvatureFilter, Demo_DistanceTag, Demo_ForestSetup, Demo_GridSnap, Demo_HillsideVegetation, Demo_LandscapeLayerSampler, Demo_NoiseMaskFilter, Demo_RelaxPoints, Demo_SplineAvoidance, Demo_SplineRoad, Demo_WaterBodyAvoidance, Demo_WeightedSelection

### Bug fixes

- **Landscape Layer Sampler** now samples paint-layer weight across **all** landscape streaming proxies. Previously it only used the first proxy found, so points over other proxies were silently dropped in World Partition levels. Added an optional `LandscapeRef` property to explicitly target a landscape in multi-landscape levels.
- **Save as Preset** now saves user presets into the project (`/Game`) via the standard Content Browser dialog instead of into plugin content. Presets created in v1.0/early-v1.1 builds were written to plugin content, where they could be lost on plugin update/re-install. Preset discovery now scans both the plugin's shipped presets and the project.
- **Spline Avoidance, Align To Nearest Spline, Water Body Avoidance, and Landscape Layer Sampler** now force game-thread execution (`CanExecuteOnlyOnMainThread`). Previously they iterated world actors / read spline & landscape data on PCG worker threads, which could cause intermittent crashes or data races during regeneration. Matches Epic's convention for actor-touching PCG elements. Results are unchanged.

### Other changes

- All spline nodes (`SplineAvoidance`, `AlignToNearestSpline`) support `SplineActorTag` for actor discovery in addition to explicit `SplineActors` references. (These nodes now run on the game thread, so discovery is reliable in-editor; explicit references are still recommended for cooked builds.)
- All filter nodes with invert behaviour now expose a consistent `bInvertSelection` / `bInvertFilter` / `bInvertMask` property.
- Soft falloff (`FalloffRadius` / `FalloffAngle` / `FalloffWidth`) added to Spline Avoidance, Water Body Avoidance, Slope Filter, and Noise Mask Filter.
- `UPCGProKitSettings::CheckDensityCap` and `CheckDensityCapFromContext` are now public static helpers callable from custom node code.

---

## v1.0.0

Initial release.

**10 nodes:** Blue Noise Scatter, Boundary Detect, Density Falloff, Distance To Nearest Tag, Grid Snap, Height Filter, Project To Landscape, Align To Nearest Spline, Slope Filter (then called SurfaceSlopeFilter), Weighted Selection By Tag.

**6 templates:** Village Corner, Grid Buildings, Natural Forest Scatter, Biome Transition, Hillside Vegetation, Spline Road.

**4 presets:** Beach Sparse, Forest Dense, Mountain Rocky, Urban Grid.

**5 demo maps:** one per original template.

**Platforms:** Win64 validated.
