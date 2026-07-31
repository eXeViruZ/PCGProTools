# PCG Pro Tools — Documentation Index

**Version:** 2.0.0 · **Engine:** Unreal Engine 5.8 · **Author:** Tom Leon Vincent Hanke

---

## Documents

| # | File | Contents |
|---|---|---|
| — | [README](../README.md) | Product overview, requirements, quick start, and content totals |
| 01 | [Installation](01_Installation.md) | Installation, first launch, demo maps, and v1.1.1 upgrade |
| 02 | [Workflow](02_Workflow.md) | Graph Inspector, Template Library, Setup Validator, Debug Overlay |
| 03 | [Templates](03_Templates.md) | All 23 templates, requirements, categories, and Add to Level behavior |
| 04 | [Nodes](04_Nodes.md) | All 22 nodes, properties, runtime notes, and usage guidance |
| 05 | [Presets](05_Presets.md) | All 13 presets and the preset capture/apply workflow |
| 06 | [Runtime Usage](06_Runtime_Usage.md) | Runtime matrix, cooked references, determinism, packaging checklist |
| 07 | [API Reference](07_API_Reference.md) | Runtime/editor modules, public classes, enums, settings, Build.cs |
| 08 | [Troubleshooting](08_Troubleshooting.md) | Common v2 setup and runtime problems |
| 09 | [Changelog](09_Changelog.md) | v2.0.0 release notes and historical releases |

---

## Quick answers

| I need to… | Go to |
|---|---|
| Install or upgrade to v2.0.0 | [01 — Installation](01_Installation.md) |
| Add a ready-to-use graph | [02 — Template Library](02_Workflow.md#template-library) |
| Create an editable project copy | [02 — Create Editable Copy](02_Workflow.md#create-editable-copy) |
| Tune a selected PCG actor | [02 — Graph Inspector](02_Workflow.md#graph-inspector) |
| Change or lock the PCG component seed | [02 — Seed Controls](02_Workflow.md#seed-controls) |
| Validate required tags and actors | [02 — Setup Validator](02_Workflow.md#setup-validator) |
| Understand a template | [03 — Templates](03_Templates.md) |
| Understand a node or property | [04 — Nodes](04_Nodes.md) |
| Apply or save presets | [05 — Presets](05_Presets.md) |
| Package a runtime PCG workflow | [06 — Runtime Usage](06_Runtime_Usage.md) |
| Use the C++ API | [07 — API Reference](07_API_Reference.md) |
| Diagnose a problem | [08 — Troubleshooting](08_Troubleshooting.md) |
| Review v2.0.0 changes | [09 — Changelog](09_Changelog.md) |

---

## Node categories

| Category | Nodes |
|---|---|
| Scatter | Blue Noise Scatter, Clump Scatter, Grid Snap |
| Spatial | Spline Offset, Align To Nearest Spline |
| Landscape | Landscape Layer Sampler, Project To Landscape |
| Utility | Print Stats |
| Filter | Instance Variation, Distance LOD, Random Subset, Biome Mask, Spline Avoidance, Water Body Avoidance, Boundary Detect, Curvature Filter, Density Falloff, Distance To Nearest Tag, Height Filter, Noise Mask Filter, Relax Points, Weighted Selection By Tag |

> Categories above match the node palette classification in v2.0.0. Template Library categories are assigned separately from template asset names.

---

## Version compatibility

| Plugin version | Engine |
|---|---|
| PCG Pro Tools 2.0.0 | Unreal Engine 5.8 |
| PCG Pro Tools 1.1.1 | Unreal Engine 5.7 |

Support: [discord.gg/vgpmnN6nCR](https://discord.gg/vgpmnN6nCR) · [GitHub Issues](https://github.com/eXeViruZ/PCGProTools/issues)
