# 01 — Installation

## Requirements

| Requirement | Value |
|---|---|
| Unreal Engine | **5.8** |
| Required built-in plugin | Procedural Content Generation Framework (`PCG`) |
| Supported target platforms | Win64, Linux, Mac |
| Hard Water-plugin dependency | None |

PCG Pro Tools v2.0.0 requires no engine modifications.

> UE 5.7 users must remain on PCG Pro Tools v1.1.1. The v2.0.0 `.uplugin` requires EngineVersion `5.8.0`.

---

## Option A — Install from Fab

1. Add **PCG Pro Tools** to your library on Fab.
2. Install the UE 5.8 build through the Epic Games Launcher.
3. Open the target project.
4. Enable **PCG Pro Tools** when prompted.
5. Restart Unreal Editor.

---

## Option B — Manual project installation

1. Close Unreal Editor.
2. Copy the plugin folder to:

   ```text
   <YourProject>/Plugins/PCGProKit/
   ```

3. Confirm that the plugin is enabled in the `.uproject` file or through **Edit → Plugins**:

   ```json
   {
     "Name": "PCGProKit",
     "Enabled": true
   }
   ```

4. Confirm the built-in `PCG` plugin is enabled.
5. Regenerate project files when using a source project.
6. Compile and reopen the project.

---

## Enable the plugin

1. Open **Edit → Plugins**.
2. Search for **PCG Pro Tools**.
3. Enable it.
4. Search for **Procedural Content Generation Framework** and confirm it is enabled.
5. Restart the editor.

The Water plugin is optional. Enable it only for workflows that use Water Body Avoidance or the corresponding demo/template.

---

## First-launch checklist

After restarting, verify:

- The Level Editor toolbar contains **Debug Overlay**, **Template Library**, and **Graph Inspector**.
- The plugin also appears under **Window → PCG Pro Tools** if the toolbar buttons are unavailable.
- **Show Plugin Content** is enabled in the Content Browser.
- `Plugins/PCGProKit Content/` is visible.
- Searching `PCG Pro` in a PCG Graph shows **22 custom nodes**.
- The Template Library lists **23 templates**.

See [Troubleshooting](08_Troubleshooting.md) if any item is missing.

---

## Opening a demo map

1. Enable **Show Plugin Content**.
2. Open `Plugins/PCGProKit Content/Maps/`.
3. Open a `Demo_*` map.
4. Select its PCG actor or volume.
5. Press **Generate** when the graph uses On Demand generation.

PCG Pro Tools v2.0.0 includes **21 demo maps**:

| Demo map | Purpose |
|---|---|
| `Demo_InstanceVariation` | Scale, rotation, and point-color variation |
| `Demo_SplineOffset` | Left/right/both-side spline offset placement |
| `Demo_DistanceLOD` | Distance-based density reduction |
| `Demo_RandomSubset` | Deterministic percentage-based selection |
| `Demo_BiomeMask` | Radial biome-density shaping and transition zones |
| `Demo_RoadsideGenerator` | Prepared roadside spline workflow |
| `Demo_BiomeTransition` | Multi-stage biome transition workflow |
| `Demo_BoundaryDetect` | Point-cloud boundary detection |
| `Demo_ClumpScatter` | Organic clusters |
| `Demo_CurvatureFilter` | Slope-based filtering |
| `Demo_DistanceTag` | External POI distance detection |
| `Demo_ForestSetup` | Full forest pipeline |
| `Demo_GridSnap` | Grid-aligned placement |
| `Demo_HillsideVegetation` | Slope and height-based vegetation |
| `Demo_LandscapeLayerSampler` | Landscape paint-layer placement |
| `Demo_NoiseMaskFilter` | Noise-driven masks |
| `Demo_RelaxPoints` | Lloyd-style point relaxation |
| `Demo_SplineAvoidance` | Clearing vegetation around splines |
| `Demo_SplineRoad` | Spline-based roadside placement |
| `Demo_WaterBodyAvoidance` | Clearing points near Water Body splines |
| `Demo_WeightedSelection` | Weighted selection from tagged datasets |

### Demo visualization assets

Most templates and demo maps use example assets from Unreal Engine's built-in PCG plugin, including meshes under:

```text
/PCG/SampleContent/
```

These assets are available with the required PCG plugin and are used only to visualize the procedural workflows. Replace the Static Mesh Spawner entries with assets from your own project when building production content.

`Demo_LandscapeLayerSampler` includes its required `LI_Grass` Landscape Layer Info asset inside PCG Pro Tools plugin content. `Demo_GridSnap` uses the regular Engine Content cube at `/Engine/BasicShapes/Cube`.

---

## Upgrading from v1.1.1 to v2.0.0

1. Back up the project or commit the current state to source control.
2. Close Unreal Editor and the IDE.
3. Delete the existing plugin folder:

   ```text
   <YourProject>/Plugins/PCGProKit/
   ```

4. Copy the v2.0.0 plugin folder into the same location.
5. Delete generated build data:

   ```text
   <YourProject>/Binaries/
   <YourProject>/Intermediate/
   <YourProject>/Plugins/PCGProKit/Binaries/
   <YourProject>/Plugins/PCGProKit/Intermediate/
   ```

6. Regenerate project files.
7. Compile the project.
8. Open the project in Unreal Engine 5.8.
9. Load representative existing graphs and run a regeneration test.

### Compatibility

- Existing v1.1.1 graph assets remain compatible.
- The 17 existing node classes retain their v1.1.1 names, properties, defaults, clamps, and pins.
- No v1.1.1-to-v2.0.0 asset rename or Core Redirect is required.
- Existing v1.1.1 presets remain compatible.

Historical v1.0-to-v1.1 breaking changes are retained only in the [Changelog](09_Changelog.md).

---

Next: [02 — Workflow](02_Workflow.md)
