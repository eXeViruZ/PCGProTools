# 08 — Troubleshooting

---

## 8.1 — Toolbar buttons are missing

**Symptom:** The Debug Overlay / Template Library / Graph Inspector buttons do not appear in the Level Editor toolbar.

**Fix:**
1. Confirm the plugin is enabled: **Edit → Plugins →** search **PCG Pro Tools** → must be checked.
2. Confirm the **Procedural Content Generation Framework** (PCG) plugin is also enabled.
3. Restart the editor after enabling both.
4. If buttons still don't appear, check the Output Log for `LogPCGProKit` errors at startup.

---

## 8.2 — Nodes don't appear in the PCG Graph palette

**Symptom:** Right-clicking in a PCG Graph and searching "PCG Pro" finds nothing.

**Fix:**
1. The plugin DLLs may be stale after a code change. Rebuild: close the editor, rebuild from Visual Studio / Rider, reopen.
2. Verify the `PCGProKit` Runtime module is listed in the `.uplugin` and has a `LoadingPhase` of `Default`.
3. If you installed manually, confirm the folder is at `<YourProject>/Plugins/PCGProKit/` (not nested an extra level deep).

---

## 8.3 — "PCG Pro: Slope Filter" node is missing / broken graph from v1.0

**Symptom:** A graph saved in v1.0 shows a broken/unknown node where the slope filter was.

**Cause:** The node was renamed from `SurfaceSlopeFilter` to **PCG Pro: Slope Filter** (`UPCGCurvatureFilterSettings`) in v1.1.

**Fix:** Delete the broken node, add a new **PCG Pro: Slope Filter** node, and re-enter the `MinAngle` / `MaxAngle` / `FalloffAngle` values.

---

## 8.4 — Spline nodes produce no output in cooked/async builds

**Symptom:** Spline Avoidance or Align To Nearest Spline outputs 0 points (or all points) in a packaged game or async execution mode.

**Cause:** No spline actors were found, or their references did not resolve.

> As of v1.1 these nodes run on the game thread, so in-editor auto-discovery and tag lookup work reliably. If you still get no output, the spline references are simply not resolving (unloaded actors, wrong tag, or cooked build).

**Fix:** Always do one of the following:
- Set `SplineActorTag` to a tag name **and** add that same tag to your spline actor (Details → Actor → Tags). This is the recommended approach.
- Or populate the `SplineActors` array directly with explicit actor references.
- For cooked/runtime builds, always assign references explicitly — a blind world scan is non-deterministic and the target actors must be loaded.

---

## 8.5 — Water Body Avoidance does nothing

**Symptom:** All points pass through unchanged even though water bodies exist in the level.

**Causes and fixes:**
1. **Water plugin not enabled.** Enable it in **Edit → Plugins → Water**. If it is not enabled, the node passes through all points by design.
2. **Async execution.** Same as 8.4 — assign `WaterBodyActors` explicitly.
3. **Wrong actor type.** The node discovers actors via reflection by class name `AWaterBody*`. If you are using a custom water actor that does not inherit from `AWaterBody`, it will not be found automatically. Assign it explicitly via `WaterBodyActors`.

---

## 8.6 — Landscape Layer Sampler removes all points

**Symptom:** Zero points survive the Landscape Layer Sampler node.

**Causes and fixes:**
1. **`LayerName` mismatch.** The name must match exactly the `Layer Name` field on the `ULandscapeLayerInfoObject` asset (case-sensitive). Open the `LayerInfoObject` and confirm.
2. **Layer has no paint data.** The layer exists as an asset but has not been painted anywhere on the landscape. Paint some weight first.
3. **`MinWeight` too high.** Default is 0.1. If the painted layer has only light weight, reduce `MinWeight`.
4. **Multiple landscapes in the level.** The node auto-discovers the first landscape it finds. If you have more than one, set `LandscapeRef` explicitly to the one you want to sample.
5. **Non-editor build.** The node is editor-only. In packaged builds all points pass through unchanged — this is expected.

> **World Partition:** as of v1.1 the node samples across all landscape streaming proxies, so partitioned landscapes work correctly. If you still see points dropped, set `LandscapeRef` explicitly.

---

## 8.7 — Height Filter removes all or no points

**Symptom:** The Height Filter node removes everything, or nothing.

**Check:**
- If `bUseLandscapeReference` is true, `LandscapeRef` must be assigned. If it is null, the node falls back to world-space Z which may not match your intended range.
- Confirm `MinZ` < `MaxZ`. Swapped values will produce an empty range.
- Z values are in centimetres. 1 m = 100 cm. Double-check your scale.

---

## 8.8 — Boundary Detect marks all or no points as boundary

**Symptom:** Every point gets `bIsBoundary = true`, or none do.

**Fix:**
- If all points are boundary: `NeighborRadius` is too small relative to your point spacing. Increase it to ~2× the average distance between points.
- If no points are boundary: `NeighborRadius` is very large (every point has many neighbors) or `MinNeighborCount` is too low. Reduce `NeighborRadius` or increase `MinNeighborCount`.

---

## 8.9 — Blue Noise Scatter outputs fewer points than expected

**Symptom:** Fewer output points than the input had, even with a large area.

**Cause:** `MinDistance` may be too large for the input density. Blue Noise Scatter thins the input — it does not generate new points.

**Fix:** Reduce `MinDistance`, or increase your input point density (larger PCGVolume / denser Surface Sampler grid).

---

## 8.10 — Density Falloff has no visible effect

**Symptom:** Point density looks the same before and after the node.

**Check:**
- `bCenterRelativeToVolume = true` (default). This means `Center = (0,0,0)` is the PCGVolume's origin, not world (0,0,0). If your volume is centred, this is correct. If you want world-space, disable the flag.
- Check that `Radius` is large enough relative to the PCGVolume size.
- Density Falloff multiplies the `Density` value on each point. If the downstream node does not filter on density, there will be no visible change. Pair it with a Density Filter or ensure your spawner uses density.

---

## 8.11 — Graph Inspector shows no parameters

**Symptom:** The Graph Inspector is open, a PCG actor is selected, but the node parameter list is empty.

**Cause:** The node parameter panel shows `PCG_Overridable` properties. If no parameters have been exposed, the panel is intentionally empty.

**Fix:** Open the PCG Graph, right-click a property on any node, and choose **Expose to Graph Instance**. It will then appear in the editable top section of the Inspector.

---

## 8.12 — Preset Apply has no effect

**Symptom:** Clicking Apply Preset in the Graph Inspector does not change node values.

**Check:**
1. The `NodeClass` in each `FPCGProKitPresetEntry` must match the actual settings class in the graph (subclasses also match). Confirm the class names.
2. The `PropertyName` must be the **exact C++ UPROPERTY name** (e.g. `MinDistance`, not `Min Distance`).
3. The `Type` field must match the property type. A `Float` entry on an `int32` property will silently fail.

---

## 8.13 — Performance warning notification appears

**Symptom:** An editor notification fires saying a node output too many points.

**This is expected behaviour.** The `DensityCapWarningThreshold` in Project Settings is a non-blocking warning, not an error. No data is lost.

**Options:**
- Reduce the output point count by tightening filter parameters.
- Raise or disable the threshold (**Edit → Project Settings → Plugins → PCG Pro Tools → Performance Guards → `DensityCapWarningThreshold`**, set to 0 to disable).

---

## 8.14 — Debug Overlay drops FPS

**Symptom:** Enabling the Debug Overlay significantly reduces editor frame rate.

**Fix:**
- Change **Overlay Scope** to `SelectedOnly` (default) in **Project Settings → Plugins → PCG Pro Tools → Debug Overlay**.
- Reduce `MaxPointsPerComponent`.
- Do not use `AllInLevel` scope on large levels with many PCG actors.

---

## 8.15 — Reporting a bug

When opening an issue, include:

1. Engine version (must be 5.7.x)
2. PCG Pro Tools version (check the `.uplugin` `VersionName` field)
3. Which demo map or template reproduces the issue
4. PCGVolume config (template used, preset applied if any)
5. Relevant Output Log lines (filter by `LogPCGProKit`)
6. Minimal repro map if possible

Open issues on the GitHub repository or post in the Discord: [discord.gg/vgpmnN6nCR](https://discord.gg/vgpmnN6nCR)
