<!-- Copyright (c) 2026 Tom Leon Vincent Hanke -->

# 08 — Troubleshooting

Known issues, fixes, and workarounds. Check the matching section before filing a support request.

---

## 8.1 Installation

### Plugin does not appear in the Plugins window
- Project plugin: folder must be `<YourProject>/Plugins/PCGProKit/` containing `PCGProKit.uplugin`.
- Engine plugin (Fab): installed to the correct engine version (UE 5.7).
- Regenerate project files and rebuild Development Editor.
- PCGProKit requires **UE 5.7.4**. Other 5.x versions are not supported in 1.0.

### Editor crashes on load after installing
- Ensure the built-in **Procedural Content Generation Framework** (`PCG`) plugin is enabled. PCGProKit depends on it.
- Delete `<YourProject>/Intermediate/` and `<YourProject>/Binaries/`, then rebuild.

### `BuildPlugin -configuration=Shipping` uses stale DLL
- **Cause**: Live Coding patches only live in memory. They are lost on editor restart.
- **Fix**: close the editor, do a full IDE rebuild of `PCGProKitPEditor` (Win64, Development Editor), then reopen the editor. Run `BuildPlugin` only after a clean rebuild.
- **Rule**: always finish a session with a full IDE rebuild if Live Coding was used.

---

## 8.2 Generation Produces Nothing

### Checklist (in order)
1. The `PCGVolume` has non-zero extent (see 8.3 below if it does not).
2. The volume bounds contain the target area (and the landscape, if required).
3. The template's required inputs are present (see [03_Templates](03_Templates.md)).
4. `PCGComponent → Generate` was pressed (or *Regenerate In Editor* is enabled).
5. No PCG warnings in the Output Log.

### Template-specific
- `PCGT_SplineRoad`: the spline actor **must have the Actor Tag `Road`** and contain a `USplineComponent` with ≥ 2 points.
- Landscape-projection templates: a `Landscape` must be present inside the volume.

---

## 8.3 `PCGVolume` Has Zero Extent / No Brush

- **Cause**: brush not built. Known transient state in UE 5.7 when volumes are created via scripting or duplication.
- **Fix**: **respawn the volume** — delete it and drag a new `PCGVolume` from *Place Actors → Volumes*. Do not try to repair the broken volume in place.

---

## 8.4 Points Are Misplaced in Z / Float Above Ground

- Template uses `ProjectToLandscape` but `LandscapeRef` is empty and no fallback landscape is found.
- **Editor fix**: assign `LandscapeRef` explicitly on the node, or ensure a `UPCGLandscapeData` is present in the input.
- **Runtime fix**: always set `LandscapeRef` explicitly. Editor fallback does not apply in cooked builds.

---

## 8.5 `PCGT_SplineRoad` Generates Nothing

Ordered causes:
1. Spline actor missing the Actor Tag `Road`. Most common cause.
2. Spline has < 2 points.
3. Volume does not overlap the spline.
4. Attempted direct `Input → SplineSampler.Spline` wiring — PCG 5.7 auto-discovery is unreliable for this path; the template uses a `Get Spline Data` node with a tag filter. Do not modify this.

---

## 8.6 Custom Node Crashes With Metadata Assert

- **Assert**: `PCGMetadataAttributeTpl.h:587`.
- **Cause**: `SetValue` called on a metadata entry that was not initialized.
- **Fix** (when extending PCGProKit or authoring your own nodes):

```cpp
Metadata->InitializeOnSet(WriteRanges.MetadataEntryRange[i]);
Attr->SetValue(WriteRanges.MetadataEntryRange[i], Value);
```

- PCGProKit's built-in nodes already follow this pattern.

---

## 8.7 Editor Crash: "World Memory Leaks" after duplicate + load

- **Trigger**: calling `duplicate_asset` on a map and immediately `load_level` on another map.
- **Workaround**: load a **neutral intermediate level** (e.g., an empty map) between the duplicate and the next load.

---

## 8.8 Live Coding Fix Disappeared After Editor Restart

- **Cause**: Live Coding patches are in-memory only. Restart discards them.
- **Fix**: finish every session with a full IDE rebuild. See 8.1 "BuildPlugin uses stale DLL".

---

## 8.9 Python: `unreal.SplineActor` Not Found

- **Cause**: `SplineActor` is not exposed to Python in UE 5.7.
- **Workaround**: spawn a generic `unreal.Actor` and add a `SplineComponent` via `SubobjectDataSubsystem.add_new_subobject`. Full snippet in [07_API_Reference](07_API_Reference.md) § 7.6.

---

## 8.10 Custom Node With Empty Actor Soft-Ref Crashes at Runtime

- **Cause**: a custom node dereferences an unset `TSoftObjectPtr<AActor>` with no fallback path.
- **PCGProKit's shipped nodes** include editor fallbacks for `LandscapeRef` and `SplineActors`. For runtime, **set them explicitly**.
- **When authoring your own**: guard fallbacks behind `WITH_EDITOR` and require explicit assignment at runtime.

---

## 8.11 Output Is Not Deterministic

- Different seed, different inputs, or a fallback world-scan is returning different actors across sessions (fallback order is not guaranteed across runs).
- **Fix**: set all actor references explicitly. Do not rely on fallbacks when determinism matters.

---

## 8.12 Regression Testing

An automation test for `BoundaryDetect` using `FTestData` + `UPCGPointArrayData` context setup crashed the editor in UE 5.7 and is **not shipped** in 1.0. Regression coverage in 1.0 is via the demo maps:

1. Open each of the 5 demo maps under `Plugins/PCGProKit Content/Maps/`.
2. Select the existing `PCGVolume` (standard config: Loc `(-12600, -12600, 130)`, Scale `(50, 50, 5)`, Seed `42`).
3. Press **Generate**.
4. Verify approximate instance counts per [03_Templates](03_Templates.md) § 3.1.

---

## 8.13 Reporting an Issue

Include in your report:
- Engine version (must be 5.7.4 for support in 1.0).
- PCGProKit version.
- Minimal repro map (or which demo map reproduces).
- PCGVolume config (Location, Scale, Seed).
- Template and preset used.
- Full editor `.log` from the affected session.
- Whether you ran Live Coding during the session (if yes, re-test after a clean IDE rebuild before reporting).

---

**Next:** [09_Changelog](09_Changelog.md)
