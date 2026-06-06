# 05 — Presets

PCG Pro Tools v1.1 introduces a **DataAsset-based preset system** that lets you save, share, and reapply node parameter configurations across PCG graphs.

---

## What a preset is

A preset is a `UPCGProKitPresetAsset` — a standard UE Data Asset that stores a list of property overrides. Each override targets one specific settings class (e.g. `UPCGBlueNoiseScatterSettings`) and one property on that class (e.g. `MinDistance`). When you apply a preset to a PCGComponent, the plugin iterates the graph's nodes, matches each node's settings class against the override's target class, and writes the value via UE property reflection.

This means:
- Presets work on **any graph** that contains the target node types.
- New nodes added in future plugin versions require **no changes** to the preset system.
- Presets are standard `.uasset` files — they can be added to source control, shared between team members, and duplicated like any other asset.

---

## Included presets

All four presets ship in `Plugins/PCGProKit Content/Presets/`.

| Preset | Target use case | Key overrides |
|---|---|---|
| `Preset_BeachSparse` | Sandy coastal areas | Low MinDistance, sparse clumps |
| `Preset_ForestDense` | Dense forest cover | High ClumpSize, tight MinDistance |
| `Preset_MountainRocky` | Rocky hillside terrain | Steep slope filter range, large clump radius |
| `Preset_UrbanGrid` | Grid-aligned urban props | GridSize snapped, rotation snap on |

> **Important:** Do not edit plugin presets in place. Duplicate them into your project content before customising.

---

## Applying a preset

1. Select a **PCGVolume** (or any actor with a PCGComponent) in the level.
2. Open the **Graph Inspector** from the toolbar (or right-click the actor → **Open in PCG Graph Inspector**).
3. In the **Preset** dropdown at the bottom of the Inspector, select the preset you want.
4. Click **Apply Preset**.
5. The overrides are written into the matching nodes. The operation is **undoable** (Ctrl+Z).

If **Auto-Regenerate** is enabled, the graph re-runs immediately after applying.

---

## Saving a preset (Snapshot)

1. Configure the graph's node parameters the way you want them.
2. In the Graph Inspector, click **Save as Preset**.
3. The standard Content Browser **Create Asset** dialog opens, defaulting to your project's `/Game` content. Choose a location and name, then confirm.
4. A new `UPCGProKitPresetAsset` is created with all current `PCG_Overridable` values captured from every node in the graph. It appears in the preset dropdown immediately.
5. Save it to disk like any other asset (Ctrl+S or Save All).

> **Presets are saved into your project**, not into the plugin. This keeps your presets safe across plugin updates and re-installs, and lets them live under your project's source control. The 4 shipped presets remain in the plugin content and are always available in the dropdown alongside your own.

The snapshot captures **Float**, **Integer**, and **Bool** properties. Vector and string properties are not captured in v1.1.

---

## Creating a preset manually

1. In the Content Browser → right-click → **Miscellaneous → Data Asset**.
2. Choose `PCGProKitPresetAsset` as the class.
3. Fill in `PresetName` and optionally `Description` and `TargetGraph` (informational only — presets can be applied to any graph).
4. Add entries to the `Overrides` array. Each entry requires:
   - `NodeClass` — the settings class to target (e.g. `UPCGBlueNoiseScatterSettings`)
   - `PropertyName` — the exact `UPROPERTY` name on that class (e.g. `MinDistance`)
   - `Type` — `Float`, `Integer`, or `Bool`
   - The corresponding value field (`FloatValue`, `IntValue`, or `BoolValue`)

---

## Preset property type reference

All `PCG_Overridable` properties currently overridable by the preset system:

| Node | Property | Type |
|---|---|---|
| Blue Noise Scatter | `MinDistance` | Float |
| Blue Noise Scatter | `MaxAttempts` | Int |
| Blue Noise Scatter | `MaxPoints` | Int |
| Boundary Detect | `NeighborRadius` | Float |
| Boundary Detect | `MinNeighborCount` | Int |
| Clump Scatter | `ClumpSize` | Int |
| Clump Scatter | `ClumpRadius` | Float |
| Clump Scatter | `bScaleFalloff` | Bool |
| Clump Scatter | `MinEdgeScale` | Float |
| Clump Scatter | `bKeepSourcePoint` | Bool |
| Density Falloff | `Radius` | Float |
| Density Falloff | `bCenterRelativeToVolume` | Bool |
| Grid Snap | `GridSize` | Float |
| Grid Snap | `bSnapRotationToGrid` | Bool |
| Height Filter | `MinZ` | Float |
| Height Filter | `MaxZ` | Float |
| Height Filter | `bUseLandscapeReference` | Bool |
| Landscape Layer Sampler | `MinWeight` | Float |
| Landscape Layer Sampler | `bModulateDensity` | Bool |
| Landscape Layer Sampler | `bInvertFilter` | Bool |
| Noise Mask Filter | `NoiseScale` | Float |
| Noise Mask Filter | `Threshold` | Float |
| Noise Mask Filter | `FalloffWidth` | Float |
| Noise Mask Filter | `bInvertMask` | Bool |
| Print Stats | `bPrintEnabled` | Bool |
| Project To Landscape | `ZOffset` | Float |
| Project To Landscape | `bProjectRotation` | Bool |
| Relax Points | `Iterations` | Int |
| Relax Points | `SearchRadius` | Float |
| Relax Points | `RelaxStrength` | Float |
| Slope Filter | `MinAngle` | Float |
| Slope Filter | `MaxAngle` | Float |
| Slope Filter | `FalloffAngle` | Float |
| Slope Filter | `bInvertFilter` | Bool |
| Spline Avoidance | `AvoidanceRadius` | Float |
| Spline Avoidance | `FalloffRadius` | Float |
| Spline Avoidance | `bInvertSelection` | Bool |
| Water Body Avoidance | `AvoidanceRadius` | Float |
| Water Body Avoidance | `FalloffRadius` | Float |
| Water Body Avoidance | `bInvertSelection` | Bool |
| Weighted Selection By Tag | `SelectionCount` | Int |

---

Next: [`06_Runtime_Usage.md`](06_Runtime_Usage.md)
