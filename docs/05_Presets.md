<!-- Copyright (c) 2026 Tom Leon Vincent Hanke -->

# 05 — Presets

Presets are `UDataAsset` instances that bundle tuning parameters (densities, thresholds, mesh pools) for a given template. They live under `Plugins/PCGProKit Content/Presets/` and are applied to a graph instance via its parameter overrides.

---

## 5.1 Shipped Presets

| Preset | Intended Template | Summary |
|---|---|---|
| `Preset_ForestDense` | `PCGT_NaturalForestScatter` | High tree density, narrow edge ring, mixed conifer/broadleaf pool. |
| `Preset_BeachSparse` | `PCGT_BiomeTransition` | Low density, wide blend band, coastal pool (driftwood, rocks, dune grass). |
| `Preset_UrbanGrid` | `PCGT_GridBuildings` | Tight grid cell size, uniform building pool, no rotation jitter. |
| `Preset_MountainRocky` | `PCGT_NaturalForestScatter` | Sparse trees, large rocks, strong Z-projection, normal alignment enabled. |

> Preset content is finalized in Phase 5 (3–5 shipped). Additional variants may ship in 1.1+.

---

## 5.2 Applying a Preset

1. Select your `PCGVolume` in the level.
2. In the Details panel, expand the PCG **Graph Instance** parameters.
3. Set the **Preset** parameter to one of the assets from 5.1.
4. Press **Generate**.

Presets do **not** contain seeds. Seed is always on the `PCGVolume` so one preset + multiple seeds = layout variation.

---

## 5.3 Creating a Custom Preset

1. Duplicate one of the shipped `PCGPreset_*` assets into your project content.
   - Do not edit plugin-content presets in place — plugin upgrades will overwrite them.
2. Open the duplicate and tune the values.
3. Assign the new asset to the graph instance's **Preset** parameter on your `PCGVolume`.

---

## 5.4 Preset Rules

- One preset targets **one template family**. Do not reuse a `ForestDense` preset on `PCGT_GridBuildings` — parameter names and mesh-pool categories differ.
- Presets are plain `UDataAsset` subclasses — safe to reference from runtime C++ or Blueprints.
- Meshes referenced in a preset must be cookable and included in the cooked content set of your project (or the plugin) if used at runtime.
- Swapping a preset does not force regeneration — press **Generate** (or enable *Regenerate In Editor* on the `PCGComponent`).

---

## 5.5 Recommended Preset per Scenario

| Scenario | Template | Preset |
|---|---|---|
| Dense northern forest | `PCGT_NaturalForestScatter` | `PCGPreset_ForestDense` |
| Rocky alpine slope | `PCGT_NaturalForestScatter` | `PCGPreset_MountainRocky` |
| Coastline / beach to dune | `PCGT_BiomeTransition` | `PCGPreset_BeachSparse` |
| City block / modular grid | `PCGT_GridBuildings` | `PCGPreset_UrbanGrid` |

---

**Next:** [06_Runtime_Usage](06_Runtime_Usage.md)
