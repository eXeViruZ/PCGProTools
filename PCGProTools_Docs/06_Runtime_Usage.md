# 06 — Runtime Usage

This page documents PCG Pro Tools v2.0.0 behavior in Unreal Editor, PIE, Standalone launched from the editor, and packaged builds.

---

## Runtime matrix

| Node | Editor | PIE | Standalone | Cooked Dev/Shipping | Threading |
|---|---|---|---|---|---|
| Instance Variation | Full | Full | Full | Full | Async |
| Spline Offset | Full | Full | Full | Full | Async |
| Distance LOD | Full | Full | Full | Full | Async |
| Random Subset | Full | Full | Full | Full | Async |
| Biome Mask | Full | Full | Full | Full | Async |
| Blue Noise Scatter | Full | Full | Full | Full | Async |
| Boundary Detect | Full | Full | Full | Full | Async |
| Clump Scatter | Full | Full | Full | Full | Async |
| Density Falloff | Full | Full | Full | Full | Async |
| Distance To Nearest Tag | Full | Full | Full | Full | Async |
| Grid Snap | Full | Full | Full | Full | Async |
| Height Filter | Full | Full | Full | Full with explicit Landscape reference | Async |
| Landscape Layer Sampler | Full | Full | Full when launched from Unreal Editor | Passthrough | Main thread |
| Noise Mask Filter | Full | Full | Full | Full | Async |
| Print Stats | Passthrough + logging | Passthrough + logging | Passthrough | Passthrough | Async |
| Project To Landscape | Full | Full | Full | Full with explicit Landscape reference | Async |
| Relax Points | Full | Full | Full | Full | Async |
| Curvature Filter | Full | Full | Full | Full | Async |
| Spline Avoidance | Full | Full | Full | Full | Main thread |
| Align To Nearest Spline | Full | Full | Full | Full | Main thread |
| Water Body Avoidance | Full | Full | Full | Full when Water plugin/actors are available | Main thread |
| Weighted Selection By Tag | Full | Full | Full | Full | Async |

---

## Landscape Layer Sampler

Landscape Layer Sampler performs actual layer-weight sampling only in editor builds.

In non-editor builds it:

1. Logs a warning.
2. Copies input data to output unchanged.
3. Does not filter points.
4. Does not modulate density.

Do not use it as a required runtime gameplay-generation step.

---

## Landscape references

### Height Filter

When `bUseLandscapeReference` is enabled, set `LandscapeRef` explicitly for cooked generation.

### Project To Landscape

Set `LandscapeRef` explicitly for cooked generation.

World discovery that appears to work in the editor should not be treated as a packaging guarantee.

---

## Spline references

Spline Avoidance and Align To Nearest Spline always execute on the main thread.

### SplineActorTag

`SplineActorTag` works in:

- Editor
- PIE
- Standalone
- Cooked Development
- Cooked Shipping

It is the recommended configuration for cooked workflows.

### SplineActors soft references

Direct soft references work when the target actor:

- Is included in the package
- Is loaded when the graph executes
- Is not located only in an unloaded streamed cell

Persistent-level actors are the safest direct-reference targets.

### World scan fallback

World scanning is available in editor/PIE/Standalone contexts. Do not rely on blind world scanning in cooked builds.

---

## Water Body Avoidance

The plugin does not link directly against the Water plugin.

The node walks actor class hierarchies and checks for the base class name `WaterBody`, which covers `AWaterBody` subclasses such as rivers, lakes, oceans, and custom Water Body subclasses.

Without the Water plugin:

- No Water Body actors can be found.
- Input points pass through unchanged.

For packaged projects:

- Enable the Water plugin.
- Ensure Water Body actors are packaged and loaded.
- Use explicit actor references where appropriate.

---

## Seed and determinism

Randomized nodes include:

- Instance Variation
- Spline Offset
- Random Subset
- Blue Noise Scatter
- Clump Scatter
- Noise Mask Filter
- Weighted Selection By Tag

The Graph Inspector edits `PCGComponent.Seed`.

### Editor deterministic setting

`UPCGProKitSettings` contains:

- `bDeterministicMode`
- `DefaultRandomSeed` (default 42)

These settings are stored in Editor config.

### Runtime determinism

For runtime determinism:

1. Set `PCGComponent.Seed` explicitly.
2. Keep graph inputs and actor references stable.
3. Avoid relying on non-deterministic world discovery.
4. Confirm the same streamed actors are loaded before generation.

---

## Density-only nodes

Distance LOD and Biome Mask do not remove points. They update Density.

When zero-density points must be eliminated, add a downstream Density Filter or another density-aware stage.

---

## Instance Variation color

Instance Variation writes point Color. It does not directly populate instance custom data.

To use color in spawned instances:

1. Enable `bApplyColorAsPerInstanceCustomData` in the Static Mesh Spawner.
2. Read Per Instance Custom Data indices 0, 1, and 2 in the material.
3. Use those values as RGB.

Verify this setup in the packaged target platform.

---

## Example visualization assets

The templates and demo maps use example assets from Unreal Engine's built-in PCG plugin and regular Engine Content. Common references include assets under:

```text
/PCG/SampleContent/
```

These assets are visualization defaults, not a separate Hanke Unreal Tools art pack. Replace them with project assets for production content.

`Demo_LandscapeLayerSampler` includes its `LI_Grass` Landscape Layer Info asset in plugin content. `Demo_GridSnap` uses `/Engine/BasicShapes/Cube` and does not require the VREditor plugin.

---

## Packaging checklist

- [ ] Project uses Unreal Engine 5.8.
- [ ] PCG Pro Tools and PCG plugins are enabled.
- [ ] `LandscapeRef` is assigned for cooked landscape-reference nodes.
- [ ] Spline workflows use `SplineActorTag` or packaged/loaded actor references.
- [ ] Water plugin is enabled when Water Body Avoidance is used.
- [ ] Required Water Body and spline actors are loaded when generation occurs.
- [ ] Landscape Layer Sampler is not required for non-editor runtime output.
- [ ] Distance LOD/Biome Mask have downstream density handling.
- [ ] Instance color transfer is configured in the spawner/material.
- [ ] Example PCG/Engine meshes have been replaced where project-specific production assets are required.
- [ ] Customized templates and presets are stored in project content.
- [ ] Runtime seeds are set explicitly when deterministic results are required.

---

Next: [07 — API Reference](07_API_Reference.md)
