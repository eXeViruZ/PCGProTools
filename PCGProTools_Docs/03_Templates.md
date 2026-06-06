# 03 — Templates

PCG Pro Tools ships with **17 graph templates** in `Plugins/PCGProKit Content/Templates/`.

Templates are ready-to-use PCG graphs wired up to showcase one or more nodes. Spawn them into your level via the **Template Library** toolbar panel, or open them directly in the Content Browser.

> **Important:** Do not edit template assets in place. Always duplicate a template into your own project content folder before customising it.

---

## Spawning a template

1. Click the **Template Library** button in the Level Editor toolbar.
2. Find the template you want and click **Add to Level**.
3. A PCGVolume is spawned at the camera position with the template graph assigned.
4. For templates that require a spline or water body actor, the plugin spawns those helpers automatically and wires them to the volume.
5. Press **Generate** on the PCGVolume to run the graph.

---

## Template reference

### PCGT_BiomeTransition
**Demo map:** `Demo_BiomeTransition`

Blends two biomes using a Noise Mask Filter. Points in the noise-high region use one asset set, points in the noise-low region use another. Combine with Weighted Selection By Tag for multi-asset transitions.

**Key nodes:** Noise Mask Filter, Weighted Selection By Tag

---

### PCGT_BoundaryDetect
**Demo map:** `Demo_BoundaryDetect`

Marks boundary points of a point cloud with a `bIsBoundary` bool attribute. Filter on that attribute downstream to place edge-specific assets (fence posts, border rocks, etc.).

**Key nodes:** Boundary Detect

---

### PCGT_ClumpScatter
**Demo map:** `Demo_ClumpScatter`

Converts a sparse even distribution into organic clusters. Each input point becomes a clump center with child points scattered around it. Scale falloff creates natural edge thinning.

**Key nodes:** Blue Noise Scatter, Clump Scatter

---

### PCGT_CurvatureFilter
**Demo map:** `Demo_CurvatureFilter`

Filters points by terrain slope angle. Use to spawn trees only on flat ground and rocks only on steep faces.

**Key nodes:** Slope Filter

> **Note:** This template was called `SurfaceSlopeFilter` in v1.0. The underlying node class is `UPCGCurvatureFilterSettings`; its display name is **PCG Pro: Slope Filter**.

---

### PCGT_DistanceTag
**Demo map:** `Demo_DistanceTag`

Writes the distance from each point to the nearest point in a tagged secondary dataset as a float attribute. Use downstream to drive scale, density, or material selection.

**Key nodes:** Distance To Nearest Tag

---

 ### PCGT_ForestSetup
 **Demo map:** `Demo_ForestSetup`
 
> **v1.0 users:** This template was called `PCGT_VillageCorner` (`DemoVillageCorner`) in v1.0 and has been renamed and reworked into a full forest pipeline in v1.1.

 Full forest pipeline: surface sample → Blue Noise Scatter → Relax Points → Slope Filter → Noise Mask Filter → Clump Scatter. Requires a Landscape.

**Key nodes:** Blue Noise Scatter, Relax Points, Slope Filter, Noise Mask Filter, Clump Scatter

---

### PCGT_GridSnap
**Demo map:** `Demo_GridSnap`

Snaps point positions to a world-space grid and optionally snaps yaw to 90° increments. Use for modular building placement and grid-aligned prop layouts.

> **v1.0 users:** this template was called `PCGT_GridBuildings` in v1.0 and has been renamed to `PCGT_GridSnap` in v1.1.

**Key nodes:** Grid Snap

---

### PCGT_HillsideVegetation
**Demo map:** `Demo_HillsideVegetation`

Combines slope filtering and height filtering to place vegetation only on the appropriate terrain band. Requires a Landscape.

**Key nodes:** Slope Filter, Height Filter, Project To Landscape

---

### PCGT_LandscapeLayerSampler
**Demo map:** `Demo_LandscapeLayerSampler`

Filters and modulates points based on a Landscape paint-layer weight. Spawn grass only on the Grass layer, rocks only on the Rocky layer, etc.

**Key nodes:** Landscape Layer Sampler

**Requires:** A Landscape with at least one painted layer.

---

### PCGT_NaturalForestScatter
**Demo map:** `Demo_ForestSetup`

A lighter forest template focused on natural scatter quality. Blue Noise Scatter → Relax Points gives an even, non-grid distribution without the full pipeline weight of `PCGT_ForestSetup`.

**Key nodes:** Blue Noise Scatter, Relax Points

---

### PCGT_NoiseMaskFilter
**Demo map:** `Demo_NoiseMaskFilter`

Breaks up uniform coverage with a Perlin noise mask. Clearings and dense patches emerge naturally without any additional geometry.

**Key nodes:** Noise Mask Filter

---

### PCGT_PrintStats
**Demo map:** *(use any graph)*

A debug utility template with Print Stats nodes placed at key pipeline stages. Shows how to label checkpoints and selectively disable logging.

**Key nodes:** Print Stats

---

### PCGT_RelaxPoints
**Demo map:** `Demo_RelaxPoints`

Demonstrates Lloyd relaxation. Random points in → evenly spaced points out. Compare the before/after Print Stats output to see the effect.

**Key nodes:** Relax Points, Print Stats

---

### PCGT_SplineAvoidance
**Demo map:** `Demo_SplineAvoidance`

Removes points near a road spline to keep vegetation clear of the path. A soft `FalloffRadius` blends density at the edge instead of a hard cut.

**Key nodes:** Spline Avoidance

**Requires:** A spline actor. The template auto-spawns one and sets `SplineActorTag` to `Road`.

---

### PCGT_SplineRoad
**Demo map:** `Demo_SplineRoad`

Full roadside dressing pipeline. Spline Avoidance clears the road centre; Align To Nearest Spline rotates props to face the road direction; Density Falloff thins props away from the road.

**Key nodes:** Spline Avoidance, Align To Nearest Spline, Density Falloff

**Requires:** A spline actor with Actor Tag `Road`. The template auto-spawns one.

---

### PCGT_WaterBodyAvoidance
**Demo map:** `Demo_WaterBodyAvoidance`

Removes points within a radius of any UE Water body (River, Lake, Ocean, Custom). Optional invert mode keeps only riverbank/lakeshore points.

**Key nodes:** Water Body Avoidance

**Requires:** The UE Water plugin enabled and at least one Water Body actor in the level.

---

### PCGT_WeightedSelection
**Demo map:** `Demo_WeightedSelection`

Samples from several tagged point datasets by relative weight. Use to drive biome-aware asset selection where one biome should appear three times as often as another.

**Key nodes:** Weighted Selection By Tag

---

## Customising a template

1. In the Content Browser, right-click the template → **Duplicate**.
2. Move the duplicate into your project content folder (outside `Plugins/`).
3. Open the PCGVolume in your level → swap its graph to the duplicate.
4. Edit freely.

---

Next: [`04_Nodes.md`](04_Nodes.md)
