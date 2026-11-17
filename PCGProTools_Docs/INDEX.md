# PCG Pro Tools — Documentation Index

**Version:** 3.0.0 · **Engine:** Unreal Engine 5.8 · **Author:** Tom Leon Vincent Hanke

---

## Documents

| # | File | Contents |
|---|---|---|
| — | [README](../README.md) | Product overview, requirements, quick start, and content totals |
| 01 | [Installation](01_Installation.md) | Installation, first launch, demo maps, and upgrading from v1.1.1 or v2.0.0 |
| 02 | [Workflow](02_Workflow.md) | Graph Inspector, Template Library, Setup Check, Debug Overlay |
| 03 | [Templates](03_Templates.md) | All 23 templates, requirements, categories, and Add to Level behavior |
| 04 | [Nodes](04_Nodes.md) | All 22 nodes, properties, runtime notes, and usage guidance |
| 05 | [Presets](05_Presets.md) | All 13 presets and the preset capture/apply workflow |
| 06 | [Runtime Usage](06_Runtime_Usage.md) | Runtime matrix, cooked references, determinism, packaging checklist |
| 07 | [API Reference](07_API_Reference.md) | Runtime/editor modules, public classes, enums, settings, Build.cs |
| 08 | [Troubleshooting](08_Troubleshooting.md) | Common setup and runtime problems |
| 09 | [Changelog](09_Changelog.md) | v3.0.0 and v2.0.0 release notes and historical releases |
| 10 | [Quick Setup](10_Quick_Setup.md) | Start from a template with your own meshes, step by step |
| 11 | [Generation Report and Setup Check](11_Generation_Report_and_Setup_Check.md) | See why a PCG component produced its points and check its setup |
| 12 | [Multi-Mesh: settings per mesh](12_Multi_Mesh_Per_Mesh_Settings.md) | Own scale, yaw, tilt, and height offset for each mesh |
| 13 | [Saved Setups](13_Saved_Setups.md) | Save a Quick Setup and reuse it in another level |
| 14 | [Editable copies and Reset to Template](14_Editable_Copies_and_Reset_to_Template.md) | Add Copy to Level and Reset to Template |
| 15 | [Multiple road splines](15_Multiple_Road_Splines.md) | Roadside Generator and Spline Road with more than one road |
| 16 | [Exclusion zones](16_Exclusion_Zones.md) | Keep scatter away from tagged actors |
| 17 | [Showcase Valley](17_Showcase_Valley.md) | The example level included in the plugin content |
| 18 | [Known limitations](18_Known_Limitations.md) | What the 3.0 tools do not do |
| 19 | [Upgrading from 2.0](19_Upgrading_From_2.0.md) | What changes for existing projects, saved setups, and C++ users |

---

## Quick answers

| I need to… | Go to |
|---|---|
| Install or upgrade to v3.0.0 | [01 — Installation](01_Installation.md) |
| Build a graph with my own meshes | [10 — Quick Setup](10_Quick_Setup.md) |
| Add a ready-to-use graph | [02 — Template Library](02_Workflow.md#template-library) |
| Create an editable project copy | [02 — Create Editable Copy](02_Workflow.md#create-editable-copy) |
| Tune a selected PCG actor | [02 — Graph Inspector](02_Workflow.md#graph-inspector) |
| Change or lock the PCG component seed | [02 — Seed Controls](02_Workflow.md#seed-controls) |
| Check required tags and actors | [02 — Setup Check](02_Workflow.md#setup-check) |
| Understand why a result has so few points | [11 — Generation Report](11_Generation_Report_and_Setup_Check.md) |
| Reuse a setup in another level | [13 — Saved Setups](13_Saved_Setups.md) |
| Open the example level | [17 — Showcase Valley](17_Showcase_Valley.md) |
| Understand a template | [03 — Templates](03_Templates.md) |
| Understand a node or property | [04 — Nodes](04_Nodes.md) |
| Apply or save presets | [05 — Presets](05_Presets.md) |
| Package a runtime PCG workflow | [06 — Runtime Usage](06_Runtime_Usage.md) |
| Use the C++ API | [07 — API Reference](07_API_Reference.md) |
| Diagnose a problem | [08 — Troubleshooting](08_Troubleshooting.md) |
| Review v3.0.0 and v2.0.0 changes | [09 — Changelog](09_Changelog.md) |
| Check limits and upgrade notes | [18 — Known limitations](18_Known_Limitations.md), [19 — Upgrading from 2.0](19_Upgrading_From_2.0.md) |

---

## Node categories

| Category | Nodes |
|---|---|
| Scatter | Blue Noise Scatter, Clump Scatter, Grid Snap |
| Spatial | Spline Offset, Align To Nearest Spline |
| Landscape | Landscape Layer Sampler, Project To Landscape |
| Utility | Print Stats |
| Filter | Instance Variation, Distance LOD, Random Subset, Biome Mask, Spline Avoidance, Water Body Avoidance, Boundary Detect, Curvature Filter, Density Falloff, Distance To Nearest Tag, Height Filter, Noise Mask Filter, Relax Points, Weighted Selection By Tag |

> Categories above match the node palette classification (unchanged in v3.0.0). Template Library categories are assigned separately from template asset names.

---

## Version compatibility

| Plugin version | Engine |
|---|---|
| PCG Pro Tools 3.0.0 | Unreal Engine 5.8 (tested with 5.8.4) |
| PCG Pro Tools 2.0.0 | Unreal Engine 5.8 (previous release) |
| PCG Pro Tools 1.1.1 | Unreal Engine 5.7 |

The 3.0 features documented in pages 10–19 are part of the UE 5.8 build. The UE 5.7 build is v1.1.1.

Support: [discord.gg/vgpmnN6nCR](https://discord.gg/vgpmnN6nCR) · [GitHub Issues](https://github.com/eXeViruZ/PCGProTools/issues)
