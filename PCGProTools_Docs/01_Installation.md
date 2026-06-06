# 01 — Installation

## Requirements

| Requirement | Version |
|---|---|
| Unreal Engine | 5.7 (tested on 5.7.4) |
| PCG Framework | built-in, must be enabled |
| Platform | Win64 (validated), Linux / Mac (code-compatible) |

PCG Pro Tools has **no additional engine dependencies** and requires no engine modifications.

---

## Option A — Install from Fab

1. Purchase **PCG Pro Tools** on [Fab](https://www.fab.com).
2. In the Epic Games Launcher → **Library** → find the plugin → click **Install to Engine** (or **Add to Project**).
3. Restart the editor.

---

## Option B — Manual install

1. Copy the `PCGProKit/` folder into your project:
   ```
   <YourProject>/Plugins/PCGProKit/
   ```
2. Open your `.uproject` file and verify the entry exists (or add it):
   ```json
   {
     "Name": "PCGProKit",
     "Enabled": true
   }
   ```
3. Open the project in Unreal Engine. You will be prompted to compile the plugin — click **Yes**.

---

## Enable the plugin

1. **Edit → Plugins** → search for **PCG Pro Tools**.
2. Enable it and restart the editor when prompted.
3. Also verify that the built-in **Procedural Content Generation Framework** plugin is enabled (it ships with UE 5.7 and is usually on by default).

---

## First launch checklist

After restarting the editor, confirm the following:

- **Toolbar:** Three new buttons appear in the Level Editor toolbar — **Debug Overlay**, **Template Library**, and **Graph Inspector**.
- **Content Browser:** Enable **Show Plugin Content** (filter icon → bottom of dropdown) to see `Plugins/PCGProKit Content/`.
- **PCG node palette:** In any PCG Graph, right-click → search **PCG Pro** — all 17 nodes should appear.

If any of these are missing, see [`08_Troubleshooting.md`](08_Troubleshooting.md).

---

## Opening a demo map

1. In the Content Browser, navigate to `Plugins/PCGProKit Content/Maps/`.
2. Open any `Demo_*` map (e.g. `Demo_BoundaryDetect`).
3. Select the **PCGVolume** actor → in the Details panel, find the **PCGComponent** → click **Generate**.

There are **15 demo maps**. Each showcases a specific feature or pipeline; some maps combine multiple nodes to demonstrate a complete workflow rather than a single node:

| Map | Demonstrates |
|---|---|
| Demo_BiomeTransition | Noise-based biome blending |
| Demo_BoundaryDetect | Edge detection on point clouds |
| Demo_ClumpScatter | Organic cluster placement |
| Demo_CurvatureFilter | Slope-based filtering |
| Demo_DistanceTag | Nearest-point distance attributes |
| Demo_ForestSetup | Full forest scatter pipeline |
| Demo_GridSnap | Grid-aligned prop placement |
| Demo_HillsideVegetation | Combined slope + height filtering |
| Demo_LandscapeLayerSampler | Paint-layer driven placement |
| Demo_NoiseMaskFilter | Noise-driven density variation |
| Demo_RelaxPoints | Lloyd relaxation for even spacing |
| Demo_SplineAvoidance | Clearing points near splines |
| Demo_SplineRoad | Roadside dressing along a spline |
| Demo_WaterBodyAvoidance | Clearing points near water bodies |
| Demo_WeightedSelection | Biome-weighted asset selection |

---

## Upgrading from v1.0

If you are upgrading an existing project from PCG Pro Tools v1.0:

1. Replace the old `PCGProKit/` folder entirely with the v1.1 folder.
2. Recompile when prompted.
 3. **`SurfaceSlopeFilter` was renamed to `Slope Filter` (`UPCGCurvatureFilterSettings`).** Any PCG graph that contained the old node will show a broken node — replace it with the new **PCG Pro: Slope Filter** node and re-enter the property values.
 4. **Renamed assets:** `PCGT_VillageCorner` → `PCGT_ForestSetup`, `DemoVillageCorner` → `Demo_ForestSetup`, and `PCGT_GridBuildings` → `PCGT_GridSnap`. Update any level or graph references accordingly.
 5. All other v1.0 nodes retain their class names and are backward-compatible.
 
 ---

Next: [`02_Workflow.md`](02_Workflow.md)
