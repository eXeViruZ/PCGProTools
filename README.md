<!-- Copyright (c) 2026 Tom Leon Vincent Hanke -->

# PCG Pro Tools

Production-ready custom PCG nodes, editor tools, graph templates, presets, and demo workflows for **Unreal Engine 5.8**.

PCG Pro Tools v3.0.0 extends Unreal Engine's built-in Procedural Content Generation Framework with:

- **22 custom C++ PCG nodes**
- **23 ready-to-use graph templates**
- **13 presets**
- **21 demo maps** and the **Showcase Valley** example level
- **Quick Setup** and **Saved Setups** for your own editable PCG graphs
- Graph Inspector (Setup Check and Generation Report), Template Library, and Debug Overlay tooling

No engine modifications are required.

---

## What's new in v3.0.0

PCG Pro Tools 3.0 is a major workflow update for **Unreal Engine 5.8**. It is not a breaking update: 2.0 graphs, presets, and saved PCG components load without migration. The new tools are editor tools that create and edit normal PCG graphs; no new runtime node was added.

### New

- **Quick Setup** — turns 20 of the 23 templates into your own editable PCG graph with your meshes, weights, and parameters. It shows warnings before anything is created, never overwrites an existing asset, and can be reopened from the Graph Inspector to reconfigure the graph (one Undo step). See [Quick Setup](PCGProTools_Docs/10_Quick_Setup.md).
- **Multi-Mesh Scatter with settings per mesh** — own scale, random yaw, tilt, and Z offset for each of up to 16 meshes. See [Multi-Mesh](PCGProTools_Docs/12_Multi_Mesh_Per_Mesh_Settings.md).
- **Saved Setups (recipes)** — save a Quick Setup and reuse it in another level. See [Saved Setups](PCGProTools_Docs/13_Saved_Setups.md).
- **Generation Report** in the Graph Inspector — point change, what changed since the previous run, node warnings, slowest nodes, and **Find First Empty Node**. See [Generation Report](PCGProTools_Docs/11_Generation_Report_and_Setup_Check.md).
- **Add Copy to Level** and **Reset to Template** — place an editable copy of a template and reset values per value or per node. See [Editable copies](PCGProTools_Docs/14_Editable_Copies_and_Reset_to_Template.md).
- **Multiple road splines** for Roadside Generator and Spline Road, and **exclusion zones** by actor tag. See [Road splines](PCGProTools_Docs/15_Multiple_Road_Splines.md) and [Exclusion zones](PCGProTools_Docs/16_Exclusion_Zones.md).
- **Showcase Valley** — an example level with 14 PCG volumes: terrain, roads, and cabin are hand-built, vegetation is placed by PCG, with original meshes. See [Showcase Valley](PCGProTools_Docs/17_Showcase_Valley.md).

### Improved

- **Setup Check** in the Graph Inspector replaces the Setup Validator dialog.
- Template Library shows each template's purpose, requirements, and Quick Setup availability; search also covers the description.
- Performance Guard warnings also appear on the PCG node and in the Generation Report.
- Shipped plugin graphs are protected: Reset and Apply Preset first offer an editable copy.
- Preset saving never overwrites an asset; Apply Preset lists what was applied, unchanged, or skipped.

### Fixed

- **Regenerate Selected** also regenerates components that were already generated and unchanged.
- **Undo/Redo** no longer clears the PCG cache or regenerates everything; a seed change is one Undo step.
- The **Distance Tag template** now updates when a `POI` actor outside the volume bounds changes.
- Debug Overlay, Weighted Selection By Tag, and several tooltip and preset-description corrections.

Behaviour changes, such as hidden unused seed settings, are listed in [Upgrading from 2.0](PCGProTools_Docs/19_Upgrading_From_2.0.md). See the complete [changelog](PCGProTools_Docs/09_Changelog.md), including v2.0.0.

---

## Requirements

| Requirement | Value |
|---|---|
| Unreal Engine | **5.8** (v3.0.0 tested with 5.8.4) |
| Required plugin | Built-in **PCG** plugin |
| Supported target platforms | Win64, Linux, Mac |
| Hard Water-plugin dependency | None |

> PCG Pro Tools v3.0.0 is built for UE 5.8. The UE 5.7 build remains v1.1.1; it does not include the 3.0 features.

---

## Quick start

1. Install PCG Pro Tools from Fab, or copy `PCGProKit/` into `<YourProject>/Plugins/PCGProKit/`.
2. Enable **PCG Pro Tools** and the built-in **Procedural Content Generation Framework** plugin.
3. Restart Unreal Editor.
4. Enable **Show Plugin Content** in the Content Browser.
5. Open the **Template Library**, choose a template, and select **Add to Level** (the shipped template) or **Quick Setup…** (your own meshes and an editable graph).
6. Select the generated PCG actor and open the **Graph Inspector** to run **Setup Check**, tune parameters, and read the **Generation Report**.

Full setup instructions: [Installation](PCGProTools_Docs/01_Installation.md), [Workflow](PCGProTools_Docs/02_Workflow.md), and [Quick Setup](PCGProTools_Docs/10_Quick_Setup.md).

---

## Editor tools

| Tool | Purpose |
|---|---|
| **Graph Inspector** | Edit exposed graph parameters, view node parameters, apply presets, change the PCG component seed, run Setup Check, read the Generation Report, regenerate, and inspect point count |
| **Template Library** | Search, filter, add, copy, and start Quick Setup from the 23 included templates |
| **Debug Overlay** | Draw PCG bounds and reduce viewport clutter with a persistent node-type filter |
| **Actor context menu** | Open a selected PCG actor directly in the Graph Inspector |

---

## Content overview

| Category | v1.1.1 | v2.0.0 | v3.0.0 |
|---|---:|---:|---:|
| Custom PCG nodes | 17 | 22 | **22** |
| Graph templates | 17 | 23 | **23** |
| Presets | 4 | 13 | **13** |
| Demo maps | 15 | 21 | **21** |
| Example level | — | — | **Showcase Valley** |

PCG Pro Tools does **not** include an environment-art pack. The templates and demo maps use example meshes and materials provided by Unreal Engine's built-in PCG plugin and Engine Content for visualization. These references can be replaced with assets from your own project. **Showcase Valley** is example content: its original meshes were made for that level and are not a separate art pack.

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
| 10 | [Quick Setup](PCGProTools_Docs/10_Quick_Setup.md) |
| 11 | [Generation Report and Setup Check](PCGProTools_Docs/11_Generation_Report_and_Setup_Check.md) |
| 12 | [Multi-Mesh: settings per mesh](PCGProTools_Docs/12_Multi_Mesh_Per_Mesh_Settings.md) |
| 13 | [Saved Setups](PCGProTools_Docs/13_Saved_Setups.md) |
| 14 | [Editable copies and Reset to Template](PCGProTools_Docs/14_Editable_Copies_and_Reset_to_Template.md) |
| 15 | [Multiple road splines](PCGProTools_Docs/15_Multiple_Road_Splines.md) |
| 16 | [Exclusion zones](PCGProTools_Docs/16_Exclusion_Zones.md) |
| 17 | [Showcase Valley](PCGProTools_Docs/17_Showcase_Valley.md) |
| 18 | [Known limitations](PCGProTools_Docs/18_Known_Limitations.md) |
| 19 | [Upgrading from 2.0](PCGProTools_Docs/19_Upgrading_From_2.0.md) |

---

## Important usage rules

- **Landscape Layer Sampler is editor-only.** In non-editor builds it passes all input points through unchanged.
- For cooked spline workflows, prefer `SplineActorTag`. Direct `SplineActors` soft references work only when the referenced actor is included in the package and loaded.
- Assign `LandscapeRef` explicitly for cooked Height Filter and Project To Landscape workflows.
- The Water plugin is optional, but Water Body Avoidance requires it to find `AWaterBody` actors.
- **Distance LOD and Biome Mask change point density; they do not delete points.** Use downstream density-aware filtering or spawning.
- Seed Lock is Graph Inspector widget state and is not persistent across editor restarts.
- The Graph Inspector seed controls change the **PCG component seed**. Native PCG nodes use it; PCG Pro Tools nodes use their own node Seed (Blue Noise Scatter, Weighted Selection By Tag) or a fixed seed. See [Workflow](PCGProTools_Docs/02_Workflow.md#seed-controls).
- Do not edit shipped templates or presets in plugin content. Use **Quick Setup**, **Add Copy to Level**, **Create Editable Copy**, or **Make Editable Copy** to work on a project-owned graph.
- Showcase Valley is plugin content: save the level into your own project before you change it.

---

## Repository layout

```text
PCGProKit/
  PCGProKit.uplugin
  Source/
    PCGProKit/            # Runtime module: 22 custom nodes, settings, preset system
    PCGProKitEditor/      # Editor module: toolbar, inspector, templates, overlay
  Content/
    maps/                 # 21 demo maps and the Showcase_Valley example level
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
