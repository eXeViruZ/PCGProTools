# PCG Pro Tools — Documentation Index

**Version:** 1.1.0 · **Engine:** Unreal Engine 5.7 · **Author:** Tom Leon Vincent Hanke

---

## Documents

| # | File | What you find there |
|---|---|---|
| — | [README](../README.md) | Overview, quickstart, node list, layout |
| 01 | [Installation](01_Installation.md) | Requirements, install options, first launch checklist, upgrade from v1.0 |
| 02 | [Workflow](02_Workflow.md) | Toolbar, Template Library, Graph Inspector, Debug Overlay, Project Settings |
| 03 | [Templates](03_Templates.md) | All 17 templates — purpose, required actors, key nodes |
| 04 | [Nodes](04_Nodes.md) | All 17 nodes — full property tables, tips, and use cases |
| 05 | [Presets](05_Presets.md) | Preset system, applying, saving snapshots, manual authoring, property reference |
| 06 | [Runtime Usage](06_Runtime_Usage.md) | Cooked builds, async execution, explicit references, packaging checklist |
| 07 | [API Reference](07_API_Reference.md) | C++ classes, enums, Build.cs, module structure |
| 08 | [Troubleshooting](08_Troubleshooting.md) | Common problems and fixes |
| 09 | [Changelog](09_Changelog.md) | v1.1.0 and v1.0.0 release notes |

---

## Quick answers

| I want to… | Go to |
|---|---|
| Install the plugin | [01 — Installation](01_Installation.md) |
| Spawn a ready-made PCG graph | [02 — Workflow · Template Library](02_Workflow.md#template-library) |
| Tune node parameters without opening the graph | [02 — Workflow · Graph Inspector](02_Workflow.md#graph-inspector) |
| See what each node does and what properties it has | [04 — Nodes](04_Nodes.md) |
| Save and reapply a parameter configuration | [05 — Presets](05_Presets.md) |
| Make the plugin work in a packaged game | [06 — Runtime Usage](06_Runtime_Usage.md) |
| Upgrade from v1.0 | [01 — Installation · Upgrading from v1.0](01_Installation.md#upgrading-from-v10) |
| Fix a broken node from v1.0 (SurfaceSlopeFilter) | [08 — Troubleshooting · 8.3](08_Troubleshooting.md#83--pcg-pro-slope-filter-node-is-missing--broken-graph-from-v10) |
| Fix spline nodes producing no output | [08 — Troubleshooting · 8.4](08_Troubleshooting.md#84--spline-nodes-produce-no-output-in-cookedasync-builds) |
| Understand what changed in v1.1 | [09 — Changelog](09_Changelog.md) |
| Use the C++ API | [07 — API Reference](07_API_Reference.md) |

---

## Node categories at a glance

| Category | Nodes |
|---|---|
| Filter | Blue Noise Scatter, Height Filter, Landscape Layer Sampler, Noise Mask Filter, Slope Filter, Spline Avoidance, Water Body Avoidance, Weighted Selection By Tag |
| Spatial | Align To Nearest Spline, Grid Snap, Project To Landscape, Relax Points |
| Sampler | Clump Scatter |
| Metadata | Boundary Detect, Distance To Nearest Tag |
| Density | Density Falloff |
| Debug | Print Stats |

---

Support: [discord.gg/vgpmnN6nCR](https://discord.gg/vgpmnN6nCR) · [GitHub Issues](https://github.com/eXeViruZ/PCGProTools/issues)
