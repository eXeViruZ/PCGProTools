# 07 — API Reference

This page summarizes the public C++ integration surface of PCG Pro Tools v2.0.0 for Unreal Engine 5.8.

---

## Modules

| Module | Type | Purpose |
|---|---|---|
| `PCGProKit` | Runtime | Custom node settings, preset assets, project settings |
| `PCGProKitEditor` | Editor | Toolbar, Graph Inspector, Template Library, Debug Overlay |

Never add a dependency on `PCGProKitEditor` from a runtime module.

---

## Runtime dependencies

`PCGProKit.Build.cs` uses:

```text
Core
CoreUObject
Engine
PCG
DeveloperSettings
Landscape
```

The Water plugin is not a hard dependency.

---

## Node settings classes

All settings headers are under:

```text
Source/PCGProKit/Public/Nodes/
```

| Settings class | Header |
|---|---|
| `UPCGInstanceVariationSettings` | `PCGInstanceVariationSettings.h` |
| `UPCGSplineOffsetSettings` | `PCGSplineOffsetSettings.h` |
| `UPCGDistanceLODSettings` | `PCGDistanceLODSettings.h` |
| `UPCGRandomSubsetSettings` | `PCGRandomSubsetSettings.h` |
| `UPCGBiomeMaskSettings` | `PCGBiomeMaskSettings.h` |
| `UPCGBlueNoiseScatterSettings` | `PCGBlueNoiseScatterSettings.h` |
| `UPCGBoundaryDetectSettings` | `PCGBoundaryDetectSettings.h` |
| `UPCGClumpScatterSettings` | `PCGClumpScatterSettings.h` |
| `UPCGCurvatureFilterSettings` | `PCGCurvatureFilterSettings.h` |
| `UPCGDensityFalloffSettings` | `PCGDensityFalloffSettings.h` |
| `UPCGDistanceToNearestTagSettings` | `PCGDistanceToNearestTagSettings.h` |
| `UPCGGridSnapSettings` | `PCGGridSnapSettings.h` |
| `UPCGHeightFilterSettings` | `PCGHeightFilterSettings.h` |
| `UPCGLandscapeLayerSamplerSettings` | `PCGLandscapeLayerSamplerSettings.h` |
| `UPCGNoiseMaskFilterSettings` | `PCGNoiseMaskFilterSettings.h` |
| `UPCGPrintStatsSettings` | `PCGPrintStatsSettings.h` |
| `UPCGProjectToLandscapeSettings` | `PCGProjectToLandscapeSettings.h` |
| `UPCGRelaxPointsSettings` | `PCGRelaxPointsSettings.h` |
| `UPCGSplineAvoidanceSettings` | `PCGSplineAvoidanceSettings.h` |
| `UPCGAlignToNearestSplineSettings` | `PCGAlignToNearestSplineSettings.h` |
| `UPCGWaterBodyAvoidanceSettings` | `PCGWaterBodyAvoidanceSettings.h` |
| `UPCGWeightedSelectionByTagSettings` | `PCGWeightedSelectionByTagSettings.h` |

---

## v2 enums

### EPCGSplineOffsetSideMode

Declared in `PCGSplineOffsetSettings.h`.

```cpp
enum class EPCGSplineOffsetSideMode : uint8
{
    Both,
    LeftOnly,
    RightOnly,
};
```

### EPCGDensityFalloffMode

Declared in `PCGBiomeMaskSettings.h`.

```cpp
enum class EPCGDensityFalloffMode : uint8
{
    Linear,
    SmoothStep,
    Inverse,
};
```

---

## Existing enums

### EPCGDebugOverlayScope

Declared in `PCGProKitSettings.h`.

```cpp
enum class EPCGDebugOverlayScope : uint8
{
    SelectedOnly,
    AllInActiveViewport,
    AllInLevel,
};
```

### EPCGProKitPresetParamType

Declared in `PCGProKitPresetEntry.h`.

```cpp
enum class EPCGProKitPresetParamType : uint8
{
    Float,
    Int32,
    Bool,
};
```

---

## UPCGProKitSettings

`UPCGProKitSettings` derives from `UDeveloperSettings` and uses Editor config.

Location:

```text
Project Settings → Plugins → PCG Pro Tools
```

Important properties:

```cpp
// Debug Overlay
EPCGDebugOverlayScope OverlayScope;
float OverlayViewportMaxDistance;
int32 MaxPointsPerComponent;
bool bShowLabelsOnlyForSelected;
float AttributeLabelDrawDistance;

// Node Type Filter
bool bEnableNodeTypeFilter;
TSet<FString> DebugNodeTypeFilter;

// Performance
int32 DensityCapWarningThreshold;

// Determinism
bool bDeterministicMode;
int32 DefaultRandomSeed;
```

Defaults:

| Property | Default |
|---|---:|
| `OverlayScope` | SelectedOnly |
| `OverlayViewportMaxDistance` | 100000 cm |
| `MaxPointsPerComponent` | 5000 |
| `bShowLabelsOnlyForSelected` | true |
| `AttributeLabelDrawDistance` | 5000 cm |
| `bEnableNodeTypeFilter` | false |
| `DensityCapWarningThreshold` | 1000000 |
| `bDeterministicMode` | false |
| `DefaultRandomSeed` | 42 |

---

## Preset API

### UPCGProKitPresetAsset

`UPCGProKitPresetAsset` derives from `UDataAsset`.

Important members:

```cpp
FName PresetName;
FText Description;
TSoftObjectPtr<UPCGGraph> TargetGraph;
TArray<FPCGProKitPresetEntry> Overrides;

bool ApplyToComponent(UPCGComponent* Component) const;
int32 CaptureFromComponent(UPCGComponent* Component);
```

`TargetGraph` is informational.

### FPCGProKitPresetEntry

```cpp
struct FPCGProKitPresetEntry
{
    TSoftClassPtr<UPCGSettings> NodeClass;
    FName PropertyName;
    EPCGProKitPresetParamType Type;
    float FloatValue;
    int32 IntValue;
    bool BoolValue;
};
```

Enums are stored through Int32 entries.

---

## Editor commands

`FPCGProKitCommands` registers:

| Command | Purpose |
|---|---|
| `ToggleDebugOverlay` | Toggle viewport overlay |
| `OpenTemplateLibrary` | Open Template Library |
| `OpenQuickTune` | Open Graph Inspector |

---

## Editor widgets and tab IDs

| Widget | Tab ID | Purpose |
|---|---|---|
| `SPCGTemplateLibraryWidget` | `PCGProKitTemplateLibrary` | Template search, categories, add, copy |
| `SPCGQuickTuneWidget` | `PCGProKitQuickTune` | Graph Inspector, presets, seeds, validation |

Internal helper types such as `FPCGNodeParamRow` and overlay ticker implementation details are not intended as stable public integration APIs.

---

## Build.cs usage

To reference runtime types:

```csharp
PublicDependencyModuleNames.AddRange(new string[]
{
    "PCG",
    "PCGProKit",
});
```

For an editor-only module that intentionally uses PCG Pro Tools editor APIs:

```csharp
if (Target.bBuildEditor)
{
    PrivateDependencyModuleNames.Add("PCGProKitEditor");
}
```

Do not place `PCGProKitEditor` in runtime module dependencies.

---

## Threading notes

Main-thread nodes:

- `UPCGSplineAvoidanceSettings`
- `UPCGAlignToNearestSplineSettings`
- `UPCGWaterBodyAvoidanceSettings`
- `UPCGLandscapeLayerSamplerSettings`

All other v2 settings classes are async-capable.

Landscape Layer Sampler performs its actual sampling only inside editor builds.

---

Next: [08 — Troubleshooting](08_Troubleshooting.md)
