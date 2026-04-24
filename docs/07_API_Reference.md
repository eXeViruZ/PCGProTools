<!-- Copyright (c) 2026 Tom Leon Vincent Hanke -->

# 07 — API Reference

Public API surface of PCGProKit for C++, Blueprint, and Python users. Targets **UE 5.7.4**.

> Internal implementation details (private helpers, editor-only tooling) are intentionally not documented here and may change without notice.

---

## 7.1 Module Surface

| Module | Type | Purpose | Exported to |
|---|---|---|---|
| `PCGProKit` | Runtime | Custom PCG node settings, data types, runtime-safe helpers | C++, Blueprint, Python |
| `PCGProKitEditor` | Editor | Menus, asset actions, tooling | Editor-only C++ |

Runtime code **must not** depend on `PCGProKitEditor`. Editor code may depend on `PCGProKit`.

---

## 7.2 Public Node Settings Classes (Runtime)

All custom nodes are implemented as `UPCGSettings` subclasses in the `PCGProKit` module. They are the BP-/Python-visible handles for configuring nodes programmatically.

| Class | Node |
|---|---|
| `UPCGProjectToLandscapeSettings` | ProjectToLandscape |
| `UPCGAlignToNearestSplineSettings` | AlignToNearestSpline |
| `UPCGBoundaryDetectSettings` | BoundaryDetect |
| `UPCGDistanceTagSettings` | DistanceTag |
| `UPCGGridSnapSettings` | GridSnap |

Key `UPROPERTY` members per class are listed in [04_Nodes](04_Nodes.md). All settings properties are `EditAnywhere, BlueprintReadWrite` (verify in the class header before scripting overrides).

---

## 7.3 C++ Usage Pattern

### Including

```cpp
#include "PCG/Settings/PCGProjectToLandscapeSettings.h"
#include "PCG/Settings/PCGAlignToNearestSplineSettings.h"
#include "PCG/Settings/PCGBoundaryDetectSettings.h"
#include "PCG/Settings/PCGDistanceTagSettings.h"
#include "PCG/Settings/PCGGridSnapSettings.h"
```

> Exact header paths follow the `PCGProKit` module's `Source/PCGProKit/Public/...` layout. Adjust to match the shipped source tree.

### Build.cs

Add `PCGProKit` to `PublicDependencyModuleNames` (or `PrivateDependencyModuleNames`) in your module's `Build.cs`:

```csharp
PublicDependencyModuleNames.AddRange(new[]
{
    "Core", "CoreUObject", "Engine",
    "PCG",
    "PCGProKit",
});
```

Editor-only code additionally needs `UnrealEd` and the editor module:

```csharp
if (Target.bBuildEditor)
{
    PrivateDependencyModuleNames.AddRange(new[] { "UnrealEd", "PCGProKitEditor" });
}
```

---

## 7.4 Triggering a Generation Programmatically

```cpp
// Copyright (c) 2026 Tom Leon Vincent Hanke

#include "PCGComponent.h"
#include "PCGVolume.h"

void GenerateOnVolume(APCGVolume* Volume, int32 Seed)
{
    if (!Volume) return;
    if (UPCGComponent* PCG = Volume->FindComponentByClass<UPCGComponent>())
    {
        PCG->Seed = Seed;
        PCG->Generate(/*bForce=*/true);
    }
}
```

See [06_Runtime_Usage](06_Runtime_Usage.md) § 6.2 for the full runtime spawn + generate flow.

---

## 7.5 Blueprint Surface

All `UPCGProKit*Settings` properties exposed as `BlueprintReadWrite` can be edited on a Graph Instance's parameter overrides in Blueprint. Typical BP flow:

1. Get the `PCGComponent` on your actor.
2. Access the graph instance.
3. Set parameter overrides by name (matches the exposed parameter name on the `PCGT_*` template).
4. Call `Generate` (exposed on `UPCGComponent`).

For project-specific convenience, wrap these calls in a small `UBlueprintFunctionLibrary` in your project (not in the plugin).

---

## 7.6 Python Scripting

Python support in UE 5.7 covers common asset/editor operations. Two project-relevant workarounds:

### Creating a spline actor (no `unreal.SplineActor` binding in 5.7)

```python
import unreal

world = unreal.EditorLevelLibrary.get_editor_world()
actor = unreal.EditorLevelLibrary.spawn_actor_from_class(
    unreal.Actor, unreal.Vector(0, 0, 0))

subsystem = unreal.get_engine_subsystem(unreal.SubobjectDataSubsystem)
root_handle = subsystem.k2_gather_subobject_data_for_instance(actor)[0]
params = unreal.AddNewSubobjectParams()
params.parent_handle = root_handle
params.new_class = unreal.SplineComponent
params.blueprint_context = None
subsystem.add_new_subobject(params)

# Required for PCGT_SplineRoad:
actor.tags = [unreal.Name('Road')]
```

### Map duplication + level load (avoid world-memory-leak crash)

A known interaction in 5.7: calling `duplicate_asset` on a map and immediately `load_level` on another map can trip a "World Memory Leaks" assert. Workaround:

1. Load a neutral intermediate level (e.g., an empty map).
2. Then perform the duplicate.
3. Then load the target map.

---

## 7.7 Extending PCGProKit (Custom Nodes)

If you want to author additional PCG nodes alongside PCGProKit in your project:

- Derive from `UPCGSettings` for the settings object.
- Derive from `FPCGElement` for the element logic.
- When writing metadata on newly created entries, **always** call `Metadata->InitializeOnSet(EntryKey)` before `Attr->SetValue(EntryKey, Value)` — required in PCG 5.7 to avoid the assert in `PCGMetadataAttributeTpl.h:587`.
- Prefer `UPCGPointArrayData` over legacy point data types for new code.
- Guard actor-reference fallbacks with `WITH_EDITOR` and bound world iteration on the **game thread**.

---

## 7.8 Versioning

- PCGProKit 1.0.0 targets PCG 5.7 API surface. No stable-API guarantee across engine major/minor versions.
- Breaking changes in PCGProKit will be documented in [09_Changelog](09_Changelog.md).

---

**Next:** [08_Troubleshooting](08_Troubleshooting.md)
