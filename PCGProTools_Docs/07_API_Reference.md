# 07 — API Reference

This page documents the public C++ integration surface of PCG Pro Tools v2.0.0 for Unreal Engine 5.8.

---

## Modules

| Module | Type | Purpose |
|---|---|---|
| `PCGProKit` | Runtime | Custom node settings, preset assets, project settings, runtime helpers |
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

All settings classes derive from `UPCGSettings`.

---

## v2 enums

### `EPCGSplineOffsetSideMode`

Declared in `PCGSplineOffsetSettings.h`.

```cpp
UENUM(BlueprintType)
enum class EPCGSplineOffsetSideMode : uint8
{
    Both,
    LeftOnly,
    RightOnly,
};
```

### `EPCGDensityFalloffMode`

Declared in `PCGBiomeMaskSettings.h`.

```cpp
UENUM(BlueprintType)
enum class EPCGDensityFalloffMode : uint8
{
    Linear,
    SmoothStep,
    Inverse,
};
```

---

## Existing public enums

### `EPCGFalloffMode`

Declared in `PCGDensityFalloffSettings.h`.

```cpp
UENUM(BlueprintType)
enum class EPCGFalloffMode : uint8
{
    Linear,
    Exponential,
    Curve,
};
```

### `EPCGSearchTarget`

Declared in `PCGDistanceToNearestTagSettings.h`.

```cpp
UENUM(BlueprintType)
enum class EPCGSearchTarget : uint8
{
    AllTagged,
    SpecificTag,
};
```

### `EPCGDebugOverlayScope`

Declared in `PCGProKitSettings.h`.

```cpp
UENUM()
enum class EPCGDebugOverlayScope : uint8
{
    SelectedOnly,
    AllInActiveViewport,
    AllInLevel,
};
```

### `EPCGProKitPresetParamType`

Declared in `PCGProKitPresetEntry.h`.

```cpp
UENUM(BlueprintType)
enum class EPCGProKitPresetParamType : uint8
{
    Float,
    Int32,
    Bool,
};
```

Enums used by presets are stored through Int32 entries.

---

## `UPCGProKitSettings`

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

### Public static helpers

```cpp
static bool CheckDensityCap(
    int32 OutputPointCount,
    const FString& NodeDisplayName);

static bool CheckDensityCapFromContext(
    FPCGContext* Context,
    const FString& NodeDisplayName);
```

`CheckDensityCap` reports when an output count exceeds `DensityCapWarningThreshold`. `CheckDensityCapFromContext` totals point outputs in the supplied PCG context before applying the same check.

---

## Preset API

### `UPCGProKitPresetAsset`

`UPCGProKitPresetAsset` derives from `UDataAsset`.

```cpp
FName PresetName;
FText Description;
TSoftObjectPtr<UPCGGraph> TargetGraph;
TArray<FPCGProKitPresetEntry> Overrides;

bool ApplyToComponent(UPCGComponent* Component) const;
int32 CaptureFromComponent(UPCGComponent* Component);
```

- `TargetGraph` is informational and is not enforced.
- `ApplyToComponent()` writes compatible overrides to matching settings classes.
- `CaptureFromComponent()` captures supported `PCG_Overridable` properties.

### `FPCGProKitPresetEntry`

```cpp
USTRUCT(BlueprintType)
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

---

## Editor module

### `FPCGProKitEditorModule`

Registers toolbar commands, menu entries, tabs, the Debug Overlay, and the PCG actor context-menu extension.

### `FPCGProKitCommands`

| Command | Purpose |
|---|---|
| `ToggleDebugOverlay` | Toggle viewport overlay |
| `OpenTemplateLibrary` | Open Template Library |
| `OpenQuickTune` | Open Graph Inspector |

### Editor widgets and tab IDs

| Widget | Tab ID | Purpose |
|---|---|---|
| `SPCGTemplateLibraryWidget` | `PCGProKitTemplateLibrary` | Template search, categories, Add to Level, editable copy |
| `SPCGQuickTuneWidget` | `PCGProKitQuickTune` | Graph Inspector, node parameters, presets, seeds, validation |

Internal helper types such as `FPCGNodeParamRow` and the overlay ticker are implementation details, not stable public integration APIs.

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

Landscape Layer Sampler performs its actual layer sampling only inside editor builds.

---

Next: [08 — Troubleshooting](08_Troubleshooting.md)
