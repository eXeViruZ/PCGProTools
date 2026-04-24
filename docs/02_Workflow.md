<!-- Copyright (c) 2026 Tom Leon Vincent Hanke -->

# 02 — Workflow

End-to-end workflow from an empty level to a deterministic PCG generation. Use this page as the "first 5 minutes" reference.

---

## 2.1 Core Concept

PCGProKit follows the standard PCG 5.7 flow:

```
PCGVolume (bounds + seed)
   ↓ references
PCG Graph (Template: PCGT_*)
   ↓ uses
Custom PCGProKit Nodes (+ built-in PCG nodes)
   ↓ outputs
Instanced Static Meshes / Actors in the level
```

You control generation through three things:
1. **The `PCGVolume`** — defines the bounds, carries the seed, owns the `PCGComponent`.
2. **The Graph (`PCGT_*`)** — defines the logic and default parameters.
3. **Graph Instance Parameters / Preset** — tunables exposed on the volume for per-placement overrides.

---

## 2.2 Standard PCGVolume Config

All shipped demo maps use these values. Use them as a known-good baseline when reproducing issues:

| Property | Value |
|---|---|
| Location | `(-12600, -12600, 130)` |
| Scale | `(50, 50, 5)` |
| Seed | `42` |

---

## 2.3 First Generation (Village Corner)

1. Open an empty level with at least one `Landscape`.
2. Content Browser → `Plugins/PCGProKit Content/Templates/` (enable **Show Plugin Content**).
3. Drag `PCGT_VillageCorner` into the viewport. A `PCGVolume` bound to the template is spawned.
4. Select the volume. Apply the standard config from 2.2.
5. Details panel → `PCGComponent` → click **Generate**.

Expected: ~1000 instances (roads, buildings, vegetation).

---

## 2.4 General Workflow in Your Own Level

1. **Place a `PCGVolume`**: *Place Actors → Volumes → PCG Volume*.
2. **Assign a template**: Details → `Graph` → choose a `PCGT_*` asset (see [03_Templates](03_Templates.md)).
3. **Size the volume** to cover the target area. Keep `Scale.Z ≥ 1` when using landscape projection.
4. **Set a Seed**. Same seed + same inputs = deterministic output.
5. **Set required inputs** per template:
   - Landscape-based templates: ensure a `Landscape` is inside the volume.
   - Spline-based templates: place a spline actor with the correct **Actor Tag** (see 03).
6. (Optional) **Apply a preset**: Graph Instance parameters → `Preset` → pick a `PCGPreset_*` (see [05_Presets](05_Presets.md)).
7. **Generate**.

---

## 2.5 Iterating

| Action | How |
|---|---|
| Reshuffle layout | Change `Seed` on the volume |
| Change density / thresholds | Override on the Graph Instance or switch preset |
| Move area | Move the volume, then regenerate |
| Clear output | Click **Cleanup** on the `PCGComponent` |
| Auto-regenerate on change | Enable `Regenerate In Editor` on the `PCGComponent` |

PCG caches results. If output looks stale after editing the template asset itself, use **Force Regenerate**.

---

## 2.6 Editor vs Runtime

PCGProKit nodes include editor-convenience fallbacks for actor soft-references (`LandscapeRef`, `SplineActors`). These fallbacks are **editor only** and must not be relied on in cooked builds.

- **Editor**: you can leave references empty — node scans the world.
- **Runtime / Shipping**: set references **explicitly** on the graph instance before calling generate.

See [06_Runtime_Usage](06_Runtime_Usage.md) for the full rules.

---

## 2.7 Typical Mistakes

- `PCGVolume` brush not built → extent is zero → nothing generates. Fix: **respawn** the volume; do not try to repair it in place.
- Spline actor without the required Actor Tag — `PCGT_SplineRoad` filters by the tag `Road` and will output nothing otherwise.
- Editing a plugin-content template or preset directly. Upgrades will overwrite your changes. Always duplicate into your project content first.
- Relying on PCG auto-discovery `Input → SplineSampler.Spline` — unreliable in PCG 5.7. PCGProKit's spline templates use the `Get Spline Data` node with an Actor-Tag filter instead.

---

**Next:** [03_Templates](03_Templates.md)
