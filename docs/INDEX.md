<!-- Copyright (c) 2026 Tom Leon Vincent Hanke -->

# PCGProKit — Documentation Index

Production-ready toolkit of custom PCG nodes, templates, and presets for **Unreal Engine 5.7.4**.

---

## Table of Contents

| # | Document | Purpose |
|---|---|---|
| 01 | [Installation](01_Installation.md) | Requirements, Fab & source install paths, module layout, verification. |
| 02 | [Workflow](02_Workflow.md) | End-to-end workflow from empty level to deterministic generation. |
| 03 | [Templates](03_Templates.md) | All 6 shipped PCG Graph templates, inputs, and tuning. |
| 04 | [Nodes](04_Nodes.md) | Full reference for every custom PCG node (pins, properties, fallbacks). |
| 05 | [Presets](05_Presets.md) | Shipped preset assets and custom preset creation. |
| 06 | [Runtime Usage](06_Runtime_Usage.md) | Rules for PIE, cooked, dedicated server, and runtime spawning. |
| 07 | [API Reference](07_API_Reference.md) | C++, Blueprint, and Python surface. Extension rules. |
| 08 | [Troubleshooting](08_Troubleshooting.md) | Known issues, fixes, and workarounds. |
| 09 | [Changelog](09_Changelog.md) | Release notes and versioning policy. |

---

## At a Glance

| Item | Value |
|---|---|
| Engine | UE 5.7.4 (exact) |
| Required built-in plugin | Procedural Content Generation Framework (`PCG`) |
| Runtime module | `PCGProKit` |
| Editor module | `PCGProKitEditor` |
| Validated platform | Win64 (Shipping) |
| Standard `PCGVolume` config | Loc `(-12600, -12600, 130)` · Scale `(50, 50, 5)` · Seed `42` |

---

## Shipped Content Summary

**Custom Nodes** — 10:

| Node | Purpose |
|---|---|
| ProjectToLandscape | Z-snap points to landscape surface |
| AlignToNearestSpline | Snap + rotate points to nearest spline |
| BoundaryDetect | Classify interior / edge / exterior of a point cluster |
| Distance To Nearest Tag | Write float distance to nearest point in tagged data sets |
| GridSnap | Snap points to a uniform grid (+ per-cell dedupe) |
| SurfaceSlopeFilter | Filter points by surface slope angle (0–90°) |
| HeightFilter | Filter points by world-space Z (absolute or relative to landscape) |
| BlueNoiseScatter | Poisson-disk thinning — minimum spacing, no clumps |
| Weighted Selection By Tag | Weighted random selection from multiple tagged data sets |
| Density Falloff | Modulate point density by distance from a center (Linear / Exponential / Curve) |

**Templates** — 6:

| Template | Demo Map | Approx. Instances |
|---|---|---|
| PCGT_VillageCorner | DemoVillageCorner | 1061 |
| PCGT_GridBuildings | Demo_GridSnap | 100 |
| PCGT_NaturalForestScatter | Demo_BoundaryDetect | 961 |
| PCGT_BiomeTransition | Demo_DistanceTag | 961 |
| PCGT_HillsideVegetation | — | varies |
| PCGT_SplineRoad | Demo_SplineRoad | 34 |

> `PCGT_HillsideVegetation` ships without a dedicated demo map in 1.0. Assign it to any PCGVolume on a hilly landscape.

**Presets** — 4: `Preset_ForestDense`, `Preset_BeachSparse`, `Preset_UrbanGrid`, `Preset_MountainRocky`.

---

## Most Important Rules (read these once)

1. **Runtime needs explicit references.** Editor fallbacks on `ProjectToLandscape` (`LandscapeRef`) and `AlignToNearestSpline` (`SplineActors`) do **not** apply in cooked builds. See [06_Runtime_Usage](06_Runtime_Usage.md).
2. **`PCGT_SplineRoad` requires the spline actor to have the Actor Tag `Road`.** The template uses a `Get Spline Data` node with a tag filter — do **not** rely on `Input → SplineSampler.Spline` auto-discovery (unreliable in PCG 5.7). See [03_Templates](03_Templates.md) § 3.7.
3. **`SurfaceSlopeFilter` requires valid surface normals.** Always precede with `ProjectToLandscape` with `bAlignToNormal = true`. See [04_Nodes](04_Nodes.md) § 4.8.
4. **Custom metadata writes must call `InitializeOnSet` before `SetValue`** in PCG 5.7, or you will hit the assert in `PCGMetadataAttributeTpl.h:587`. See [04_Nodes](04_Nodes.md) § 4.4 and [08_Troubleshooting](08_Troubleshooting.md) § 8.6.
5. **If a `PCGVolume` has zero extent, respawn it.** Do not try to repair in place. See [08_Troubleshooting](08_Troubleshooting.md) § 8.3.
6. **Live Coding fixes do not survive an editor restart.** Always finish a session with a full IDE rebuild before running `BuildPlugin -configuration=Shipping`. See [08_Troubleshooting](08_Troubleshooting.md) § 8.1.
7. **Do not edit plugin-content assets in place.** Duplicate templates/presets into your project content before customizing — plugin upgrades will overwrite them.

---

## Support

When reporting an issue, include:
- Engine version (must be 5.7.4 for support in 1.0)
- PCGProKit version
- Minimal repro map or the affected demo map
- `PCGVolume` config, template, preset
- Editor `.log`
- Whether Live Coding was used during the session

See [08_Troubleshooting](08_Troubleshooting.md) § 8.13 for the full checklist.

---

© 2026 Tom Leon Vincent Hanke. Licensed under the Fab marketplace EULA under which you acquired the plugin.
