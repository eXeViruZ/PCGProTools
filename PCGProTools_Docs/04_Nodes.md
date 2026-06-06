# 04 — Nodes

PCG Pro Tools v1.1 ships **17 custom C++ nodes**. All nodes appear in the PCG Graph node palette under the **PCG Pro:** prefix. All overridable properties are marked `PCG_Overridable` and can be exposed to a Graph Instance.

---

## Quick reference

| Node | Class | Category | Seed |
|---|---|---|---|
| Blue Noise Scatter | `UPCGBlueNoiseScatterSettings` | Filter | yes |
| Boundary Detect | `UPCGBoundaryDetectSettings` | Metadata | no |
| Clump Scatter | `UPCGClumpScatterSettings` | Sampler | yes (per-clump) |
| Density Falloff | `UPCGDensityFalloffSettings` | Density | no |
| Distance To Nearest Tag | `UPCGDistanceToNearestTagSettings` | Metadata | no |
| Grid Snap | `UPCGGridSnapSettings` | Spatial | no |
| Height Filter | `UPCGHeightFilterSettings` | Filter | no |
| Landscape Layer Sampler | `UPCGLandscapeLayerSamplerSettings` | Filter | no |
| Noise Mask Filter | `UPCGNoiseMaskFilterSettings` | Filter | no |
| Print Stats | `UPCGPrintStatsSettings` | Debug | no |
| Project To Landscape | `UPCGProjectToLandscapeSettings` | Spatial | no |
| Relax Points | `UPCGRelaxPointsSettings` | Spatial | no |
| Slope Filter | `UPCGCurvatureFilterSettings` | Filter | no |
| Spline Avoidance | `UPCGSplineAvoidanceSettings` | Filter | no |
| Align To Nearest Spline | `UPCGAlignToNearestSplineSettings` | Spatial | no |
| Water Body Avoidance | `UPCGWaterBodyAvoidanceSettings` | Filter | no |
| Weighted Selection By Tag | `UPCGWeightedSelectionByTagSettings` | Filter | yes |

---

## Blue Noise Scatter
**Display name:** PCG Pro: Blue Noise Scatter  
**Class:** `UPCGBlueNoiseScatterSettings`  
**Category:** Filter  
**Pins:** Points In → Points Out

Poisson-disk thinning filter. Removes points that are closer than `MinDistance` to an already-kept point, using a 2D spatial hash grid (O(n)). The result is a well-distributed set with no tight clusters.

| Property | Type | Default | Description |
|---|---|---|---|
| `MinDistance` | float (cm) | 100 | Minimum distance between any two kept points |
| `MaxAttempts` | int [1–100] | 30 | Bridson rejection attempts per point. Higher = denser packing |
| `MaxPoints` | int [1–1 000 000] | 100 000 | Hard output cap. Prevents runaway output on large inputs |

**Tip:** Pipe this into Relax Points for an even higher-quality distribution.

---

## Boundary Detect
**Display name:** PCG Pro: Boundary Detect  
**Class:** `UPCGBoundaryDetectSettings`  
**Category:** Metadata  
**Pins:** Points In → Points Out

Marks each point as a boundary or interior point. A point is a boundary if it has fewer than `MinNeighborCount` neighbors within `NeighborRadius`. Uses a spatial octree (O(n log n)). Result is written as a bool attribute.

| Property | Type | Default | Description |
|---|---|---|---|
| `NeighborRadius` | float (cm) | 200 | Search radius for neighbor counting |
| `MinNeighborCount` | int ≥ 1 | 3 | Points with fewer neighbors than this are marked as boundary |
| `OutputAttributeName` | FName | `bIsBoundary` | Name of the output bool attribute |

**Tip:** Filter downstream on `bIsBoundary == true` to place fence posts, border stones, or edge-specific assets.

---

## Clump Scatter
**Display name:** PCG Pro: Clump Scatter  
**Class:** `UPCGClumpScatterSettings`  
**Category:** Sampler  
**Pins:** Points In → Points Out

Converts each input point into a natural-looking clump of child points. Each input point becomes a clump center; `ClumpSize` child points are scattered within `ClumpRadius` using uniform disk sampling. Each clump uses a deterministic seed derived from the graph seed + point index.

| Property | Type | Default | Description |
|---|---|---|---|
| `ClumpSize` | int [1–64] | 6 | Number of child points per input point |
| `ClumpRadius` | float (cm) | 200 | Maximum distance from clump center |
| `bScaleFalloff` | bool | true | Scale child points down near the clump edge |
| `MinEdgeScale` | float [0–1] | 0.4 | Scale multiplier at the clump edge when `bScaleFalloff` is on |
| `bKeepSourcePoint` | bool | false | Include the original input (center) point in the output |

**Use case:** turn a sparse Blue Noise scatter into organic tree clusters, rock formations, or flower patches.

---

## Density Falloff
**Display name:** PCG Pro: Density Falloff  
**Class:** `UPCGDensityFalloffSettings`  
**Category:** Density  
**Pins:** Points In → Points Out

Multiplies each point's density by a falloff function based on its distance from a center point. Points beyond `Radius` are unchanged.

| Property | Type | Default | Description |
|---|---|---|---|
| `bCenterRelativeToVolume` | bool | true | If true, `Center` is relative to the PCGVolume origin |
| `Center` | FVector | (0,0,0) | Center of the falloff effect |
| `Radius` | float (cm) | 1000 | Radius beyond which density is unchanged |
| `FalloffMode` | enum | Linear | `Linear`, `Exponential`, or `Curve` |
| `FalloffCurve` | FRuntimeFloatCurve | — | Custom curve (X = normalised distance [0,1], Y = density multiplier). Only active when `FalloffMode == Curve` |

---

## Distance To Nearest Tag
**Display name:** PCG Pro: Distance To Nearest Tag  
**Class:** `UPCGDistanceToNearestTagSettings`  
**Category:** Metadata  
**Pins:** Points In, Tagged Data In → Points Out

For each input point, finds the nearest point in the tagged secondary dataset and writes the distance (cm) as a float attribute. Uses a secondary input pin for the reference dataset.

| Property | Type | Default | Description |
|---|---|---|---|
| `SearchTarget` | enum | AllTagged | `AllTagged` — search all tagged inputs; `SpecificTag` — search only the dataset whose tag matches `TargetTag` |
| `TargetTag` | FName | None | Tag to search (only when `SearchTarget == SpecificTag`) |
| `OutputAttributeName` | FName | `NearestTagDistance` | Name of the output float attribute |
| `MaxSearchDistance` | float (cm) | 0 | Points farther than this are ignored. 0 = no limit |

---

## Grid Snap
**Display name:** PCG Pro: Grid Snap  
**Class:** `UPCGGridSnapSettings`  
**Category:** Spatial  
**Pins:** Points In → Points Out

Snaps each point's position to the nearest cell of a world-space grid. Optionally snaps yaw rotation to the nearest 90° increment.

| Property | Type | Default | Description |
|---|---|---|---|
| `GridSize` | float (cm) | 100 | Cell size. All axes use the same size |
| `GridOrigin` | FVector | (0,0,0) | World-space origin of the grid |
| `bSnapRotationToGrid` | bool | false | Snap yaw to nearest 90°. Pitch and roll are unchanged |

---

## Height Filter
**Display name:** PCG Pro: Height Filter  
**Class:** `UPCGHeightFilterSettings`  
**Category:** Filter  
**Pins:** Points In → Points Out

Filters points by world-space Z height. Optionally treats `MinZ`/`MaxZ` as offsets from the Landscape surface at each XY position.

| Property | Type | Default | Description |
|---|---|---|---|
| `MinZ` | float (cm) | -1 000 000 | Minimum world Z to keep |
| `MaxZ` | float (cm) | 1 000 000 | Maximum world Z to keep |
| `bUseLandscapeReference` | bool | false | If true, `MinZ`/`MaxZ` are added on top of the landscape height at each XY |
| `LandscapeRef` | `TSoftObjectPtr<ALandscapeProxy>` | None | The landscape to use as height reference |

> **Cooked builds:** always assign `LandscapeRef` explicitly when `bUseLandscapeReference` is true.

---

## Landscape Layer Sampler
**Display name:** PCG Pro: Landscape Layer Sampler  
**Class:** `UPCGLandscapeLayerSamplerSettings`  
**Category:** Filter  
**Pins:** Points In → Points Out

Filters and modulates point density based on a Landscape paint-layer weight. Editor-only: in non-editor builds all points pass through unchanged.

| Property | Type | Default | Description |
|---|---|---|---|
| `LayerName` | FName | None | Exact name of the Landscape paint layer. Must match the `Layer Name` on the `ULandscapeLayerInfoObject` asset |
| `MinWeight` | float [0–1] | 0.1 | Points with sampled weight below this are removed |
| `bModulateDensity` | bool | true | Multiply surviving point density by the sampled weight |
| `bInvertFilter` | bool | false | Keep only points with weight **below** `MinWeight` |
| `LandscapeRef` | `TSoftObjectPtr<ALandscapeProxy>` | None | Optional explicit landscape. Leave empty to auto-discover. Recommended for World Partition or multi-landscape levels |

> **World Partition:** the node samples paint-layer weight across **all** landscape streaming proxies, so it works correctly on partitioned landscapes. If your level has more than one landscape, set `LandscapeRef` to disambiguate which one to sample.

**Tip:** Use `bInvertFilter = true` to spawn rocks on non-grass areas.

---

## Noise Mask Filter
**Display name:** PCG Pro: Noise Mask Filter  
**Class:** `UPCGNoiseMaskFilterSettings`  
**Category:** Filter  
**Pins:** Points In → Points Out

Samples 2D Perlin noise at each point's XY world position and removes points whose noise value falls below `Threshold`. Creates organic, non-uniform density variation.

| Property | Type | Default | Description |
|---|---|---|---|
| `NoiseScale` | float | 0.003 | World-space frequency. Lower = larger blobs, higher = finer grain |
| `Threshold` | float [0–1] | 0.5 | Points with noise below this are removed. 0.5 ≈ 50% survive |
| `FalloffWidth` | float [0–0.5] | 0.1 | Soft band around the threshold. Points within this range get density scaled instead of removed |
| `NoiseOffset` | FVector2D | (0,0) | World-space XY shift of the noise pattern |
| `bInvertMask` | bool | false | Keep only points with noise **below** threshold |

---

## Print Stats
**Display name:** PCG Pro: Print Stats  
**Class:** `UPCGPrintStatsSettings`  
**Category:** Debug  
**Pins:** Points In → Points Out (passthrough — input is never modified)

Logs point count, XY bounds, Z range, and density range to the Output Log. Zero effect on the output data.

| Property | Type | Default | Description |
|---|---|---|---|
| `bPrintEnabled` | bool | true | When false, the node is a silent passthrough |
| `Label` | FString | `PCG Stats` | Prefix for the log line. Use unique labels when multiple nodes are in one graph |
| `bLogPointCount` | bool | true | Log total point count |
| `bLogBounds` | bool | true | Log XY bounding box |
| `bLogZRange` | bool | true | Log min/max Z |
| `bLogDensityRange` | bool | true | Log min/max density |

**Workflow:** place between nodes, label each checkpoint, force-regen, read the Output Log. Disable all `bPrintEnabled` flags before shipping.

---

## Project To Landscape
**Display name:** PCG Pro: Project To Landscape  
**Class:** `UPCGProjectToLandscapeSettings`  
**Category:** Spatial  
**Pins:** Points In → Points Out

Projects each point vertically onto a Landscape surface. Optionally aligns the point's rotation to the surface normal (finite-difference approach).

| Property | Type | Default | Description |
|---|---|---|---|
| `LandscapeRef` | `TSoftObjectPtr<ALandscapeProxy>` | None | The landscape to project onto |
| `ZOffset` | float (cm) | 0 | Vertical offset added after projection (prevents z-fighting) |
| `bProjectRotation` | bool | false | Align point rotation to the landscape surface normal |

> **Cooked builds:** always assign `LandscapeRef` explicitly.

---

## Relax Points
**Display name:** PCG Pro: Relax Points  
**Class:** `UPCGRelaxPointsSettings`  
**Category:** Spatial  
**Pins:** Points In → Points Out

Approximates Lloyd's algorithm: iteratively nudges each point toward the centroid of its neighbors within `SearchRadius`. After several iterations the result is a well-spaced, organic distribution.

| Property | Type | Default | Description |
|---|---|---|---|
| `Iterations` | int [1–10] | 2 | Number of relaxation passes. 1–3 is usually sufficient |
| `SearchRadius` | float (cm) | 300 | Neighbor lookup radius. ~1.5–2× desired minimum spacing |
| `RelaxStrength` | float [0–1] | 0.5 | Blend factor toward neighbor centroid per iteration. 0 = no movement, 1 = full snap |

**Performance:** O(N × AvgNeighbors × Iterations). Spatial grid avoids O(N²) brute force.

---

## Slope Filter
**Display name:** PCG Pro: Slope Filter  
**Class:** `UPCGCurvatureFilterSettings`  
**Category:** Filter  
**Pins:** Points In → Points Out

Filters points by terrain slope angle. Reads the surface normal from the point's Transform Z-axis (set correctly by the PCG Surface Sampler on a Landscape). Slope angle = angle between the surface normal and the world up vector.

> **v1.0 users:** this node was called `SurfaceSlopeFilter` in v1.0. The C++ class is `UPCGCurvatureFilterSettings`.

| Property | Type | Default | Description |
|---|---|---|---|
| `MinAngle` | float [0–90°] | 0 | Points with slope below this are removed |
| `MaxAngle` | float [0–90°] | 30 | Points with slope above this are removed |
| `FalloffAngle` | float [0–45°] | 0 | Soft density falloff band at both slope boundaries. 0 = hard cut |
| `bInvertFilter` | bool | false | Keep only points **outside** [MinAngle, MaxAngle] |

**Tip:** 0–25° = flat ground (trees), 50–90° = steep faces (cliff rocks), 30–60° = mid slopes (shrubs).

---

## Spline Avoidance
**Display name:** PCG Pro: Spline Avoidance  
**Class:** `UPCGSplineAvoidanceSettings`  
**Category:** Filter  
**Pins:** Points In → Points Out

Removes or attenuates points that are within `AvoidanceRadius` of any spline on the specified actors. Soft falloff smoothly reduces density in the transition zone.

| Property | Type | Default | Description |
|---|---|---|---|
| `SplineActorTag` | FName | None | Actor Tag used to find spline actors automatically. Takes priority over `SplineActors`. Works in async execution |
| `SplineActors` | `TArray<TSoftObjectPtr<AActor>>` | empty | Explicit spline actor references. Always set this in async execution |
| `AvoidanceRadius` | float (cm) | 500 | Hard avoidance radius. Points closer than this are removed |
| `FalloffRadius` | float (cm) | 0 | Soft zone beyond `AvoidanceRadius`. Points here get density scaled down. 0 = hard edge |
| `bInvertSelection` | bool | false | Keep **only** points inside `AvoidanceRadius` (roadside/riverbank mode) |

---

## Align To Nearest Spline
**Display name:** PCG Pro: Align To Nearest Spline  
**Class:** `UPCGAlignToNearestSplineSettings`  
**Category:** Spatial  
**Pins:** Points In → Points Out

Aligns each point's rotation toward the tangent of the nearest spline. Uses the engine SplineComponent API to find the closest point and tangent.

| Property | Type | Default | Description |
|---|---|---|---|
| `SplineActorTag` | FName | None | Actor Tag used to find spline actors automatically. Takes priority over `SplineActors` |
| `SplineActors` | `TArray<TSoftObjectPtr<AActor>>` | empty | Explicit spline actor references. Always set in async execution |

> **Important:** In async PCG execution (the default), always assign `SplineActorTag` or populate `SplineActors`. The auto-discovery world scan only works in synchronous mode.

---

## Water Body Avoidance
**Display name:** PCG Pro: Water Body Avoidance  
**Class:** `UPCGWaterBodyAvoidanceSettings`  
**Category:** Filter  
**Pins:** Points In → Points Out

Removes or attenuates points within `AvoidanceRadius` of any UE Water body spline. Works with all water body types: River, Lake, Ocean, Custom. No hard dependency on the Water plugin — discovered via reflection. If the Water plugin is not enabled, all points pass through unchanged.

| Property | Type | Default | Description |
|---|---|---|---|
| `WaterBodyActors` | `TArray<TSoftObjectPtr<AActor>>` | empty | Explicit water body actor references. Leave empty for auto-discovery (synchronous only) |
| `AvoidanceRadius` | float (cm) | 500 | Hard avoidance radius |
| `FalloffRadius` | float (cm) | 0 | Soft falloff zone beyond `AvoidanceRadius`. 0 = hard edge |
| `bInvertSelection` | bool | false | Keep **only** points inside `AvoidanceRadius` (riverbank/lakeshore mode) |

---

## Weighted Selection By Tag
**Display name:** PCG Pro: Weighted Selection By Tag  
**Class:** `UPCGWeightedSelectionByTagSettings`  
**Category:** Filter  
**Pins:** Points In → Points Out

Selects `SelectionCount` points from multiple tagged input datasets by weighted random sampling. Data sets with no matching tag use weight 1.0.

| Property | Type | Default | Description |
|---|---|---|---|
| `TagWeights` | `TMap<FName, float>` | empty | Maps a tag name (from `FPCGTaggedData.Tags`) to its relative selection weight |
| `SelectionCount` | int ≥ 1 | 100 | Total number of output points. Clamped to available input count |

**Example:** `TagWeights = { Oak: 3.0, Pine: 1.0 }` → oak appears ~75% of the time, pine ~25%.

---

Next: [`05_Presets.md`](05_Presets.md)
