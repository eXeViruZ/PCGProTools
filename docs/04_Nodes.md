<!-- Copyright (c) 2026 Tom Leon Vincent Hanke -->

# 04 — Node Reference

Custom PCG nodes shipped by PCGProKit. All nodes appear in the PCG Graph editor palette under the **PCGProKit** category. Point flows use standard `UPCGPointArrayData`.

---

## 4.1 Node Overview

| Node | Purpose | Key Properties |
|---|---|---|
| ProjectToLandscape | Z-snap points to landscape surface | `LandscapeRef`, `bAlignToNormal`, `ZOffset` |
| AlignToNearestSpline | Snap + align points to nearest spline | `SplineActors`, `MaxSnapDistance`, `bAlignToTangent` |
| BoundaryDetect | Classify interior / edge / exterior | `EdgeThreshold`, `AttributeName` |
| Distance To Nearest Tag | Write distance-to-nearest-tagged-point attribute | `SearchTarget`, `TargetTag`, `OutputAttributeName`, `MaxSearchDistance` |
| GridSnap | Snap point positions to a grid (+ dedupe) | `GridSize`, `GridOrigin`, `bDedupe` |
| SurfaceSlopeFilter | Filter points by surface slope angle | `MinSlopeDeg`, `MaxSlopeDeg`, `SlopeSource`, `bInvertFilter` |
| HeightFilter | Filter points by world-space Z height | `MinZ`, `MaxZ`, `bUseLandscapeReference`, `LandscapeRef`, `bInvertFilter` |
| BlueNoiseScatter | Poisson-disk thinning (O(n) spatial hash) | `MinDistance`, `MaxAttempts`, `MaxPoints` |
| Weighted Selection By Tag | Weighted random selection from tagged data sets | `TagWeights (TMap<FName,float>)`, `SelectionCount` |
| Density Falloff | Modulate point density by distance from a center | `Center`, `Radius`, `FalloffMode`, `FalloffCurve` |

---

## 4.2 ProjectToLandscape

Projects incoming points onto the landscape surface (Z-snap, optional normal alignment).

**Pins**
- In: `In` — points to project.
- Out: `Out` — projected points.

**Properties**
| Property | Type | Default | Description |
|---|---|---|---|
| `LandscapeRef` | `TSoftObjectPtr<ALandscape>` | None | Target landscape. Optional (editor fallback). |
| `bAlignToNormal` | `bool` | false | Rotate points to match the surface normal. |
| `ZOffset` | `float` (cm) | 0.0 | Additive Z offset after projection. |

**Editor fallback**
If `LandscapeRef` is empty, the node searches `Context->InputData` for a `UPCGLandscapeData`. Convenience only — **set `LandscapeRef` explicitly for runtime / cooked builds**.

**Notes**
- Points outside the landscape footprint are passed through unchanged.
- Typical pairing: scatter points in 2D → `ProjectToLandscape` → spawn meshes.

---

## 4.3 AlignToNearestSpline

Snaps each incoming point to the nearest point on the nearest spline actor; optionally aligns rotation to the spline tangent.

**Pins**
- In: `In` — points to align.
- Out: `Out` — aligned points.

**Properties**
| Property | Type | Default | Description |
|---|---|---|---|
| `SplineActors` | `TArray<TSoftObjectPtr<AActor>>` | empty | Candidate actors. Must contain a `USplineComponent`. |
| `MaxSnapDistance` | `float` (cm) | 500.0 | Points beyond this distance are skipped (pass-through). |
| `bAlignToTangent` | `bool` | false | Rotate points to face along the spline tangent. |

**Editor fallback**
If `SplineActors` is empty, the node iterates the current world on the **game thread** and collects up to **16** actors with a `USplineComponent`. Editor convenience only — set `SplineActors` explicitly for runtime.

**Complexity**
O(actors × splines × points) in the naive path. Keep the actor list bounded (hence the 16-actor fallback cap).

**Edge cases**
- Empty splines or splines with < 2 points are skipped.
- Ties on distance resolve to the first encountered spline.

---

## 4.4 BoundaryDetect

Classifies points as *interior* / *edge* / *exterior* of a point cluster and writes the result to a metadata attribute.

**Pins**
- In: `In` — source points.
- Out: `Out` — same points with an added attribute.

**Properties**
| Property | Type | Default | Description |
|---|---|---|---|
| `EdgeThreshold` | `float` (cm) | 200.0 | Radius used for edge classification. Larger = thicker ring. |
| `AttributeName` | `FName` | `Boundary` | Name of the written `int32` attribute. Values: 0=interior, 1=edge, 2=exterior. |

**PCG 5.7 implementation note**
Metadata writes call `Metadata->InitializeOnSet(WriteRanges.MetadataEntryRange[i])` **before** `Attr->SetValue(...)`. This is mandatory in PCG 5.7 — omitting it triggers the engine assert in `PCGMetadataAttributeTpl.h:587`. Any custom extension you write must follow the same pattern.

**Typical usage**
Feed the resulting `Boundary` attribute into an **Attribute Filter** or **Density Filter** to thin interior points while preserving the silhouette.

---

## 4.5 Distance To Nearest Tag

For each input point finds the nearest point among tagged data sets on a secondary pin and writes the distance as a float metadata attribute.

**Pins**
- In: `In` — points to annotate.
- In: `Tagged` — one or more tagged point data sets to search against.
- Out: `Out` — `In` with added distance attribute.

**Properties**
| Property | Type | Default | Description |
|---|---|---|---|
| `SearchTarget` | `EPCGSearchTarget` | `AllTagged` | `AllTagged` = search all tagged inputs. `SpecificTag` = only the data set whose tag matches `TargetTag`. |
| `TargetTag` | `FName` | None | Used only when `SearchTarget == SpecificTag`. |
| `OutputAttributeName` | `FName` | `NearestTagDistance` | Name of the written float attribute. |
| `MaxSearchDistance` | `float` (cm) | 0 | Points farther than this are excluded. `0` = no limit. |

**Typical usage**
Biome transitions: write distance to anchor points (roads, water) and use the attribute to blend mesh pools. Drives `PCGT_BiomeTransition`.

---

## 4.6 GridSnap

Snaps point positions to a uniform grid; optionally deduplicates per cell.

**Pins**
- In: `In` — points to snap.
- Out: `Out` — snapped points.

**Properties**
| Property | Type | Default | Description |
|---|---|---|---|
| `GridSize` | `FVector` (cm) | `(200, 200, 200)` | Cell size per axis. Set an axis to 0 to disable snap on that axis. |
| `GridOrigin` | `FVector` (cm) | `(0, 0, 0)` | Origin of the snap grid. |
| `bDedupe` | `bool` | false | Keep only one point per occupied cell. |

**Determinism**
Deterministic in input order when `bDedupe = false`. With dedupe, the first point per cell wins.

---

## 4.7 Common Gotchas for Custom-Node Authors

- **Actor soft refs require fallbacks or explicit setters.** Unset `TSoftObjectPtr<AActor>` at runtime without a fallback path crashes UX. PCGProKit's nodes ship with editor fallbacks; mimic the pattern or require explicit assignment.
- **Metadata entries must be initialized.** Call `Metadata->InitializeOnSet(EntryKey)` before `SetValue()` on newly created entries in PCG 5.7.
- **Auto-discovery `Input → SplineSampler.Spline` is unreliable** in PCG 5.7. Prefer `Get Spline Data` with an Actor-Tag filter.
- **World iteration for fallback** must stay on the game thread and be capped (PCGProKit uses 16 actors).

---

---

## 4.8 SurfaceSlopeFilter

Filters points by surface slope derived from each point's Z-axis orientation. Only points within `[MinSlopeDeg, MaxSlopeDeg]` are kept.

**Pins**
- In: `In` — points to filter.
- Out: `Out` — filtered points.

**Properties**
| Property | Type | Default | Description |
|---|---|---|---|
| `MinSlopeDeg` | `float` (°) | 0.0 | Minimum slope to keep. 0 = flat. |
| `MaxSlopeDeg` | `float` (°) | 45.0 | Maximum slope to keep. 90 = vertical wall. |
| `SlopeSource` | `EPCGSlopeSource` | `PointNormal` | Source of slope. `PointNormal` uses the point's Z-axis (works for any projected-to-surface data). `LandscapeQuery` is reserved for a future release. |
| `bInvertFilter` | `bool` | false | Keep points **outside** the slope range instead. |

**Notes**
- Requires the points to have been projected to a surface first (e.g., via `ProjectToLandscape` with `bAlignToNormal = true`). Points with no rotation have slope = 0.
- Typical use: scatter vegetation only on moderate slopes (e.g., `MinSlopeDeg=5`, `MaxSlopeDeg=35`); exclude cliff faces and flat ground.
- Used in `PCGT_HillsideVegetation` to isolate hillside terrain.

---

## 4.9 HeightFilter

Filters points by world-space Z height. Optionally relative to the landscape height at each XY location.

**Pins**
- In: `In` — points to filter.
- Out: `Out` — filtered points.

**Properties**
| Property | Type | Default | Description |
|---|---|---|---|
| `MinZ` | `float` (cm) | -1 000 000 | Minimum Z to keep. |
| `MaxZ` | `float` (cm) | 1 000 000 | Maximum Z to keep. |
| `bUseLandscapeReference` | `bool` | false | If true, `MinZ`/`MaxZ` are **relative** to the landscape height at each point's XY. |
| `LandscapeRef` | `TSoftObjectPtr<ALandscapeProxy>` | None | Landscape used when `bUseLandscapeReference = true`. |
| `bInvertFilter` | `bool` | false | Keep points outside the Z range instead. |

**Notes**
- With `bUseLandscapeReference = false`: absolute world Z. Useful for altitude bands (e.g., snow above 5000 cm).
- With `bUseLandscapeReference = true`: relative height above ground. Useful for filtering floating/sunken points.
- For **runtime** always set `LandscapeRef` explicitly when `bUseLandscapeReference = true`.

---

## 4.10 BlueNoiseScatter

Poisson-disk (Bridson-like) thinning filter. Removes points that are closer than `MinDistance` to an already-kept point, producing an even, non-clumping distribution. Uses a 2D spatial hash grid for O(n) performance.

**Pins**
- In: `In` — points to thin.
- Out: `Out` — thinned points.

**Properties**
| Property | Type | Default | Description |
|---|---|---|---|
| `MinDistance` | `float` (cm) | 100.0 | Minimum distance between any two kept points. |
| `MaxAttempts` | `int32` | 30 | Rejection attempts per candidate (Bridson). Higher = denser packing but slower. Range: 1–100. |
| `MaxPoints` | `int32` | 100 000 | Hard output cap. Prevents runaway output on extreme inputs. Range: 1–1 000 000. |

**Notes**
- Seed-dependent (`UseSeed() = true`). Different seeds produce different valid distributions.
- Unlike a simple random thin, blue-noise guarantees minimum spacing → no clumps, no voids. Ideal for natural vegetation scatter.
- Used in `PCGT_HillsideVegetation` as the final scatter step before the mesh spawner.
- Performance: O(n) via spatial hash. `MinDistance` does not change complexity but tighter spacing reduces the accepted set.

---

## 4.11 Weighted Selection By Tag

Selects points from multiple tagged data sets by weighted random sampling. `SelectionCount` total points are drawn proportionally from all inputs based on their tag weights.

**Pins**
- In: `In` — multiple tagged point data sets.
- Out: `Out` — selected subset of points.

**Properties**
| Property | Type | Default | Description |
|---|---|---|---|
| `TagWeights` | `TMap<FName, float>` | empty | Maps a tag name (from `FPCGTaggedData.Tags`) to its relative selection weight. Data sets with no matching tag use weight `1.0`. |
| `SelectionCount` | `int32` | 100 | Total number of points to output. Clamped to the available input count. |

**Notes**
- Seed-dependent (`UseSeed() = true`).
- Use case: pick from multiple biome mesh pools with different probabilities. E.g., tag `Trees` weight `3.0`, tag `Rocks` weight `1.0` → 75% trees, 25% rocks from a combined 100 output points.
- Data sets with no tag entry in `TagWeights` fall back to weight `1.0` (equal chance).

---

## 4.12 Density Falloff

Modifies point density based on distance from a world-space center point. Points within `Radius` have their density multiplied by the chosen falloff function; points beyond `Radius` are unchanged.

**Pins**
- In: `In` — points to modify.
- Out: `Out` — same points with modified density values.

**Properties**
| Property | Type | Default | Description |
|---|---|---|---|
| `Center` | `FVector` (cm) | `(0, 0, 0)` | World-space origin of the falloff effect. |
| `Radius` | `float` (cm) | 1000.0 | Points beyond this radius are not affected. |
| `FalloffMode` | `EPCGFalloffMode` | `Linear` | `Linear`: density *= 1 - (dist/Radius). `Exponential`: density *= exp(-3 × dist/Radius). `Curve`: density *= curve evaluated at normalized distance. |
| `FalloffCurve` | `FRuntimeFloatCurve` | — | Custom curve. X = normalized distance [0..1], Y = density multiplier [0..1]. Only used when `FalloffMode == Curve`. |

**Notes**
- Density values are clamped to `[0, 1]` after the multiplication.
- Combine with a Density Filter node after this node to remove zero-density points.
- `Exponential` mode produces a natural-looking soft edge. `Linear` is sharp. `Curve` gives full artistic control.
- Useful for clearing a radius around a landmark (e.g., no trees near a building center).

---

**Next:** [05_Presets](05_Presets.md)
