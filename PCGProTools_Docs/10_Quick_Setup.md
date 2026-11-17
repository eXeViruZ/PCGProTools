# 10 — Quick Setup workflows

Quick Setup turns a template into **your own editable graph** with your meshes and settings. The shipped template is never changed.

## Before you start

- Open a level with a **Landscape** (most templates sample the landscape).
- Import or pick the static meshes you want to scatter.
- Open the Template Library with its button in the Level Editor toolbar (tooltip "Open PCG Pro Template Library"). The Graph Inspector button sits next to it.

## Create a setup

1. Point the viewport camera at the area you want to fill. A new PCG Volume is created in front of the camera.
2. In the Template Library, click **Quick Setup…** next to a template (20 of 23 templates support it; the button explains why when a template does not).
3. Under **Meshes**, select meshes in the Content Browser and click **+ Add Content Browser Selection**. Remove the example mesh with its trash button. Set a **Weight** per mesh.
4. Adjust the **Parameters** (density, spacing, slope and so on). The dialog shows warnings and information before anything is created.
5. Choose the **Folder** and **Name** for the new graph (inside `/Game`) and the volume size.
6. Click **Create & Generate**.

The new graph is a normal PCG graph asset. Undo (**Ctrl+Z**) removes the new volume; the graph asset stays in the Content Browser so no work is lost.

## Change an existing setup

Select the PCG Volume, open the **Graph Inspector** and click **Quick Setup…**. The dialog reads the current values back from the graph. **Apply & Regenerate** writes the changes as one undoable step.

## Typical combinations

| Goal | Template | Tips |
|---|---|---|
| Forest with a clearing | Forest Setup + [exclusion zones](16_Exclusion_Zones.md) | Tag a building `PCGExclude` |
| Meadow with grass, flowers, small rocks | Multi-Mesh Scatter with [settings per mesh](12_Multi_Mesh_Per_Mesh_Settings.md) | Save it as a [recipe](13_Saved_Setups.md) |
| Planting along roads | Roadside Generator with [multiple roads](15_Multiple_Road_Splines.md) | "Road splines overlapping this volume" |
| Rocks and trees on slopes | Hillside Vegetation | Maximum slope limits steep faces |

---

Next: [11 — Generation Report and Setup Check](11_Generation_Report_and_Setup_Check.md)
