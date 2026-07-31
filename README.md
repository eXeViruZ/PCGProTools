<!-- Copyright (c) 2026 Tom Leon Vincent Hanke -->

# PCG Pro Tools

Production-ready custom PCG nodes, editor tools, graph templates, presets, and demo workflows for **Unreal Engine 5.8**.

PCG Pro Tools v2.0.0 extends Unreal Engine's built-in Procedural Content Generation Framework with:

- **22 custom C++ PCG nodes**
- **23 ready-to-use graph templates**
- **13 presets**
- **21 demo maps**
- Graph Inspector, Template Library, Setup Validator, and Debug Overlay tooling

No engine modifications are required.

---

## What's new in v2.0.0

### Five new custom PCG nodes

- **Instance Variation** — randomized scale, rotation, and point color
- **Spline Offset** — lateral point placement around spline-sampled directions
- **Distance LOD** — distance-based point-density reduction
- **Random Subset** — deterministic percentage-based point selection
- **Biome Mask** — radial density shaping with transition-boundary detection

### Six new templates

- `PCGT_InstanceVariation`
- `PCGT_SplineOffset`
- `PCGT_DistanceLOD`
- `PCGT_RandomSubset`
- `PCGT_BiomeMask`
- `PCGT_RoadsideGenerator`

### Editor workflow improvements

- Previous, next, randomize, direct-entry, and lockable component seed controls
- Point-count display in the Graph Inspector
- Direct editing and per-property reset for all supported `PCG_Overridable` properties
- Setup Validator for tags, Landscapes, Water Bodies, and supported workflow requirements
- Template search and category filters
- **Create Editable Copy** into `/Game/PCG/`
- Improved Add to Level setup
- Node Type Debug Filter with persistent configuration
- More reliable regeneration after Undo, Redo, and Reset

See the complete [v2.0.0 changelog](PCGProTools_Docs/09_Changelog.md).

---

## Requirements

| Requirement | Value |
|---|---|
| Unreal Engine | **5.8** |
| Required plugin | Built-in **PCG** plugin |
| Supported target platforms | Win64, Linux, Mac |
| Hard Water-plugin dependency | None |

> PCG Pro Tools v2.0.0 is built for UE 5.8. The previous v1.1.1 build remains the UE 5.7-compatible release.

---

## Quick start

1. Install PCG Pro Tools from Fab, or copy `PCGProKit/` into `<YourProject>/Plugins/PCGProKit/`.
2. Enable **PCG Pro Tools** and the built-in **Procedural Content Generation Framework** plugin.
3. Restart Unreal Editor.
4. Enable **Show Plugin Content** in the Content Browser.
5. Open the **Template Library**, choose a template, and select **Add to Level**.
6. Select the generated PCG actor and open the **Graph Inspector** to tune parameters.

Full setup instructions: [Installation](PCGProTools_Docs/01_Installation.md) and [Workflow](PCGProTools_Docs/02_Workflow.md).

---

## Editor tools

| Tool | Purpose |
|---|---|
| **Graph Inspector** | Edit exposed graph parameters and supported node properties, apply presets, control the component seed, validate setup, regenerate, and inspect point count |
| **Template Library** | Search, filter, add, and copy the 23 included templates |
| **Debug Overlay** | Draw PCG bounds and reduce viewport clutter with a persistent node-type filter |
| **Actor context menu** | Open a selected PCG actor directly in the Graph Inspector |

---

## Content overview

| Category | v1.1.1 | v2.0.0 |
|---|---:|---:|
| Custom PCG nodes | 17 | **22** |
| Graph templates | 17 | **23** |
| Presets | 4 | **13** |
| Demo maps | 15 | **21** |

PCG Pro Tools does **not** include a separate Hanke Unreal Tools environment-art pack. The templates and demo maps use example meshes and materials provided by Unreal Engine's built-in PCG plugin and Engine Content for visualization. These references can be replaced with assets from your own project.

The Landscape Layer Sampler demo also includes its required `LI_Grass` Landscape Layer Info asset inside PCG Pro Tools plugin content.

---

## Documentation

| # | Document |
|---|---|
| — | [Documentation Index](PCGProTools_Docs/INDEX.md) |
| 01 | [Installation](PCGProTools_Docs/01_Installation.md) |
| 02 | [Workflow](PCGProTools_Docs/02_Workflow.md) |
| 03 | [Templates](PCGProTools_Docs/03_Templates.md) |
| 04 | [Nodes](PCGProTools_Docs/04_Nodes.md) |
| 05 | [Presets](PCGProTools_Docs/05_Presets.md) |
| 06 | [Runtime Usage](PCGProTools_Docs/06_Runtime_Usage.md) |
| 07 | [API Reference](PCGProTools_Docs/07_API_Reference.md) |
| 08 | [Troubleshooting](PCGProTools_Docs/08_Troubleshooting.md) |
| 09 | [Changelog](PCGProTools_Docs/09_Changelog.md) |

---

## Important usage rules

- **Landscape Layer Sampler is editor-only.** In non-editor builds it passes all input points through unchanged.
- For cooked spline workflows, prefer `SplineActorTag`. Direct `SplineActors` soft references work only when the referenced actor is included in the package and loaded.
- Assign `LandscapeRef` explicitly for cooked Height Filter and Project To Landscape workflows.
- The Water plugin is optional, but Water Body Avoidance requires it to find `AWaterBody` actors.
- **Distance LOD and Biome Mask change point density; they do not delete points.** Use downstream density-aware filtering or spawning.
- Seed Lock is Graph Inspector widget state and is not persistent across editor restarts.
- Do not edit shipped templates or presets in plugin content. Use **Create Editable Copy** or duplicate them into project content.

---

## Repository layout

```text
PCGProKit/
  PCGProKit.uplugin
  Source/
    PCGProKit/            # Runtime module: 22 custom nodes, settings, preset system
    PCGProKitEditor/      # Editor module: toolbar, inspector, templates, overlay
  Content/
    Maps/                 # 21 demo maps
    Templates/            # 23 PCGT_* graph assets
    Presets/              # 13 preset assets

PCGProTools_Docs/         # Public documentation
README.md
```

---

## Support

Open a GitHub issue or join the Discord: [discord.gg/vgpmnN6nCR](https://discord.gg/vgpmnN6nCR)

Include:

- Unreal Engine version
- PCG Pro Tools version
- Reproducing template or demo map
- Relevant PCG actor and graph setup
- `LogPCGProKit` output
- Minimal reproduction steps

---

## License

© 2026 Tom Leon Vincent Hanke. Licensed under the Fab marketplace EULA under which you acquired the plugin.
