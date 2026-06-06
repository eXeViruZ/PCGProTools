# 07 — API Reference

This page covers the public C++ API of PCG Pro Tools v1.1 for teams integrating with the plugin from code.

---

## Module structure

| Module | Type | Description |
|---|---|---|
| `PCGProKit` | Runtime | Custom node settings classes, preset system, project settings |
| `PCGProKitEditor` | Editor | Toolbar, debug overlay, template library, graph inspector widgets |

Add `PCGProKit` to your `Build.cs` `PublicDependencyModuleNames` if you need to reference node settings classes at runtime. Never take a dependency on `PCGProKitEditor` from a runtime module.

---

## Runtime module — PCGProKit

### Node settings classes

All node settings classes are in `Source/PCGProKit/Public/Nodes/`. Each inherits from `UPCGSettings`.

| Class | Header |
|---|---|
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

### Enums

#### `EPCGFalloffMode` (`PCGDensityFalloffSettings.h`)

```cpp
UENUM(BlueprintType)
enum class EPCGFalloffMode : uint8
{
    Linear,       // density *= 1.0 - (distance / Radius)
    Exponential,  // density *= exp(-3.0 * distance / Radius)
    Curve,        // density *= FalloffCurve.Eval(distance / Radius)
};
```

#### `EPCGSearchTarget` (`PCGDistanceToNearestTagSettings.h`)

```cpp
UENUM(BlueprintType)
enum class EPCGSearchTarget : uint8
{
    AllTagged,    // Search all tagged input datasets
    SpecificTag,  // Search only the dataset whose tag matches TargetTag
};
```

#### `EPCGDebugOverlayScope` (`PCGProKitSettings.h`)

```cpp
UENUM()
enum class EPCGDebugOverlayScope : uint8
{
    SelectedOnly,
    AllVisibleInViewport,
    AllInLevel,
};
```

---

### `UPCGProKitSettings` (`PCGProKitSettings.h`)

Project-level settings. Accessible via `UPCGProKitSettings::Get()` or `GetDefault<UPCGProKitSettings>()`.

```cpp
// Debug Overlay
EPCGDebugOverlayScope OverlayScope;          // default: SelectedOnly
float  OverlayViewportMaxDistance;           // default: 100000 cm
int32  MaxPointsPerComponent;                // default: 5000
bool   bShowLabelsOnlyForSelected;           // default: true
float  AttributeLabelDrawDistance;           // default: 5000 cm

// Performance Guards
int32  DensityCapWarningThreshold;           // default: 1000000 (0 = off)

// Determinism
bool   bDeterministicMode;                   // default: false
int32  DefaultRandomSeed;                    // default: 42
```

**Static helpers:**

```cpp
// Logs a warning if OutputPointCount > DensityCapWarningThreshold.
// Returns true if the warning fired.
static bool CheckDensityCap(int32 OutputPointCount, const FString& NodeDisplayName);

// Sums all output point data in Context->OutputData and checks against threshold.
static bool CheckDensityCapFromContext(FPCGContext* Context, const FString& NodeDisplayName);
```

---

### `UPCGProKitPresetAsset` (`PCGProKitPresetAsset.h`)

Data asset storing a set of named property overrides.

```cpp
FName   PresetName;                          // Display name in the Graph Inspector dropdown
FText   Description;                         // Tooltip in Graph Inspector
TSoftObjectPtr<UPCGGraph> TargetGraph;       // Informational only; not enforced
TArray<FPCGProKitPresetEntry> Overrides;     // Override list

// Applies all overrides to the matching nodes on Component's graph.
// Iterates Overrides[] and writes each entry via FProperty reflection.
// Returns true on success.
bool ApplyToComponent(UPCGComponent* Component) const;

// Captures all PCG_Overridable properties from every node in Component's graph
// into this asset's Overrides array.
// Returns the number of overrides captured, or 0 on failure.
int32 CaptureFromComponent(UPCGComponent* Component);
```

---

### `FPCGProKitPresetEntry` (`PCGProKitPresetEntry.h`)

One override entry in a `UPCGProKitPresetAsset`.

```cpp
USTRUCT(BlueprintType)
struct FPCGProKitPresetEntry
{
    TSoftClassPtr<UPCGSettings> NodeClass;   // Settings class to target
    FName PropertyName;                      // Exact UPROPERTY name
    EPCGProKitPresetParamType Type;          // Float / Int32 / Bool
    float FloatValue;
    int32 IntValue;
    bool  BoolValue;
};
```

#### `EPCGProKitPresetParamType`

```cpp
UENUM(BlueprintType)
enum class EPCGProKitPresetParamType : uint8
{
    Float,
    Int32,
    Bool,
};
```

---

## Editor module — PCGProKitEditor

The editor module is loaded only in editor builds. Do not reference it from runtime code.

### `FPCGProKitEditorModule`

Module entry point. Registers the toolbar, menus, debug overlay, and actor context menu extension.

### `FPCGProKitCommands`

Three registered commands:

| Command | Description |
|---|---|
| `ToggleDebugOverlay` | Enables/disables the viewport debug overlay |
| `OpenTemplateLibrary` | Opens the Template Library tab |
| `OpenQuickTune` | Opens the Graph Inspector tab |

### `FPCGProKitDebugOverlayTicker`

Tick-based overlay manager (~30 Hz). Maintains a cached set of live `UPCGComponent` weak pointers, updated via actor lifecycle delegates. Falls back to a full `TObjectIterator` rescan when the cache is empty.

```cpp
void Start();    // Start ticking, build cache, subscribe to actor delegates
void Stop();     // Stop ticking, unsubscribe, clear cache
bool IsRunning() const;
```

### Widgets

| Class | Tab name | Description |
|---|---|---|
| `SPCGTemplateLibraryWidget` | `PCGProKit.TemplateLibrary` | Template browser and spawner |
| `SPCGQuickTuneWidget` | `PCGProKit.QuickTune` | Graph Inspector (node param overview + preset controls) |

---

## Build.cs dependency

To reference `PCGProKit` runtime types from your own module:

```csharp
PublicDependencyModuleNames.AddRange(new string[]
{
    "PCG",
    "PCGProKit",
});
```

For editor-only tools that also need the editor module:

```csharp
if (Target.bBuildEditor)
{
    PrivateDependencyModuleNames.Add("PCGProKitEditor");
}
```

---

Next: [`08_Troubleshooting.md`](08_Troubleshooting.md)
