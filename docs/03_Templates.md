<!-- Copyright (c) 2026 Tom Leon Vincent Hanke -->

# 03 — Templates

Ready-to-use PCG Graphs under `Plugins/PCGProKit Content/Templates/`. Drag a template into the viewport (spawns a `PCGVolume` bound to it) or assign it to an existing `PCGVolume` via Details → `Graph`.

**Standard PCGVolume config for reproducing the demo maps**
- Location `(-12600, -12600, 130)` · Scale `(50, 50, 5)` · Seed `42`

---

## 3.1 Template Overview

| Template | Demo Map | Approx. Instances | Required Input |
|---|---|---|---|
| `PCGT_VillageCorner` | DemoVillageCorner | 1061 | Landscape |
| `PCGT_GridBuildings` | Demo_GridSnap | 100 | — |
| `PCGT_NaturalForestScatter` | Demo_BoundaryDetect | 961 | Landscape |
| `PCGT_BiomeTransition` | Demo_DistanceTag | 961 | Landscape + Anchor points |
| `PCGT_HillsideVegetation` | — | varies | Landscape (with normals) |
| `PCGT_SplineRoad` | Demo_SplineRoad | 34 | Spline actor with Actor Tag `Road` |

---

## 3.2 PCGT_VillageCorner

Village-style scatter: roads, plots, buildings, props, edge vegetation.

**Inputs**
- Landscape inside the volume (auto-detected in editor; set `LandscapeRef` explicitly for runtime).

**Key parameters (Graph Instance)**
- Density multipliers for buildings / props / vegetation.
- Mesh pools per category.

**Notes**
- Uses `ProjectToLandscape` for Z-snap and `BoundaryDetect` for plot-edge detection.
- Deterministic by `Seed` on the `PCGVolume`.

---

## 3.3 PCGT_GridBuildings

Uniform grid placement for city blocks / modular layouts.

**Inputs**
- None mandatory.

**Key parameters**
- `GridSize` on the internal `GridSnap` node — cell size per axis.
- Building mesh pool.

**Notes**
- Fully deterministic. Ideal for level-layout prototyping.
- Set `bDedupe = true` on `GridSnap` if multiple source points can collapse into one cell.

---

## 3.4 PCGT_NaturalForestScatter

Organic forest with clustered placement and interior thinning for performance.

**Inputs**
- Landscape inside the volume.

**Key parameters**
- `EdgeThreshold` on `BoundaryDetect` — wider = thicker edge ring.
- Tree species pool.
- Interior density multiplier.

**Notes**
- Interior points are thinned via the `Boundary` attribute written by `BoundaryDetect`.
- Pair with `PCGPreset_ForestDense` or `PCGPreset_MountainRocky` (see [05_Presets](05_Presets.md)).

---

## 3.5 PCGT_HillsideVegetation

Organic vegetation scatter on hillside terrain using slope and height filtering for realistic placement.

**Node pipeline**
```
Get Landscape Data + Input
  → Surface Sampler
  → SurfaceSlopeFilter   (keep moderate slopes)
  → HeightFilter         (altitude band)
  → BlueNoiseScatter     (even, non-clumping spread)
  → Static Mesh Spawner
  → Output
```

**Inputs**
- Landscape inside the volume. Points must be projected to the landscape surface with normal alignment enabled so `SurfaceSlopeFilter` has valid Z-axis data.

**Key parameters**
- `MinSlopeDeg` / `MaxSlopeDeg` on `SurfaceSlopeFilter` — control which slopes receive vegetation. Default: 5°–35° (moderate hillside).
- `MinZ` / `MaxZ` on `HeightFilter` — altitude band for the vegetation type.
- `MinDistance` on `BlueNoiseScatter` — minimum spacing between placed meshes.
- Vegetation mesh pool on the spawner.

**Notes**
- `SurfaceSlopeFilter` requires points to have valid surface normals. Always pair with `ProjectToLandscape` with `bAlignToNormal = true`.
- `BlueNoiseScatter` is seed-dependent; changing the `PCGVolume` seed produces a valid but different distribution.
- No demo map ships with 1.0 for this template. Assign it to any PCGVolume on a hilly landscape, set the slope range, and generate.

---

## 3.6 PCGT_BiomeTransition

Blends two biomes using distance to anchor points.

**Inputs**
- Landscape.
- Anchor points: a second point source (e.g., spline sampled to points, a `PCGPointData` asset, or a point actor).

**Key parameters**
- `MaxDistance` on `DistanceTag` — controls blend width.
- `bNormalize` — use `[0, 1]` output for direct lerp weights.
- Per-biome mesh pools (A / B).

**Notes**
- Pair with `PCGPreset_BeachSparse` for coastal transitions.

---

## 3.6 PCGT_SplineRoad — READ THIS SECTION

Scatters road props (lamps, fences, signs) along one or more spline actors.

**Required setup — mandatory**

1. Place a spline actor in the level. It must contain a `USplineComponent` with ≥ 2 points.
2. **Add the Actor Tag `Road`** to the spline actor (Details → Actor → Tags).
3. The template consumes splines through a **`Get Spline Data`** node filtered by the tag `Road`.
4. Do **not** wire `Input → SplineSampler.Spline` directly — PCG 5.7 auto-discovery for this path is unreliable. The tag-filtered `Get Spline Data` pattern is the supported approach in this engine version.

**Key parameters**
- Prop spacing along spline.
- Lateral jitter amplitude.
- Prop mesh pool.
- Align-to-tangent toggle on the internal `AlignToNearestSpline` node.

**Runtime note**
- `SplineActors` and `LandscapeRef` have editor fallbacks but **must be set explicitly** for cooked/Shipping builds. The editor fallback iterates the world (cap: 16 actors) which is not appropriate for runtime.

**If nothing generates**
- Missing Actor Tag `Road`: most common cause.
- Spline has < 2 points.
- Volume does not overlap the spline.

---

## 3.7 Python scripting note (spline actor creation)

`unreal.SplineActor` is **not exposed** to Python in 5.7. To script spline actor creation:

```python
import unreal

world = unreal.EditorLevelLibrary.get_editor_world()
actor = unreal.EditorLevelLibrary.spawn_actor_from_class(
    unreal.Actor, unreal.Vector(0, 0, 0))

subsystem = unreal.get_engine_subsystem(unreal.SubobjectDataSubsystem)
root_handle = subsystem.k2_gather_subobject_data_for_instance(actor)[0]
params = unreal.AddNewSubobjectParams()
params.parent_handle = root_handle
params.new_class = unreal.SplineComponent
params.blueprint_context = None
subsystem.add_new_subobject(params)

actor.tags = [unreal.Name('Road')]
```

---

## 3.8 Creating Your Own Template

1. Duplicate a shipped `PCGT_*` into your project content (do not edit plugin-content assets directly).
2. Open in the PCG Graph editor.
3. Swap mesh pools and tune parameters.
4. Save and assign to your `PCGVolume`.

---

**Next:** [04_Nodes](04_Nodes.md)
