<!-- Copyright (c) 2026 Tom Leon Vincent Hanke -->

# 06 — Runtime Usage

Rules for using PCGProKit outside the editor: in PIE, cooked builds, dedicated servers, and runtime spawning. The central rule:

> **Editor fallbacks are editor-only. For runtime, set all actor references explicitly.**

---

## 6.1 Editor vs Runtime Behavior Matrix

| Node | Editor fallback | Runtime requirement |
|---|---|---|
| `ProjectToLandscape` | Searches `Context->InputData` for `UPCGLandscapeData` | **Set `LandscapeRef` explicitly** |
| `AlignToNearestSpline` | Iterates world on game thread (cap 16 actors with `USplineComponent`) | **Populate `SplineActors` explicitly** |
| `BoundaryDetect` | — | Works in runtime as-is |
| `DistanceTag` | — | Works in runtime as-is |
| `GridSnap` | — | Works in runtime as-is |

**Why**: the fallbacks depend on editor-available world iteration and editor-populated input data. They are not reliable in cooked contexts and must not be relied on for shipping builds.

---

## 6.2 Runtime Generation — Required Steps

1. **Spawn** a `PCGVolume` (or a custom actor owning a `UPCGComponent`).
2. **Assign the graph**: set the `PCGComponent->Graph` to your `PCGT_*` asset.
3. **Override parameters** on the graph instance:
   - For spline templates: set `SplineActors` to the concrete spline actors present at runtime.
   - For landscape-projection templates: set `LandscapeRef` to the concrete `ALandscape` instance.
4. **Set the seed**.
5. **Call Generate**.

### Minimal C++ example

```cpp
// Copyright (c) 2026 Tom Leon Vincent Hanke

void AMyRuntimeSpawner::SpawnAndGenerate()
{
    UWorld* World = GetWorld();
    if (!World) return;

    // 1. Spawn volume.
    APCGVolume* Volume = World->SpawnActor<APCGVolume>(
        APCGVolume::StaticClass(), FTransform(Location));
    if (!Volume) return;

    Volume->SetActorScale3D(FVector(50.f, 50.f, 5.f));

    UPCGComponent* PCG = Volume->FindComponentByClass<UPCGComponent>();
    if (!PCG) return;

    // 2. Assign graph.
    PCG->SetGraph(TemplateGraph); // TemplateGraph: UPCGGraphInterface*

    // 3. Override references BEFORE generation.
    //    Use the parameter names exposed on the specific template.
    //    Example for PCGT_SplineRoad:
    //    - SplineActors : TArray<TSoftObjectPtr<AActor>>
    //    - LandscapeRef : TSoftObjectPtr<ALandscape>
    //
    //    Set them via the graph instance parameter overrides API.

    // 4. Seed.
    PCG->Seed = 42;

    // 5. Generate.
    PCG->Generate(/*bForce=*/true);
}
```

> The graph-instance parameter override API depends on the exact parameter layout of your template. Inspect the `PCGT_*` graph's exposed parameters in the editor and set them via the corresponding override container.

---

## 6.3 Cooking / Packaging

Ensure all referenced assets are cookable:

- Template graphs (`PCGT_*`).
- Presets (`PCGPreset_*`) and all mesh assets they reference.
- Any `LandscapeRef` / `SplineActors` must be in a level that is cooked (or loaded at runtime before generation).

For plugin-content dependencies, verify they are in the plugin's **Content** folder so `BuildPlugin -configuration=Shipping` cooks them.

---

## 6.4 Determinism at Runtime

- Same graph + same seed + same inputs → same output.
- Iteration order of unordered collections can break determinism. PCGProKit's custom nodes preserve input order.
- If determinism matters across sessions, pin all inputs explicitly (no fallback world-scans).

---

## 6.5 Performance Considerations

- `AlignToNearestSpline` scales as O(actors × splines × points). Budget the actor count; the editor fallback caps at 16 for that reason.
- `ProjectToLandscape` is typically the cheapest projection node; landscape height sampling is O(1) per point.
- `BoundaryDetect` uses a spatial pass at cost O(N) with a neighborhood lookup; `EdgeThreshold` does not materially change complexity but a very large value widens the edge attribute set.
- Avoid regenerating large PCG volumes per-frame. Trigger generation on explicit events (level load, player proximity, gameplay trigger).

---

## 6.6 Dedicated Server

- If generation affects gameplay (navigation, collision), generate on the server and replicate the resulting actor state, **not** the PCG generation itself. PCG output is not automatically replicated.
- For visual-only props, generate on the client.

---

## 6.7 Common Runtime Pitfalls

- Relying on the editor fallback in packaged builds → empty output. Always set references explicitly.
- Forgetting to call `Generate(true)` after changing parameters at runtime.
- Generating before the target `Landscape` or spline actor is fully loaded (streaming). Gate generation behind `OnLevelLoaded` / streaming completion.
- Forgetting to include referenced meshes in the cook (unreferenced presets are stripped).

---

**Next:** [07_API_Reference](07_API_Reference.md)
