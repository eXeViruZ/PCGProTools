# 06 — Runtime Usage

This page covers everything relevant when PCG Pro Tools nodes run outside the editor — in cooked builds, packaged games, or server builds.

---

## What runs at runtime vs. editor-only

| Node | Runtime | Notes |
|---|---|---|
| Blue Noise Scatter | ✅ full | |
| Boundary Detect | ✅ full | |
| Clump Scatter | ✅ full | |
| Density Falloff | ✅ full | |
| Distance To Nearest Tag | ✅ full | |
| Grid Snap | ✅ full | |
| Height Filter | ✅ full | `LandscapeRef` must be assigned explicitly |
| Noise Mask Filter | ✅ full | |
| Print Stats | ✅ passthrough | Logs to Output Log in editor. In non-editor builds `bPrintEnabled` is effectively ignored and the node is always a silent passthrough |
| Project To Landscape | ✅ full | `LandscapeRef` must be assigned explicitly |
| Relax Points | ✅ full | |
| Slope Filter | ✅ full | |
| Spline Avoidance | ✅ full | `SplineActors` or `SplineActorTag` must be assigned |
| Align To Nearest Spline | ✅ full | `SplineActors` or `SplineActorTag` must be assigned |
| Water Body Avoidance | ✅ full | `WaterBodyActors` must be assigned; Water plugin must be enabled |
| Weighted Selection By Tag | ✅ full | |
| **Landscape Layer Sampler** | ⚠️ editor-only | In non-editor builds all points pass through unchanged. Do not rely on this node for runtime PCG generation |

---

## Explicit references are required in cooked builds

Several nodes support auto-discovery in the editor (scanning the world for actors). **This fallback does not work in cooked builds or async PCG execution.** Always assign references explicitly before packaging.

### Landscape nodes

`Project To Landscape` and `Height Filter` both have a `LandscapeRef` property:

1. Select the PCGVolume in the level.
2. In the Details panel, find the node settings.
3. Set `LandscapeRef` to your Landscape actor.

### Spline nodes

`Spline Avoidance` and `Align To Nearest Spline` have two options:

**Option A — Actor Tag (recommended):**
1. Add an Actor Tag to your spline actor (Details → Actor → Tags), e.g. `Road`.
2. Set `SplineActorTag = Road` on the node.

**Option B — Direct reference:**
1. Populate the `SplineActors` array on the node with explicit actor references.

### Water Body nodes

`Water Body Avoidance`:
1. Populate `WaterBodyActors` with explicit actor references.
2. Leave it empty only if you are certain the graph runs synchronously on the game thread (rare).

---

## Async PCG execution

UE 5.7 runs PCG graphs asynchronously by default. All PCG Pro Tools nodes are written to be async-safe **provided explicit references are set** (see above). The auto-discovery fallbacks (world scans, `TObjectIterator`) are explicitly guarded and will not run in async contexts — they fail silently and the node passes through all input points rather than crashing.

---

## Performance guards at runtime

The `DensityCapWarningThreshold` setting fires an **editor notification** only — it has no effect in cooked builds. The underlying `CheckDensityCap` / `CheckDensityCapFromContext` functions log to `LogPCGProKit` in all build types if you call them from custom code.

---

## Deterministic mode

When `bDeterministicMode` is enabled in Project Settings, all PCG Pro Tools nodes that use randomness (`Blue Noise Scatter`, `Clump Scatter`, `Weighted Selection By Tag`) use `DefaultRandomSeed` as their base seed instead of the graph's runtime seed. Each node still offsets by its own node index to avoid identical patterns across nodes.

This setting is stored in `Config/DefaultEditor.ini` and applies in editor only. For runtime determinism, use the standard PCG graph seed mechanism on the PCGComponent.

---

## Packaging checklist

Before packaging a project that uses PCG Pro Tools:

- [ ] `LandscapeRef` assigned on all Project To Landscape and Height Filter nodes that use landscape reference mode
- [ ] `SplineActorTag` or `SplineActors` assigned on all Spline Avoidance and Align To Nearest Spline nodes
- [ ] `WaterBodyActors` assigned on all Water Body Avoidance nodes (or confirmed sync-only execution)
- [ ] All `Print Stats` nodes have `bPrintEnabled = false` (or are removed)
- [ ] Landscape Layer Sampler nodes are not relied on for runtime generation
- [ ] Plugin content (templates, presets) that you customised has been duplicated into your project content — plugin content is not always included in a package by default

---

Next: [`07_API_Reference.md`](07_API_Reference.md)
