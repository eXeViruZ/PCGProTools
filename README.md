<!-- Copyright (c) 2026 Tom Leon Vincent Hanke -->
<!-- This file is the GitHub repository README. Place at repo root, NOT in Docs/. -->

# PCG Pro Tools

Production-ready toolkit of custom PCG nodes, editor tools, templates, and presets for Unreal Engine 5.7.

## What it is

PCG Pro Tools extends Unreal Engine's built-in Procedural Content Generation Framework with **17 custom C++ nodes**, a full **editor toolbar** (Debug Overlay · Template Library · Graph Inspector), **17 graph templates**, **4 presets**, and **15 demo maps** — tuned for real production use on landscapes, splines, water bodies, and grid-based layouts.

No engine modifications required.

---

## What you get

### 17 Custom PCG Nodes

| Node | Category | Purpose |
|---|---|---|
| Blue Noise Scatter | Filter | Poisson-disk thinning for even, organic point distribution |
| Boundary Detect | Metadata | Marks edge points of a point cloud as `bIsBoundary` |
| Clump Scatter | Sampler | Expands each point into a natural-looking group of child points |
| Relax Points | Spatial | Lloyd-relaxation for evenly spaced, non-clustered distributions |
| Slope Filter | Filter | Filters by terrain slope angle (reads surface normal from Transform Z) |
| Noise Mask Filter | Filter | Perlin-noise mask — creates organic clearings and density variation |
| Density Falloff | Density | Fades point density radially (Linear / Exponential / Curve) |
| Height Filter | Filter | Filters by world-space Z, optionally relative to a Landscape |
| Grid Snap | Spatial | Snaps points to a world-space grid, optional 90° yaw snap |
| Spline Avoidance | Filter | Removes/attenuates points near spline actors; supports invert mode |
| Align To Nearest Spline | Spatial | Rotates each point toward the tangent of the nearest spline |
| Water Body Avoidance | Filter | Removes/attenuates points near UE Water body splines |
| Distance To Nearest Tag | Metadata | Writes nearest-point distance to a float attribute |
| Weighted Selection By Tag | Filter | Samples from multiple tagged datasets by relative weight |
| Print Stats | Debug | Passthrough node that logs point count, bounds, Z range, and density |
| Project To Landscape | Spatial | Projects points onto a Landscape surface with optional normal align |
| Landscape Layer Sampler | Filter | Filters/modulates points based on a Landscape paint-layer weight |

### Editor Tools

| Tool | Toolbar Button | Description |
|---|---|---|
| Debug Overlay | 🔲 | Draws bounds boxes around PCG actors in the viewport |
| Template Library | 📋 | Browse and spawn pre-built PCG graph templates into the level |
| Graph Inspector | 🔍 | Inspect and tweak all `PCG_Overridable` node parameters of the selected PCG actor |

### 17 Graph Templates

Biome Transition · Boundary Detect · Clump Scatter · Curvature Filter · Distance Tag ·
Forest Setup · Grid Snap · Hillside Vegetation · Landscape Layer Sampler · Natural Forest Scatter ·
Noise Mask Filter · Print Stats · Relax Points · Spline Avoidance · Spline Road ·
Water Body Avoidance · Weighted Selection

### 4 Presets

Beach Sparse · Forest Dense · Mountain Rocky · Urban Grid

### 15 Demo Maps

One map per node — open, select the PCGVolume, press **Generate**.

`Demo_BiomeTransition` · `Demo_BoundaryDetect` · `Demo_ClumpScatter` · `Demo_CurvatureFilter` ·
`Demo_DistanceTag` · `Demo_ForestSetup` · `Demo_GridSnap` · `Demo_HillsideVegetation` ·
`Demo_LandscapeLayerSampler` · `Demo_NoiseMaskFilter` · `Demo_RelaxPoints` ·
`Demo_SplineAvoidance` · `Demo_SplineRoad` · `Demo_WaterBodyAvoidance` · `Demo_WeightedSelection`

---

## Requirements

- Unreal Engine **5.7** (tested on 5.7.4)
- Built-in **Procedural Content Generation Framework** plugin enabled
- Platforms: **Win64** (validated), Linux, Mac (code-compatible)

---

## Quickstart

1. Copy `PCGProKit/` into `<YourProject>/Plugins/PCGProKit/` (or install from Fab).
2. Open your project → **Edit → Plugins** → enable **PCG Pro Tools** → restart the editor.
3. In the Content Browser enable **Show Plugin Content**.
4. Open `Plugins/PCGProKit Content/Maps/Demo_BoundaryDetect`.
5. Select the **PCGVolume** → press **Generate** on the PCGComponent.

Full install + workflow: [`Docs/01_Installation.md`](Docs/01_Installation.md) and [`Docs/02_Workflow.md`](Docs/02_Workflow.md).

---

## Documentation

Full docs live in `Docs/`:

| # | Document |
|---|---|
| 01 | [Installation](Docs/01_Installation.md) |
| 02 | [Workflow](Docs/02_Workflow.md) |
| 03 | [Templates](Docs/03_Templates.md) |
| 04 | [Nodes](Docs/04_Nodes.md) |
| 05 | [Presets](Docs/05_Presets.md) |
| 06 | [Runtime Usage](Docs/06_Runtime_Usage.md) |
| 07 | [API Reference](Docs/07_API_Reference.md) |
| 08 | [Troubleshooting](Docs/08_Troubleshooting.md) |
| 09 | [Changelog](Docs/09_Changelog.md) |

Start here: [`Docs/INDEX.md`](Docs/INDEX.md)

---

## Must-read rules (TL;DR)

- **Runtime needs explicit references.** Set `LandscapeRef` / `SplineActors` / `WaterBodyActors` explicitly in cooked builds. Editor auto-discovery is editor-only.
 - **`PCGT_SplineRoad`** requires the spline actor to have the Actor Tag `Road`.
 - **Spline nodes in async PCG execution** — always assign `SplineActorTag` or populate `SplineActors` explicitly. The world-scan fallback only works in synchronous mode.
 - **`Landscape Layer Sampler`** — `LayerName` must match the Layer Name on the `ULandscapeLayerInfoObject` asset exactly.
 - **Don't edit plugin-content assets in place.** Duplicate templates/presets into your project content before customising.
 - **`SurfaceSlopeFilter` was renamed to `Slope Filter`** in v1.1. Graphs from v1.0 using the old node must be updated.
 - **Renamed templates/maps in v1.1:** `PCGT_VillageCorner` → `PCGT_ForestSetup`, `DemoVillageCorner` → `Demo_ForestSetup`, `PCGT_GridBuildings` → `PCGT_GridSnap`. Update any level or graph references accordingly.

Details: [`Docs/08_Troubleshooting.md`](Docs/08_Troubleshooting.md)

---

## Repository layout

```
PCGProKit/
  PCGProKit.uplugin
  Source/
    PCGProKit/            # Runtime module (17 custom nodes, settings, preset system)
    PCGProKitEditor/      # Editor module (toolbar, debug overlay, template library, graph inspector)
  Content/
    Maps/                 # 15 demo maps
    Templates/            # 17 PCGT_* graph assets
    Presets/              # 4 preset data assets
  Docs/                   # Full documentation
  README.md
```

---

## Support

Issues and feedback: open an issue on this repository, or join the Discord: [discord.gg/vgpmnN6nCR](https://discord.gg/vgpmnN6nCR)

When reporting a bug please include:
- Engine version (5.7.x)
- PCG Pro Tools version
- Which demo map or template reproduces the issue
- PCGVolume config + editor `.log`

Full checklist: [`Docs/08_Troubleshooting.md`](Docs/08_Troubleshooting.md)

---

## License

© 2026 Tom Leon Vincent Hanke. Licensed under the Fab marketplace EULA under which you acquired the plugin.
