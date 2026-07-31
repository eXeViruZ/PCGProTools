# 03 — Templates

PCG Pro Tools v2.0.0 includes **23 graph templates** in `Plugins/PCGProKit Content/Templates/`.

Templates can be added through the Template Library or opened directly from plugin content.

> Do not edit shipped template assets in place. Use **Create Editable Copy** to copy a template into `/Game/PCG/`.

---

## Template Library behavior

### Search

Search is case-insensitive and matches the asset name.

### Categories

Categories are assigned by the Template Library from asset-name substrings:

- Scatter
- Filter
- Spline
- Landscape
- Water
- Utility

Because this is name-based, some categories describe library organization rather than the exact node class category.

### Add to Level

Add to Level creates an `APCGVolume` at the camera look point and assigns the selected graph. Default volume bounds are approximately `20000 × 20000 × 5000`.

Landscape-dependent templates verify that a Landscape exists before setup.

---

## Quick reference

| Template | Library category | Demo map | Requirements | Add to Level | Generation |
|---|---|---|---|---|---|
| `PCGT_BiomeMask` | Landscape | `Demo_BiomeMask` | Landscape | PCGVolume | On Load |
| `PCGT_BiomeTransition` | Landscape | `Demo_BiomeTransition` | Landscape | PCGVolume | On Load |
| `PCGT_BoundaryDetect` | Spline | `Demo_BoundaryDetect` | Landscape | PCGVolume | On Load |
| `PCGT_ClumpScatter` | Scatter | `Demo_ClumpScatter` | Landscape | PCGVolume | On Load |
| `PCGT_CurvatureFilter` | Filter | `Demo_CurvatureFilter` | Landscape | PCGVolume | On Load |
| `PCGT_DistanceLOD` | Landscape | `Demo_DistanceLOD` | Landscape | PCGVolume | On Load |
| `PCGT_DistanceTag` | Landscape | `Demo_DistanceTag` | Landscape, POI actors tagged `POI` | PCGVolume | On Load |
| `PCGT_ForestSetup` | Landscape | `Demo_ForestSetup` | Landscape | PCGVolume | On Load |
| `PCGT_GridSnap` | Scatter | `Demo_GridSnap` | Landscape | PCGVolume | On Load |
| `PCGT_HillsideVegetation` | Landscape | `Demo_HillsideVegetation` | Landscape | PCGVolume | On Load |
| `PCGT_InstanceVariation` | Utility | `Demo_InstanceVariation` | Landscape | PCGVolume | On Load |
| `PCGT_LandscapeLayerSampler` | Landscape | `Demo_LandscapeLayerSampler` | Painted Landscape layer | PCGVolume | On Load |
| `PCGT_NaturalForestScatter` | Scatter | — | Landscape | PCGVolume | On Load |
| `PCGT_NoiseMaskFilter` | Filter | `Demo_NoiseMaskFilter` | Landscape | PCGVolume | On Load |
| `PCGT_PrintStats` | Utility | — | None | PCGVolume | On Load |
| `PCGT_RandomSubset` | Utility | `Demo_RandomSubset` | Landscape | PCGVolume | On Load |
| `PCGT_RelaxPoints` | Utility | `Demo_RelaxPoints` | Landscape | PCGVolume | On Load |
| `PCGT_RoadsideGenerator` | Spline | `Demo_RoadsideGenerator` | Spline actor tagged `Road` | RoadSpline + PCGVolume | On Demand |
| `PCGT_SplineAvoidance` | Spline | `Demo_SplineAvoidance` | Spline | AvoidanceSpline + PCGVolume | On Demand |
| `PCGT_SplineOffset` | Spline | `Demo_SplineOffset` | Spline-sampled direction data | PCGVolume | On Demand |
| `PCGT_SplineRoad` | Spline | `Demo_SplineRoad` | Spline actor tagged `Road` | RoadSpline + PCGVolume | On Demand |
| `PCGT_WaterBodyAvoidance` | Water | `Demo_WaterBodyAvoidance` | Water plugin and Water Body actor | PCGVolume | On Demand |
| `PCGT_WeightedSelection` | Utility | `Demo_WeightedSelection` | Landscape | PCGVolume | On Load |

---

## New v2.0.0 templates

### PCGT_InstanceVariation

Applies randomized scale, rotation, and point color before spawning.

**Key node:** Instance Variation

For tree-style yaw-only variation, set Pitch Jitter and Roll Jitter to `0` while keeping Yaw Jitter above `0`.

Point color must be transferred and read by the material/spawner workflow described in [Instance Variation](04_Nodes.md#instance-variation).

---

### PCGT_SplineOffset

Moves spline-sampled points laterally.

**Key node:** Spline Offset

Supports:

- Both sides through `ScatterWidth`
- Left only through `LeftWidth`
- Right only through `RightWidth`
- Center-to-edge density falloff

The node moves existing input points. It does not create new points.

---

### PCGT_DistanceLOD

Reduces point density based on distance to `FixedLocation`.

**Key node:** Distance LOD

The node changes Density only. Add a density-aware filter or spawner behavior when points must be fully removed.

---

### PCGT_RandomSubset

Keeps a deterministic percentage of input points.

**Key node:** Random Subset

`KeepPercentage` is percentage-based. There is no count mode.

---

### PCGT_BiomeMask

Shapes density radially from the PCG volume center and detects transition boundaries from local point neighborhoods.

**Key node:** Biome Mask

Use downstream density filters or separate spawners to turn the generated density ranges into visible biome regions.

This template does not require an external biome actor or shape.

---

### PCGT_RoadsideGenerator

Prepared roadside workflow for vegetation or props along a road spline.

**Add to Level creates:**

- A helper `RoadSpline`
- A PCGVolume
- Required `Road` tag setup

**Key nodes:** Spline Offset, Spline Avoidance, Static Mesh Spawner

**Generation:** On Demand  
**Landscape required:** No

Included presets:

- Roadside Highway
- Roadside Avenue
- Roadside Natural

#### Example mesh

The default spawner uses the example tree mesh:

```text
/PCG/SampleContent/SimpleForest/Meshes/PCG_Tree_01
```

This asset is provided through Unreal Engine's required built-in PCG plugin and is used for demonstration. Replace it with a mesh from your own project for production use.

---

## Existing templates

### PCGT_BiomeTransition

Multi-node biome transition workflow. Updated in v2.0.0 to match the current biome setup.

### PCGT_BoundaryDetect

Marks edge points using the `bIsBoundary` attribute for border-specific placement.

### PCGT_ClumpScatter

Creates organic child-point groups from sparse source points.

### PCGT_CurvatureFilter

Filters points by terrain slope.

**Public node name:** PCG Pro: Curvature Filter  
**C++ class:** `UPCGCurvatureFilterSettings`

### PCGT_DistanceTag

Measures point distance to externally selected POI actors.

The v2 version uses Get Actor Data with actor tag `POI`, multi-selection, target tagging, and corrected inside filtering.

### PCGT_ForestSetup

Full forest pipeline combining spacing, slope, noise, and clump controls.

### PCGT_GridSnap

Snaps placement and optional yaw to a world grid.

### PCGT_HillsideVegetation

Combines slope, height, and Landscape projection for terrain bands.

### PCGT_LandscapeLayerSampler

Drives editor-time placement from a painted Landscape layer.

> Landscape Layer Sampler is not a runtime filter. In non-editor builds it passes points through unchanged.

### PCGT_NaturalForestScatter

Lighter natural-scatter workflow based on spacing and relaxation.

### PCGT_NoiseMaskFilter

Creates clearings and organic coverage through a noise threshold.

### PCGT_PrintStats

Shows Print Stats nodes as pipeline checkpoints.

### PCGT_RelaxPoints

Demonstrates iterative neighbor-based point relaxation.

### PCGT_SplineAvoidance

Clears or attenuates points around a helper spline.

### PCGT_SplineRoad

Roadside alignment and avoidance workflow using a helper spline tagged `Road`.

### PCGT_WaterBodyAvoidance

Clears or keeps points near `AWaterBody` splines. Requires the Water plugin in the consuming project.

### PCGT_WeightedSelection

Selects from tagged input datasets using relative weights.

---

## Create Editable Copy

1. Open Template Library.
2. Open the template context menu.
3. Select **Copy**.
4. The plugin creates a copy under `/Game/PCG/`.
5. The graph opens automatically.

Naming is deduplicated with `_Copy`, `_Copy2`, `_Copy3`, and so on.

The operation is not undoable through the editor transaction system.

---

Next: [04 — Nodes](04_Nodes.md)
