# 03 — Templates

PCG Pro Tools v2.0.0 includes **23 graph templates** in `Plugins/PCGProKit Content/Templates/`.

Templates are ready-made PCG graphs that demonstrate individual nodes or complete workflows. Add them through the Template Library, or inspect them directly in plugin content.

> Do not edit shipped template assets in place. Use **Create Editable Copy** to copy a template into `/Game/PCG/` before structural changes.

---

## Template Library behavior

### Search

Search is case-insensitive and matches the template asset name.

### Categories

The Template Library assigns categories from asset-name substrings:

- Scatter
- Filter
- Spline
- Landscape
- Water
- Utility

Because categorization is name-based, a library category may describe navigation rather than the exact category of every node inside the graph.

### Add to Level

**Add to Level** creates an `APCGVolume` at the camera look point, assigns the selected graph, and applies the template's generation mode.

Default volume bounds are approximately:

```text
20000 × 20000 × 5000
```

Landscape-dependent templates verify that a Landscape actor exists before setup. Selected spline workflows create helper actors; other templates expect the required actors or data to exist already.

### Create Editable Copy

The template context-menu **Copy** action:

- Copies the graph to `/Game/PCG/`
- Uses `_Copy`, `_Copy2`, `_Copy3`, and later suffixes to avoid name collisions
- Opens the copied graph automatically
- Does not support Ctrl+Z because it creates a new asset

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
| `PCGT_DistanceTag` | Landscape | `Demo_DistanceTag` | Landscape and actors tagged `POI` | PCGVolume | On Load |
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
| `PCGT_RoadsideGenerator` | Spline | `Demo_RoadsideGenerator` | Helper spline tagged `Road` | RoadSpline + PCGVolume | On Demand |
| `PCGT_SplineAvoidance` | Spline | `Demo_SplineAvoidance` | Helper spline | AvoidanceSpline + PCGVolume | On Demand |
| `PCGT_SplineOffset` | Spline | `Demo_SplineOffset` | Spline-sampled point directions | PCGVolume | On Demand |
| `PCGT_SplineRoad` | Spline | `Demo_SplineRoad` | Helper spline tagged `Road` | RoadSpline + PCGVolume | On Demand |
| `PCGT_WaterBodyAvoidance` | Water | `Demo_WaterBodyAvoidance` | Water plugin and Water Body actor | PCGVolume | On Demand |
| `PCGT_WeightedSelection` | Utility | `Demo_WeightedSelection` | Landscape | PCGVolume | On Load |

---

# New v2.0.0 templates

## PCGT_InstanceVariation

**Demo map:** `Demo_InstanceVariation`  
**Key node:** Instance Variation  
**Requires:** Landscape

Applies controlled variation before the Static Mesh Spawner:

- Uniform or independent-axis scale
- Yaw, pitch, and roll jitter
- Point Color generated from configurable HSV ranges

For tree-style yaw-only rotation, use:

```text
YawJitter > 0
PitchJitter = 0
RollJitter = 0
```

Point Color must be transferred by the Static Mesh Spawner and consumed by the material. See [Instance Variation](04_Nodes.md#instance-variation).

---

## PCGT_SplineOffset

**Demo map:** `Demo_SplineOffset`  
**Key node:** Spline Offset  
**Requires:** Point directions produced by a spline-sampling workflow  
**Generation:** On Demand

Moves existing points laterally relative to their forward direction.

Supported modes:

- **Both** — uses `ScatterWidth`
- **Left Only** — uses `LeftWidth`
- **Right Only** — uses `RightWidth`

The node also supports randomized offset and center-to-edge density falloff.

> Add to Level creates the PCGVolume but does not create a helper spline for this template. Use the demo map as a reference or provide the required spline-sampled point data in your own setup.

---

## PCGT_DistanceLOD

**Demo map:** `Demo_DistanceLOD`  
**Key node:** Distance LOD  
**Requires:** Landscape

Reduces point Density based on distance to `FixedLocation`.

- Before `NearDistance`: Density remains unchanged
- Between Near and Far: Density interpolates toward `FarDensity`
- Beyond `FarDistance`: Density equals `FarDensity`

Distance LOD does not delete points. Add a Density Filter or another density-aware downstream stage when distant points must be removed completely.

---

## PCGT_RandomSubset

**Demo map:** `Demo_RandomSubset`  
**Key node:** Random Subset  
**Requires:** Landscape

Keeps a deterministic percentage of each input point dataset.

`KeepPercentage` is percentage-based. There is no fixed-count mode. Optional density scaling can reduce surviving point Density by the selected percentage.

---

## PCGT_BiomeMask

**Demo map:** `Demo_BiomeMask`  
**Key node:** Biome Mask  
**Requires:** Landscape

Shapes point Density radially from the PCGVolume center and detects transition zones from local point neighborhoods.

The template:

- Does not use an external biome actor, volume, or shape
- Does not delete points
- Supports Linear, SmoothStep, and Inverse falloff
- Is intended to feed downstream Density Filters or separate spawners

Use the generated density bands to create smooth transitions between biome-specific placement stages.

---

## PCGT_RoadsideGenerator

**Demo map:** `Demo_RoadsideGenerator`  
**Key nodes:** Spline Offset, Spline Avoidance, Static Mesh Spawner  
**Generation:** On Demand  
**Landscape required:** No

Prepared workflow for vegetation, rocks, lights, fences, or props along roads and paths.

**Add to Level creates:**

- A helper `RoadSpline`
- Actor Tag `Road`
- A PCGVolume using `PCGT_RoadsideGenerator`

The workflow supports:

- Both-side, left-only, and right-only placement
- Configurable placement widths
- Random lateral offset
- Center-to-edge density falloff
- Road-clearance control through Spline Avoidance

Included presets:

- `Preset_RoadsideHighway`
- `Preset_RoadsideAvenue`
- `Preset_RoadsideNatural`

The default visualization mesh is:

```text
/PCG/SampleContent/SimpleForest/Meshes/PCG_Tree_01
```

It is supplied by Unreal Engine's built-in PCG plugin and can be replaced with any compatible project mesh.

---

# Existing templates

## PCGT_BiomeTransition

**Demo map:** `Demo_BiomeTransition`  
**Key nodes:** Noise Mask Filter, Weighted Selection By Tag  
**Requires:** Landscape

Demonstrates a multi-stage biome transition. Noise-based regions and tagged weighted selection split points into different visual sets. The workflow was updated in v2.0.0 to match the current biome setup.

---

## PCGT_BoundaryDetect

**Demo map:** `Demo_BoundaryDetect`  
**Key node:** Boundary Detect  
**Requires:** Landscape

Marks edge points with a `bIsBoundary` bool attribute. Filter that attribute downstream to place border rocks, posts, fences, or other edge-specific assets.

---

## PCGT_ClumpScatter

**Demo map:** `Demo_ClumpScatter`  
**Key nodes:** Blue Noise Scatter, Clump Scatter  
**Requires:** Landscape

Converts a sparse distribution into organic clusters. Each source point becomes a clump center, and child-point scaling can thin the edge of each cluster.

---

## PCGT_CurvatureFilter

**Demo map:** `Demo_CurvatureFilter`  
**Key node:** Curvature Filter  
**Requires:** Landscape

Filters points by terrain slope angle. Typical uses include placing trees on flatter ground and rocks on steeper surfaces.

**Public node name:** PCG Pro: Curvature Filter  
**C++ class:** `UPCGCurvatureFilterSettings`

---

## PCGT_DistanceTag

**Demo map:** `Demo_DistanceTag`  
**Key node:** Distance To Nearest Tag  
**Requires:** Landscape and one or more actors tagged `POI`

Writes the distance from each source point to the nearest externally selected POI point.

The corrected v2 workflow uses:

- Get Actor Data with Actor Tag `POI`
- Select Multiple enabled
- Target data tagged `TargetPoints`
- Correct inside-filter behavior
- Live regeneration when relevant POI actors move or change tags

Use the resulting `NearestTagDistance` attribute to drive filtering, density, scale, or later branches.

---

## PCGT_ForestSetup

**Demo map:** `Demo_ForestSetup`  
**Key nodes:** Blue Noise Scatter, Relax Points, Curvature Filter, Noise Mask Filter, Clump Scatter  
**Requires:** Landscape

Full forest pipeline combining even spacing, relaxation, slope filtering, noise-driven clearings, and natural clumping.

---

## PCGT_GridSnap

**Demo map:** `Demo_GridSnap`  
**Key node:** Grid Snap  
**Requires:** Landscape

Snaps point positions to a world grid and can snap yaw to 90-degree increments. The v2 demo uses the regular Engine Content cube:

```text
/Engine/BasicShapes/Cube
```

No VREditor plugin dependency is required.

---

## PCGT_HillsideVegetation

**Demo map:** `Demo_HillsideVegetation`  
**Key nodes:** Curvature Filter, Height Filter, Project To Landscape  
**Requires:** Landscape

Combines slope and height conditions to place vegetation only inside a selected terrain band. The demo is suitable for showing live adaptation while sculpting the Landscape in the editor.

---

## PCGT_LandscapeLayerSampler

**Demo map:** `Demo_LandscapeLayerSampler`  
**Key node:** Landscape Layer Sampler  
**Requires:** Landscape with a painted layer

Filters and modulates points from a Landscape paint-layer weight. The demo includes its required `LI_Grass` Landscape Layer Info asset inside PCG Pro Tools plugin content.

> The actual layer sampling is editor-only. In packaged non-editor builds the node passes all input points through unchanged.

---

## PCGT_NaturalForestScatter

**Dedicated demo map:** None  
**Key nodes:** Blue Noise Scatter, Relax Points  
**Requires:** Landscape

A lighter forest workflow focused on even, organic spacing without the full `PCGT_ForestSetup` pipeline.

---

## PCGT_NoiseMaskFilter

**Demo map:** `Demo_NoiseMaskFilter`  
**Key node:** Noise Mask Filter  
**Requires:** Landscape

Breaks uniform coverage into organic clearings and denser patches through a 2D noise threshold and soft falloff band.

---

## PCGT_PrintStats

**Dedicated demo map:** None  
**Key node:** Print Stats  
**Requires:** None

Shows how to place labeled Print Stats checkpoints between graph stages. The node passes data through unchanged while reporting selected statistics in editor builds.

---

## PCGT_RelaxPoints

**Demo map:** `Demo_RelaxPoints`  
**Key nodes:** Relax Points, Print Stats  
**Requires:** Landscape

Demonstrates iterative neighbor-based point relaxation. Compare source and result distributions to see points settle into a more even layout.

---

## PCGT_SplineAvoidance

**Demo map:** `Demo_SplineAvoidance`  
**Key node:** Spline Avoidance  
**Generation:** On Demand

Creates an `AvoidanceSpline` helper and clears or attenuates points around it. `FalloffRadius` produces a soft transition outside the hard avoidance radius.

---

## PCGT_SplineRoad

**Demo map:** `Demo_SplineRoad`  
**Key nodes:** Spline Avoidance, Align To Nearest Spline, Density Falloff  
**Generation:** On Demand

Creates a `RoadSpline` helper tagged `Road`. The graph clears the road center, aligns placement to the spline direction, and controls density around the road.

---

## PCGT_WaterBodyAvoidance

**Demo map:** `Demo_WaterBodyAvoidance`  
**Key node:** Water Body Avoidance  
**Requires:** Water plugin and at least one loaded Water Body actor  
**Generation:** On Demand

Removes or attenuates points near `AWaterBody` splines. Invert mode can keep only riverbank or shoreline regions.

The Water plugin is optional for PCG Pro Tools generally, but required for this template to find Water Body actors.

---

## PCGT_WeightedSelection

**Demo map:** `Demo_WeightedSelection`  
**Key node:** Weighted Selection By Tag  
**Requires:** Landscape

Samples from tagged datasets using relative weights. Use it for biome-aware or variation-driven selection where one tagged dataset should appear more often than another.

---

## Example visualization assets

The templates and demo maps use example meshes and materials from Unreal Engine's built-in PCG plugin and regular Engine Content. These assets visualize the workflows and are not a separate Hanke Unreal Tools environment-art pack.

Replace Static Mesh Spawner entries with assets from your own project for production use.

---

Next: [04 — Nodes](04_Nodes.md)
