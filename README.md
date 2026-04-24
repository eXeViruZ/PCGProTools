<!-- Copyright (c) 2026 Tom Leon Vincent Hanke -->
<!-- This file is the GitHub repository README. Place at repo root, NOT in Docs/. -->

# PCGProKit

Production-ready toolkit of custom PCG nodes, templates, and presets for **Unreal Engine 5.7.4**.

[![Engine](https://img.shields.io/badge/Unreal%20Engine-5.7.4-blue)](https://www.unrealengine.com/)
[![Platform](https://img.shields.io/badge/Platform-Win64-lightgrey)]()
[![License](https://img.shields.io/badge/License-Fab%20EULA-green)]()

---

## What it is

PCGProKit extends Unreal's built-in **Procedural Content Generation Framework** with 5 custom nodes, 5 graph templates, 4 presets, and 5 demo maps — tuned for real production use on landscapes, splines, and grid-based layouts.

## What you get

- **10 Custom PCG Nodes** — `ProjectToLandscape`, `AlignToNearestSpline`, `BoundaryDetect`, `DistanceToNearestTag`, `GridSnap`, `SurfaceSlopeFilter`, `HeightFilter`, `BlueNoiseScatter`, `WeightedSelectionByTag`, `DensityFalloff`.
- **6 Templates** — Village Corner, Grid Buildings, Natural Forest Scatter, Biome Transition, Hillside Vegetation, Spline Road.
- **4 Presets** — Forest Dense, Beach Sparse, Urban Grid, Mountain Rocky.
- **5 Demo Maps** — each pre-wired, press *Generate* and see ~100–1000+ instances.

## Requirements

- Unreal Engine **5.7.4** (exact)
- Built-in **Procedural Content Generation Framework** (`PCG`) plugin enabled
- Win64 validated; other platforms are code-compatible, untested in 1.0

## Quickstart

1. Copy `PCGProKit/` into `<YourProject>/Plugins/PCGProKit/` (or install from Fab).
2. Open your project, enable **PCGProKit** in `Edit → Plugins`, restart the editor.
3. Open `Plugins/PCGProKit Content/Maps/DemoVillageCorner` (enable *Show Plugin Content* in the Content Browser).
4. Select the `PCGVolume` and press **Generate** on the `PCGComponent`.

Expected: ~1061 instances.

Full install + workflow: see [`Docs/01_Installation.md`](Docs/01_Installation.md) and [`Docs/02_Workflow.md`](Docs/02_Workflow.md).

## Documentation

Full docs live in [`Docs/`](Docs/INDEX.md):

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

Start here: [`Docs/INDEX.md`](Docs/INDEX.md).

## Must-read rules (TL;DR)

1. **Runtime needs explicit references.** Set `LandscapeRef` / `SplineActors` explicitly in cooked builds. Editor fallbacks are editor-only.
2. **`PCGT_SplineRoad` requires the spline actor to have the Actor Tag `Road`.**
3. **Custom metadata writes must call `Metadata->InitializeOnSet(EntryKey)` before `SetValue`** (PCG 5.7 requirement).
4. **Don't edit plugin-content assets in place.** Duplicate templates/presets into your project content before customizing.
5. **Live Coding fixes do not survive editor restart.** Finish sessions with a full IDE rebuild before packaging.

Details in [`Docs/08_Troubleshooting.md`](Docs/08_Troubleshooting.md).

## Repository Layout

```
PCGProKit/
  PCGProKit.uplugin
  Source/
    PCGProKit/            # Runtime module
    PCGProKitEditor/      # Editor module (no PCGEditor dep — no public API in 5.7)
  Content/
    Maps/                 # 5 demo maps
    Templates/            # 5 PCGT_* graphs
    Presets/              # 4 PCGPreset_* data assets
  Docs/                   # Full documentation (this link target)
  README.md               # This file
```

## Support

Issues and feedback: open an issue on this repository, or contact via the Fab listing page. When reporting, include:
- Engine version (must be 5.7.4)
- PCGProKit version
- Minimal repro map (or which demo map reproduces)
- `PCGVolume` config, template, preset
- Editor `.log`

Full checklist: [`Docs/08_Troubleshooting.md`](Docs/08_Troubleshooting.md) § 8.13.

## License

© 2026 Tom Leon Vincent Hanke. Licensed under the Fab marketplace EULA under which you acquired the plugin.
