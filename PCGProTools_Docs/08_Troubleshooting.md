# 08 — Troubleshooting

This page applies to PCG Pro Tools v2.0.0 for Unreal Engine 5.8.

---

## 8.1 — Toolbar buttons are missing

**Symptom:** Debug Overlay, Template Library, or Graph Inspector is not visible.

**Checks:**

1. Enable **PCG Pro Tools**.
2. Enable the built-in **PCG** plugin.
3. Restart the editor.
4. Open the tools through **Window → PCG Pro Tools**.
5. Check the Output Log for `LogPCGProKit` startup errors.

---

## 8.2 — Custom nodes do not appear

**Symptom:** Searching `PCG Pro` in a graph does not show all 22 nodes.

**Fix:**

- Confirm the project is running UE 5.8.
- Confirm the plugin folder is not nested incorrectly.
- Remove stale plugin/project `Binaries` and `Intermediate`.
- Regenerate project files and rebuild.
- Confirm `PCGProKit` loads as a Runtime module.

---

## 8.3 — Graph Inspector upper panel is empty

The upper panel displays only graph-exposed `UPCGGraphInstance` parameters.

Expose a graph property when it must appear there.

The lower Node Parameter panel is separate and should still list supported `PCG_Overridable` Float, Int32, and Bool properties.

---

## 8.4 — A node property is missing from the lower panel

The direct node panel supports Float, Int32, and Bool parameter rows. Unsupported property types are not represented by the current row implementation.

Also confirm the property is marked `PCG_Overridable`.

---

## 8.5 — Seed Lock resets

Seed Lock is Graph Inspector widget state. It is intentionally not saved to the actor, level, or config.

Re-enable it after reopening the editor or recreating the tab.

---

## 8.6 — Point count does not update immediately

Point count refreshes after component regeneration and reads all point outputs from `GetPCGData()`.

Wait for generation to finish or use **Regenerate Selected**.

---

## 8.7 — Undo, Redo, or Reset appears delayed

The Graph Inspector waits while the PCG component reports active generation, then flushes its cache and regenerates.

A short delay is expected. Repeatedly clicking controls during generation can extend the wait.

---

## 8.8 — Preset Apply changes nothing

Check:

- The graph contains the target settings class.
- `PropertyName` matches the exact C++ property name.
- The stored type matches the property.
- The relevant properties are supported by the preset system.

Incompatible entries are skipped silently.

When several nodes use the same settings class, the override can affect each compatible node.

---

## 8.9 — Template search finds no result

Search matches the template asset name only and is case-insensitive.

Clear the category filter, then search for a term contained in the asset name.

Examples:

```text
Spline
Forest
Distance
Biome
```

---

## 8.10 — Add to Level warns about a missing Landscape

The selected template is classified as Landscape-dependent.

Add or load a Landscape actor before trying again. In World Partition, load the cell containing the required Landscape.

---

## 8.11 — Create Editable Copy cannot be undone

Copy creates a project asset through a filesystem/content operation, not an editor transaction.

Delete the copied asset manually when no longer needed.

---

## 8.12 — Spline workflow finds no spline

Preferred setup:

1. Add an Actor Tag such as `Road`.
2. Set `SplineActorTag` to the same value.
3. Confirm the actor is loaded.

Direct `SplineActors` soft references work only when the target actor is packaged and loaded.

Blind world scanning must not be relied on in cooked builds.

---

## 8.13 — Setup Validator reports a missing actor that exists

The validator checks loaded actors.

Possible causes:

- World Partition cell is unloaded.
- Actor tag does not exactly match.
- A direct soft reference is unresolved.
- Required Water plugin is disabled.
- The validator is checking a different selected PCG actor or graph.

Load the relevant actors and validate again.

---

## 8.14 — Water Body Avoidance does nothing

Check:

1. Enable the UE Water plugin.
2. Confirm the actor derives from `AWaterBody`.
3. Confirm the actor is loaded.
4. Confirm avoidance radius and falloff values.
5. In cooked builds, verify references and packaging.

The plugin has no hard Water dependency. Without Water Body classes or actors, input passes through.

---

## 8.15 — Landscape Layer Sampler works in editor but not packaged

This is expected.

The node's actual layer-sampling code is editor-only. In non-editor builds it logs a warning and passes all points through unchanged.

Do not use it as a required runtime filter.

---

## 8.16 — Landscape Layer Sampler removes all points

Check:

1. `LayerName` must exactly match the Landscape Layer Info asset's layer name.
2. The layer must contain painted weight data.
3. Reduce `MinWeight` when the painted weight is too low.
4. Set `LandscapeRef` explicitly when multiple Landscapes exist.
5. Load the relevant World Partition Landscape cells and proxies.

Use `bInvertFilter` only when the intended result is to keep points below the threshold.

---

## 8.17 — Landscape Layer Sampler misses World Partition areas

The relevant Landscape cell or proxy must be loaded.

Load the required cells and set `LandscapeRef` explicitly when multiple Landscapes are present.

---

## 8.18 — Height Filter removes all or no points

Check:

- `MinZ` must be lower than `MaxZ`.
- Values are in centimeters.
- When `bUseLandscapeReference` is enabled, assign the intended `LandscapeRef`.
- Confirm the selected Landscape is loaded in World Partition.

---

## 8.19 — Boundary Detect marks all or no points as boundary

If every point is marked as a boundary, `NeighborRadius` is probably too small for the point spacing.

If no point is marked as a boundary, `NeighborRadius` may be too large or `MinNeighborCount` too low.

Adjust both values relative to the actual source-point spacing.

---

## 8.20 — Blue Noise Scatter outputs fewer points than expected

Blue Noise Scatter thins an existing point set. It does not generate new source points.

Reduce `MinDistance`, increase the density of the upstream sampler, or increase the available area.

`MaxPoints` is also a hard output cap.

---

## 8.21 — Density Falloff has no visible effect

Check:

- `Center` and `bCenterRelativeToVolume` use the intended coordinate space.
- `Radius` covers the visible point area.
- The downstream workflow respects point Density.

Density Falloff changes Density; it does not automatically delete points. Add a Density Filter or use a density-aware spawner when a visible cull is required.

---

## 8.22 — Instance Variation color is not visible

The node writes point Color, not custom instance data directly.

Configure:

1. Static Mesh Spawner: enable `bApplyColorAsPerInstanceCustomData`.
2. Material: read Per Instance Custom Data indices 0, 1, and 2.
3. Use the values as RGB.

Also confirm the material and mesh support the intended ISM/HISM path.

---

## 8.23 — Instance Variation tilts tree meshes

There is no separate Yaw Only switch.

For upright tree variation use:

```text
YawJitter > 0
PitchJitter = 0
RollJitter = 0
```

---

## 8.24 — Spline Offset appears on the wrong side

Side modes use the point's forward direction:

- Both uses `ScatterWidth` around the center.
- Left Only uses `LeftWidth`.
- Right Only uses `RightWidth`.

If sides appear reversed, verify the source spline direction and the forward direction generated by the upstream spline sampler.

---

## 8.25 — Spline Offset does not create more points

This is expected. Spline Offset moves existing points laterally; it does not generate new points.

Increase the upstream spline-sampling density when more placement points are required.

---

## 8.26 — Distance LOD does not remove distant points

Distance LOD modifies Density only.

Add a downstream Density Filter or configure the spawner to respect Density. Points with Density 0 can remain in the point dataset until culled downstream.

---

## 8.27 — Random Subset keeps the wrong amount

`KeepPercentage` is percentage-based and is applied per input dataset.

There is no fixed-count mode. Confirm the upstream point count and remember that each separately tagged dataset is processed independently.

---

## 8.28 — Biome Mask does not use my biome volume

Biome Mask has no external Actor, Shape, or Region input.

It creates radial density and transition zones relative to the PCGVolume center. Use downstream Density Filters or separate spawners.

---

## 8.29 — A template or demo map shows a missing example mesh

Most templates and demo maps use visualization assets from Unreal Engine's built-in PCG plugin, commonly under:

```text
/PCG/SampleContent/
```

Confirm the required PCG plugin is enabled and its content is mounted. For production use, replace example Static Mesh Spawner references with assets from your own project.

`Demo_GridSnap` uses `/Engine/BasicShapes/Cube`. The Landscape Layer Sampler demo includes its required `LI_Grass` asset in PCG Pro Tools plugin content.

---

## 8.30 — Distance Tag demo or workflow finds no POIs

The corrected v2 demo expects:

- Actors tagged `POI`
- Get Actor Data set to all world actors
- Selection by Actor Tag
- Select Multiple enabled
- Target data tagged `TargetPoints`

Move or retag a POI actor and regenerate when the live result does not update.

---

## 8.31 — Performance warning notification appears

`DensityCapWarningThreshold` is a non-blocking editor warning. No data is removed automatically.

Options:

- Reduce upstream or generated point count.
- Increase the threshold.
- Set the threshold to `0` to disable the warning.

Location:

```text
Project Settings → Plugins → PCG Pro Tools → Performance Guards
```

---

## 8.32 — Debug Overlay reduces editor FPS

Use:

- `SelectedOnly`
- Lower `MaxPointsPerComponent`
- Shorter label draw distance
- Node Type Debug Filter
- Avoid `AllInLevel` in large levels

---

## 8.33 — Reporting a bug

Include:

1. Unreal Engine version: 5.8.x
2. PCG Pro Tools version
3. Template or demo map
4. Selected actor and graph setup
5. Preset and seed
6. Relevant `LogPCGProKit` lines
7. Expected result
8. Actual result
9. Minimal reproduction steps

Open a GitHub issue or post in Discord: [discord.gg/vgpmnN6nCR](https://discord.gg/vgpmnN6nCR)
