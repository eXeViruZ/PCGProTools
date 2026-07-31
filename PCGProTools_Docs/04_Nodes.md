# 04 — Nodes

PCG Pro Tools v2.0.0 includes **22 custom C++ PCG nodes** for Unreal Engine 5.8.

All nodes appear under the **PCG Pro:** prefix. Supported node properties marked `PCG_Overridable` can be edited in the Graph Inspector and captured by the preset system.

---

## Quick reference

| Node | Settings class | Category | Pins | Runtime | Seed |
|---|---|---|---|---|---|
| Instance Variation | `UPCGInstanceVariationSettings` | Filter | Points → Points | Full | Yes |
| Spline Offset | `UPCGSplineOffsetSettings` | Spatial | Points → Points | Full | Yes |
| Distance LOD | `UPCGDistanceLODSettings` | Filter | Points → Points | Full | No |
| Random Subset | `UPCGRandomSubsetSettings` | Filter | Points → Points | Full | Yes |
| Biome Mask | `UPCGBiomeMaskSettings` | Filter | Points → Points | Full | No |
| Blue Noise Scatter | `UPCGBlueNoiseScatterSettings` | Scatter | Points → Points | Full | Yes |
| Boundary Detect | `UPCGBoundaryDetectSettings` | Filter | Points → Points | Full | No |
| Clump Scatter | `UPCGClumpScatterSettings` | Scatter | Points → Points | Full | Yes |
| Density Falloff | `UPCGDensityFalloffSettings` | Filter | Points → Points | Full | No |
| Distance To Nearest Tag | `UPCGDistanceToNearestTagSettings` | Filter | Points + Target → Points | Full | No |
| Grid Snap | `UPCGGridSnapSettings` | Scatter | Points → Points | Full | No |
| Height Filter | `UPCGHeightFilterSettings` | Filter | Points → Points | Full | No |
| Landscape Layer Sampler | `UPCGLandscapeLayerSamplerSettings` | Landscape | Points → Points | Editor-only; cooked passthrough | No |
| Noise Mask Filter | `UPCGNoiseMaskFilterSettings` | Filter | Points → Points | Full | Yes |
| Print Stats | `UPCGPrintStatsSettings` | Utility | Points → Points | Passthrough outside editor logging | No |
| Project To Landscape | `UPCGProjectToLandscapeSettings` | Landscape | Points → Points | Full | No |
| Relax Points | `UPCGRelaxPointsSettings` | Filter | Points → Points | Full | No |
| Curvature Filter | `UPCGCurvatureFilterSettings` | Filter | Points → Points | Full | No |
| Spline Avoidance | `UPCGSplineAvoidanceSettings` | Filter | Points → Points | Full, main thread | No |
| Align To Nearest Spline | `UPCGAlignToNearestSplineSettings` | Spatial | Points → Points | Full, main thread | No |
| Water Body Avoidance | `UPCGWaterBodyAvoidanceSettings` | Filter | Points → Points | Full, main thread | No |
| Weighted Selection By Tag | `UPCGWeightedSelectionByTagSettings` | Filter | Points → Points | Full | Yes |

---

# New v2.0.0 nodes

## Instance Variation

**Display name:** PCG Pro: Instance Variation  
**Class:** `UPCGInstanceVariationSettings`  
**Pins:** Points → Points  
**Threading:** Async-capable

Randomizes transform scale, rotation, and point color. Density is not changed.

| Property | Type | Default | Range | Description |
|---|---|---:|---:|---|
| `bRandomizeScale` | bool | true | — | Enable scale variation |
| `ScaleMin` | float | 0.8 | 0.01–10 | Minimum scale multiplier |
| `ScaleMax` | float | 1.2 | 0.01–10 | Maximum scale multiplier |
| `bIndependentAxes` | bool | false | — | Randomize X, Y, and Z independently |
| `bRandomizeRotation` | bool | false | — | Enable rotation jitter |
| `YawJitter` | float | 180 | 0–180° | Adds random ± yaw |
| `PitchJitter` | float | 10 | 0–90° | Adds random ± pitch |
| `RollJitter` | float | 180 | 0–180° | Adds random ± roll |
| `bRandomizeColor` | bool | true | — | Enable HSV-derived color variation |
| `ColorHueMin` | float | 0.0 | 0–1 | Minimum hue |
| `ColorHueMax` | float | 0.1 | 0–1 | Maximum hue |
| `ColorSaturationMin` | float | 0.7 | 0–1 | Minimum saturation |
| `ColorSaturationMax` | float | 1.0 | 0–1 | Maximum saturation |
| `ColorValueMin` | float | 0.7 | 0–1 | Minimum value |
| `ColorValueMax` | float | 1.0 | 0–1 | Maximum value |
| `DefaultTint` | FLinearColor | White | — | Default RGB tint |

### Rotation behavior

There is no separate Yaw Only switch. Configure yaw-only rotation with:

```text
YawJitter > 0
PitchJitter = 0
RollJitter = 0
```

### Point color and material setup

The node writes RGBA into the PCG point Color range:

```text
R, G, B, A
```

Alpha is `1.0`.

It does not create a metadata attribute and does not directly write `PerInstanceCustomData`.

To transfer point color to spawned instances:

1. Enable `bApplyColorAsPerInstanceCustomData` in the Static Mesh Spawner.
2. In the material, read **Per Instance Custom Data** indices:
   - 0 = R
   - 1 = G
   - 2 = B
3. Combine the values and multiply them with the material Base Color.

The workflow is compatible with ISM/HISM spawning when the spawner and material are configured to consume the data.

---

## Spline Offset

**Display name:** PCG Pro: Spline Offset  
**Class:** `UPCGSplineOffsetSettings`  
**Enum:** `EPCGSplineOffsetSideMode` (`Both`, `LeftOnly`, `RightOnly`)  
**Pins:** Points → Points  
**Threading:** Async-capable

Moves existing input points laterally using the point forward direction produced by a spline sampling workflow. It does not create additional points.

| Property | Type | Default | Range | Description |
|---|---|---:|---:|---|
| `ScatterWidth` | float | 2000 cm | 10–100000 | Full width used in Both mode |
| `CenterDensity` | float | 1.0 | 0–1 | Density at the spline center |
| `EdgeDensity` | float | 0.2 | 0–1 | Density at the selected side's outer edge |
| `bOffsetPerpendicular` | bool | true | — | Offset perpendicular to point forward |
| `bRandomizeOffset` | bool | true | — | Randomize lateral offset |
| `SideMode` | enum | Both | — | Both, Left Only, or Right Only |
| `LeftWidth` | float | 1000 cm | 10–100000 | Width used by Left Only |
| `RightWidth` | float | 1000 cm | 10–100000 | Width used by Right Only |

### Width behavior

| Side mode | Width used | Offset range |
|---|---|---|
| Both | `ScatterWidth` | `-ScatterWidth/2` to `+ScatterWidth/2` |
| Left Only | `LeftWidth` | `-LeftWidth` to `0` |
| Right Only | `RightWidth` | `0` to `RightWidth` |

`ScatterWidth` remains active and is not deprecated.

Visibility conditions:

- `ScatterWidth` is always visible.
- `LeftWidth` is hidden only in Right Only mode.
- `RightWidth` is hidden only in Left Only mode.

Density is interpolated from center to edge:

```text
Lerp(CenterDensity, EdgeDensity, Abs(Offset) / MaxOffsetForSelectedMode)
```

---

## Distance LOD

**Display name:** PCG Pro: Distance LOD  
**Class:** `UPCGDistanceLODSettings`  
**Pins:** Points → Points  
**Threading:** Async-capable

Reduces point density based on distance from `FixedLocation`.

| Property | Type | Default | Range |
|---|---|---:|---:|
| `NearDistance` | float | 5000 cm | 100–1000000 |
| `FarDistance` | float | 50000 cm | 100–1000000 |
| `FarDensity` | float | 0.1 | 0–1 |
| `FixedLocation` | FVector | (0,0,0) | — |

Behavior:

- Before Near Distance: Density unchanged
- Near to Far: Linear interpolation toward Far Density
- Beyond Far Distance: Density equals Far Density

The node does not remove points. Even Density `0` remains in the dataset until a downstream filter or density-aware spawner culls it.

---

## Random Subset

**Display name:** PCG Pro: Random Subset  
**Class:** `UPCGRandomSubsetSettings`  
**Pins:** Points → Points  
**Threading:** Async-capable

Keeps a deterministic percentage of each input point dataset.

| Property | Type | Default | Range |
|---|---|---:|---:|
| `KeepPercentage` | float | 50 | 0–100 |
| `bScaleDensity` | bool | false | — |

There is no fixed-count mode.

When `bScaleDensity` is enabled, surviving point density is multiplied by:

```text
KeepPercentage / 100
```

Multiple input datasets are processed independently.

---

## Biome Mask

**Display name:** PCG Pro: Biome Mask  
**Class:** `UPCGBiomeMaskSettings`  
**Enum:** `EPCGDensityFalloffMode` (`Linear`, `SmoothStep`, `Inverse`)  
**Pins:** Points → Points  
**Threading:** Async-capable

Modifies point density based on distance from the PCG volume center. It supports three falloff modes and optional neighborhood-based boundary detection for transition zones.

The node has no actor, shape, or external biome-region input. It does not delete points.

| Property | Type | Default | Range |
|---|---|---:|---:|
| `FalloffMode` | enum | Linear | — |
| `FalloffRadius` | float | 10000 cm | 100–500000 |
| `bCenterRelativeToVolume` | bool | true | — |
| `BoundaryNeighborRadius` | float | 200 cm | 10–5000 |
| `BoundaryMinNeighborCount` | int32 | 3 | 1–16 |
| `TransitionLowerBound` | float | 0.3 | 0–1 |
| `TransitionUpperBound` | float | 1.0 | 0–1 |
| `bNormalizeDensity` | bool | false | — |

`Inverse` reverses falloff direction: center density trends toward 0 while the outer region trends toward 1.

Boundary detection classifies transition regions from local neighbor counts. Use downstream Density Filters or separate spawners to convert the resulting density bands into visible biome borders.

Compared with Density Falloff, Biome Mask combines radial shaping with transition-boundary classification.

---

# Existing nodes

The 17 v1.1.1 node classes retain their property names, types, defaults, clamps, and pins in v2.0.0.

## Blue Noise Scatter

**Class:** `UPCGBlueNoiseScatterSettings`  
**Pins:** Points → Points

Poisson-disk thinning for evenly spaced point distributions.

| Property | Type | Default | Description |
|---|---|---:|---|
| `MinDistance` | float | 100 cm | Minimum distance between kept points |
| `MaxAttempts` | int32 | 30 | Rejection attempts |
| `MaxPoints` | int32 | 100000 | Hard output cap |

---

## Boundary Detect

**Class:** `UPCGBoundaryDetectSettings`  
**Pins:** Points → Points

Writes a bool boundary attribute based on local neighbor count.

| Property | Type | Default |
|---|---|---:|
| `NeighborRadius` | float | 200 cm |
| `MinNeighborCount` | int32 | 3 |
| `OutputAttributeName` | FName | `bIsBoundary` |

---

## Clump Scatter

**Class:** `UPCGClumpScatterSettings`  
**Pins:** Points → Points

Expands each input point into a deterministic local cluster.

| Property | Type | Default |
|---|---|---:|
| `ClumpSize` | int32 | 6 |
| `ClumpRadius` | float | 200 cm |
| `bScaleFalloff` | bool | true |
| `MinEdgeScale` | float | 0.4 |
| `bKeepSourcePoint` | bool | false |

---

## Density Falloff

**Class:** `UPCGDensityFalloffSettings`  
**Pins:** Points → Points

Multiplies density by a radial falloff.

| Property | Type | Default |
|---|---|---:|
| `bCenterRelativeToVolume` | bool | true |
| `Center` | FVector | (0,0,0) |
| `Radius` | float | 1000 cm |
| `FalloffMode` | enum | Linear |
| `FalloffCurve` | FRuntimeFloatCurve | — |

---

## Distance To Nearest Tag

**Class:** `UPCGDistanceToNearestTagSettings`  
**Pins:** Points + Target → Points

Writes nearest target-point distance to a float attribute.

| Property | Type | Default |
|---|---|---:|
| `SearchTarget` | enum | AllTagged |
| `TargetTag` | FName | None |
| `OutputAttributeName` | FName | `NearestTagDistance` |
| `MaxSearchDistance` | float | 0 |

---

## Grid Snap

**Class:** `UPCGGridSnapSettings`  
**Pins:** Points → Points

Snaps position to a world grid and can snap yaw to 90-degree increments.

| Property | Type | Default |
|---|---|---:|
| `GridSize` | float | 100 cm |
| `GridOrigin` | FVector | (0,0,0) |
| `bSnapRotationToGrid` | bool | false |

---

## Height Filter

**Class:** `UPCGHeightFilterSettings`  
**Pins:** Points → Points

Filters by world Z or height relative to a Landscape.

| Property | Type | Default |
|---|---|---:|
| `MinZ` | float | -1000000 cm |
| `MaxZ` | float | 1000000 cm |
| `bUseLandscapeReference` | bool | false |
| `LandscapeRef` | `TSoftObjectPtr<ALandscapeProxy>` | None |

Assign `LandscapeRef` explicitly in cooked builds when landscape-relative mode is used.

---

## Landscape Layer Sampler

**Class:** `UPCGLandscapeLayerSamplerSettings`  
**Pins:** Points → Points  
**Threading:** Main thread  
**Runtime:** Editor-only processing

Filters and modulates density from a painted Landscape layer.

| Property | Type | Default |
|---|---|---:|
| `LayerName` | FName | None |
| `MinWeight` | float | 0.1 |
| `bModulateDensity` | bool | true |
| `bInvertFilter` | bool | false |
| `LandscapeRef` | `TSoftObjectPtr<ALandscapeProxy>` | None |

In non-editor builds, the node logs a warning and passes input data through unchanged.

World Partition processing requires the relevant Landscape cells/proxies to be loaded.

---

## Noise Mask Filter

**Class:** `UPCGNoiseMaskFilterSettings`  
**Pins:** Points → Points

Uses 2D noise to remove or attenuate points.

| Property | Type | Default |
|---|---|---:|
| `NoiseScale` | float | 0.003 |
| `Threshold` | float | 0.5 |
| `FalloffWidth` | float | 0.1 |
| `NoiseOffset` | FVector2D | (0,0) |
| `bInvertMask` | bool | false |

---

## Print Stats

**Class:** `UPCGPrintStatsSettings`  
**Pins:** Points → Points

Passthrough debug node for point count, bounds, height, and density logging.

| Property | Type | Default |
|---|---|---:|
| `bPrintEnabled` | bool | true |
| `Label` | FString | `PCG Stats` |
| `bLogPointCount` | bool | true |
| `bLogBounds` | bool | true |
| `bLogZRange` | bool | true |
| `bLogDensityRange` | bool | true |

---

## Project To Landscape

**Class:** `UPCGProjectToLandscapeSettings`  
**Pins:** Points → Points

Projects points vertically to a Landscape.

| Property | Type | Default |
|---|---|---:|
| `LandscapeRef` | `TSoftObjectPtr<ALandscapeProxy>` | None |
| `ZOffset` | float | 0 cm |
| `bProjectRotation` | bool | false |

Assign `LandscapeRef` explicitly in cooked builds.

---

## Relax Points

**Class:** `UPCGRelaxPointsSettings`  
**Pins:** Points → Points

Iteratively moves points toward local-neighbor centroids.

| Property | Type | Default |
|---|---|---:|
| `Iterations` | int32 | 2 |
| `SearchRadius` | float | 300 cm |
| `RelaxStrength` | float | 0.5 |

---

## Curvature Filter

**Display name:** PCG Pro: Curvature Filter  
**Class:** `UPCGCurvatureFilterSettings`  
**Pins:** Points → Points

Filters by the angle between point surface normal and world up.

| Property | Type | Default |
|---|---|---:|
| `MinAngle` | float | 0° |
| `MaxAngle` | float | 30° |
| `FalloffAngle` | float | 0° |
| `bInvertFilter` | bool | false |

The internal class, template, and public v2 node title use **Curvature Filter** naming. The older **Slope Filter** title is documented only in the historical changelog.

---

## Spline Avoidance

**Class:** `UPCGSplineAvoidanceSettings`  
**Pins:** Points → Points  
**Threading:** Main thread

Removes or attenuates points near splines.

| Property | Type | Default |
|---|---|---:|
| `SplineActorTag` | FName | None |
| `SplineActors` | array of soft actor references | Empty |
| `AvoidanceRadius` | float | 500 cm |
| `FalloffRadius` | float | 0 cm |
| `bInvertSelection` | bool | false |

For cooked builds, prefer `SplineActorTag`. Direct references require the actor to be packaged and loaded.

---

## Align To Nearest Spline

**Class:** `UPCGAlignToNearestSplineSettings`  
**Pins:** Points → Points  
**Threading:** Main thread

Rotates each point to the tangent of the nearest configured spline.

| Property | Type | Default |
|---|---|---:|
| `SplineActorTag` | FName | None |
| `SplineActors` | array of soft actor references | Empty |

---

## Water Body Avoidance

**Class:** `UPCGWaterBodyAvoidanceSettings`  
**Pins:** Points → Points  
**Threading:** Main thread

Removes or attenuates points around UE Water Body splines.

| Property | Type | Default |
|---|---|---:|
| `WaterBodyActors` | array of soft actor references | Empty |
| `AvoidanceRadius` | float | 500 cm |
| `FalloffRadius` | float | 0 cm |
| `bInvertSelection` | bool | false |

The plugin has no hard Water dependency. The node walks the class hierarchy and matches the base class name `WaterBody`, covering `AWaterBody` subclasses. Without the Water plugin, the node cannot find Water Body actors and passes points through.

---

## Weighted Selection By Tag

**Class:** `UPCGWeightedSelectionByTagSettings`  
**Pins:** Points → Points

Selects points from tagged datasets with relative weights.

| Property | Type | Default |
|---|---|---:|
| `TagWeights` | `TMap<FName, float>` | Empty |
| `SelectionCount` | int32 | 100 |

Example:

```text
Oak = 3.0
Pine = 1.0
```

produces an approximate 75/25 relative selection.

---

Next: [05 — Presets](05_Presets.md)
