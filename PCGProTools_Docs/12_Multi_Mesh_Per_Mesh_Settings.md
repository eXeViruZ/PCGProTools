# 12 — Multi-Mesh: settings per mesh

Multi-Mesh Scatter places several meshes with a weighted choice per point. With **Per-mesh settings** each mesh gets its own transform rules.

## Turn it on

1. Open **Quick Setup…** for **Multi-Mesh Scatter** (top of the Template Library).
2. Add your meshes and set their weights.
3. Tick **Per-mesh settings (own scale, yaw, tilt and Z offset per mesh)**.
4. For each mesh set:
   - **Scale** from / to,
   - **Random yaw** on or off,
   - **Tilt ±°**,
   - **Z offset cm** (world height; negative values sink rocks into the ground).

Up to 16 meshes are supported. Example: grass 0.9–1.6 with 10° tilt, flowers 0.8–1.3, small rocks 0.25–0.5 with 20° tilt and −10 cm.

## How it works

The graph uses only native PCG nodes: each point gets one random value, non-overlapping weight ranges decide which mesh it receives, and each mesh has its own Transform Points and Static Mesh Spawner. The same seed always gives the same result.

## Switching modes

With **Per-mesh settings** off, all meshes share one scale/yaw/tilt range (the previous behaviour). Switching between the modes creates a **new graph** and assigns it to the volume in one undoable step; the previous graph stays in your project.

---

Next: [13 — Saved setups (recipes)](13_Saved_Setups.md)
