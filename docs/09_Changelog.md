<!-- Copyright (c) 2026 Tom Leon Vincent Hanke -->

# 09 — Changelog

All notable changes to PCGProKit are documented here. Format follows [Keep a Changelog](https://keepachangelog.com/). Versioning follows [SemVer](https://semver.org/).

---

## [1.0.0] — Initial Release

**Target engine:** Unreal Engine 5.7.4

### Added

- **Custom PCG Nodes** (module `PCGProKit`):
  - `ProjectToLandscape` — Z-snap points to landscape with optional normal alignment.
  - `AlignToNearestSpline` — snap + rotate points to the nearest spline actor.
  - `BoundaryDetect` — classify points as interior / edge / exterior via metadata attribute.
  - `DistanceTag` — write per-point distance-to-nearest-anchor attribute (optionally normalized).
  - `GridSnap` — snap point positions to a uniform grid with optional per-cell dedupe.
- **Templates** (`Plugins/PCGProKit Content/Templates/`):
  - `PCGT_VillageCorner`
  - `PCGT_GridBuildings`
  - `PCGT_NaturalForestScatter`
  - `PCGT_BiomeTransition`
  - `PCGT_SplineRoad`
- **Demo Maps** (`Plugins/PCGProKit Content/Maps/`):
  - `DemoVillageCorner` (~1061 instances)
  - `Demo_GridSnap` (~100 instances)
  - `Demo_BoundaryDetect` (~961 instances)
  - `Demo_DistanceTag` (~961 instances)
  - `Demo_SplineRoad` (~34 instances)
- **Presets** (`Plugins/PCGProKit Content/Presets/`):
  - `Preset_ForestDense`
  - `Preset_BeachSparse`
  - `Preset_UrbanGrid`
  - `Preset_MountainRocky`
- **Editor module** `PCGProKitEditor` with content-browser integration and menu entries.
- **Documentation** (`Plugins/PCGProKit/Docs/`): 01–09 + `INDEX.md`.

### Fixed

- `PCGBoundaryDetectSettings.cpp` — call `Metadata->InitializeOnSet(WriteRanges.MetadataEntryRange[i])` before `Attr->SetValue(...)` to satisfy the PCG 5.7 metadata contract. Resolves engine assert in `PCGMetadataAttributeTpl.h:587`.
- `PCGProjectToLandscapeSettings.cpp` — editor QoL fallback: if `LandscapeRef` is empty, the node searches `Context->InputData` for a `UPCGLandscapeData`. Editor-only convenience; runtime still requires explicit assignment.
- `PCGAlignToNearestSplineSettings.cpp` — editor QoL fallback: if `SplineActors` is empty, iterates the current world on the game thread (cap 16 actors with `USplineComponent`). Required includes added: `Engine/World.h`, `EngineUtils.h`, `PCGComponent.h`. Editor-only convenience; runtime still requires explicit assignment.

### Module Dependencies (locked)

- **`PCGProKit` (Runtime)** public deps: `Core`, `CoreUObject`, `Engine`, `PCG`, `DeveloperSettings`, `Landscape`.
- **`PCGProKitEditor` (Editor)** public deps: `Core`, `CoreUObject`, `Engine`, `PCGProKit`, `Slate`, `SlateCore`, `EditorStyle`, `UnrealEd`, `ToolMenus`, `WorkspaceMenuStructure`, `PropertyEditor`, `LevelEditor`, `InputCore`, `Projects`, `AssetRegistry`.
- **No `PCGEditor` dependency** — the engine's `PCGEditor` module exports no public API in 5.7.

### Known Limitations

- Automation test for `BoundaryDetect` using `FTestData` + `UPCGPointArrayData` context setup crashes the editor in 5.7 and is not shipped. Regression coverage is delivered via the 5 demo maps.
- Editor-only fallbacks on `ProjectToLandscape` and `AlignToNearestSpline` do **not** apply in cooked builds. For runtime, set `LandscapeRef` and `SplineActors` explicitly.
- PCG 5.7 auto-discovery `Input → SplineSampler.Spline` is unreliable; `PCGT_SplineRoad` uses a `Get Spline Data` node with Actor-Tag `Road` filter as the supported pattern.
- Win64 Shipping validated. Other platforms are code-compatible but untested in 1.0.
- `unreal.SplineActor` is not exposed to Python in UE 5.7. Use the `Actor + SplineComponent` workaround documented in [07_API_Reference](07_API_Reference.md) § 7.6.

---

## Upgrade Notes

### Upgrading from pre-release / internal builds

- Close the editor before replacing plugin files.
- Delete `<YourProject>/Intermediate/` and `<YourProject>/Binaries/`, then rebuild.
- Do not edit plugin-content templates or presets in place — duplicate them into your project content before customizing. Plugin upgrades overwrite plugin-content assets.
- After upgrade, re-open each of the 5 demo maps and press **Generate** on the `PCGVolume` to verify the baseline.

---

## Versioning Policy

- **Patch** (1.0.x): bug fixes, no API or asset-layout changes.
- **Minor** (1.x.0): new nodes, templates, or presets; additive API changes.
- **Major** (x.0.0): breaking API or asset-layout changes; engine major/minor retarget.

Breaking changes will be called out explicitly in this file with migration steps.

---

**Back to:** [INDEX](INDEX.md)
