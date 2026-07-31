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
- The validator is checking a different selected PCG actor/graph.

Load the relevant actors and validate again.

---

## 8.14 — Water Body Avoidance does nothing

Check:

1. Enable the UE Water plugin.
2. Confirm the actor derives from `AWaterBody`.
3. Confirm the actor is loaded.
4. Confirm avoidance radius/falloff values.
5. In cooked builds, verify references and packaging.

The plugin has no hard Water dependency. Without Water Body classes/actors, input passes through.

---

## 8.15 — Landscape Layer Sampler works in editor but not packaged

This is expected.

The node's actual sampling code is editor-only. In non-editor builds it logs a warning and passes all points through unchanged.

Do not use it as a required runtime filter.

---

## 8.16 — Landscape Layer Sampler misses World Partition areas

The relevant Landscape cell/proxy must be loaded.

Load the required cells and set `LandscapeRef` explicitly when multiple Landscapes are present.

---

## 8.17 — Instance Variation color is not visible

The node writes point Color, not custom instance data directly.

Configure:

1. Static Mesh Spawner: enable `bApplyColorAsPerInstanceCustomData`.
2. Material: read Per Instance Custom Data indices 0, 1, and 2.
3. Use the values as RGB.

Also confirm the material and mesh support the intended ISM/HISM path.

---

## 8.18 — Distance LOD does not remove distant points

Distance LOD modifies Density only.

Add a downstream Density Filter or configure the spawner to respect density. Points with Density 0 can remain in the point dataset until culled downstream.

---

## 8.19 — Biome Mask does not use my biome volume

Biome Mask has no external Actor/Shape/Region input.

It creates radial density and transition zones relative to the PCG volume center. Use downstream density filters or separate spawners.

---

## 8.20 — A template or demo map shows a missing example mesh

Most templates and demo maps use visualization assets from Unreal Engine's built-in PCG plugin, commonly under:

```text
/PCG/SampleContent/
```

Confirm that the required PCG plugin is enabled and that plugin content is mounted. For production use, replace the example Static Mesh Spawner references with assets from your own project.

`Demo_GridSnap` uses `/Engine/BasicShapes/Cube`, and the Landscape Layer Sampler demo includes its required `LI_Grass` asset in PCG Pro Tools plugin content.

---

## 8.21 — Distance Tag demo or workflow finds no POIs

The corrected v2 demo expects:

- Actors tagged `POI`
- Get Actor Data set to all world actors
- Selection by Actor Tag
- Select Multiple enabled
- Target data tagged `TargetPoints`

Move or retag a POI actor and regenerate when the live result does not update.

---

## 8.22 — Debug Overlay reduces editor FPS

Use:

- `SelectedOnly`
- Lower `MaxPointsPerComponent`
- Shorter label draw distance
- Node Type Debug Filter
- Avoid `AllInLevel` in large levels

---

## 8.23 — Reporting a bug

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
