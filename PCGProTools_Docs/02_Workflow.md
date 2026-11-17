# 02 — Workflow

This page covers the v3.0.0 editor workflow: the toolbar, Graph Inspector, Setup Check, Template Library, editable copies, and Debug Overlay. Quick Setup, the Generation Report, and the other 3.0 workflows have their own pages ([10](10_Quick_Setup.md) to [17](17_Showcase_Valley.md)).

---

## Toolbar and tabs

PCG Pro Tools registers three Level Editor toolbar commands:

| Tool | Purpose |
|---|---|
| **Debug Overlay** | Toggle viewport PCG bounds/debug rendering |
| **Template Library** | Search, filter, add, copy graph templates, or start Quick Setup |
| **Graph Inspector** | Tune selected PCG actors, manage the component seed and presets, run Setup Check, read the Generation Report |

The tools can also be opened through **Window → PCG Pro Tools**. Template Library and Graph Inspector are dockable tabs.

---

## Template Library

The Template Library contains **23 graph templates**.

### Search and filters

- Search is case-insensitive.
- Search matches the template **asset name** or its **purpose** text.
- Search and category filters work together.
- Categories: **All, Scatter, Filter, Spline, Landscape, Water, Utility**.

Each template shows its purpose, its requirements, and whether **Quick Setup…** is available for it (20 of 23 templates). Terms from asset names such as `Spline`, `Forest`, `Distance`, or `Biome` always work.

### Add to Level

1. Open **Template Library**.
2. Search or choose a category.
3. Select a template.
4. Click **Add to Level**.
5. The plugin spawns an `APCGVolume` at the camera look point.
6. Default bounds are approximately `20000 × 20000 × 5000`.
7. The template graph is assigned to the PCG component.
8. Landscape-dependent templates first verify that a Landscape exists.

Generation behavior depends on the template:

- Most landscape/scatter templates use **On Load**.
- Spline and water workflows commonly use **On Demand**.
- Roadside Generator, Spline Road, and Spline Avoidance create their documented helper spline actors.
- Not every template creates helper actors. Review the [Template Reference](03_Templates.md).

### Quick Setup and Add Copy to Level

- **Quick Setup…** builds your own editable graph with your meshes. See [10 — Quick Setup](10_Quick_Setup.md).
- **Add Copy to Level** creates an editable copy in `/Game/PCG` and places a volume that uses it. See [14 — Editable copies and Reset to Template](14_Editable_Copies_and_Reset_to_Template.md).

### Create Editable Copy

Use the template context menu **Copy** action to create a project-owned copy.

- Destination: `/Game/PCG/`
- Names: `{Template}_Copy`, `{Template}_Copy2`, `{Template}_Copy3`, …
- The copied graph opens automatically in the PCG Graph Editor.
- The operation creates an asset and is not undoable through Ctrl+Z.

Use editable copies instead of modifying templates in plugin content.

---

## Graph Inspector

The Graph Inspector tab is implemented by `SPCGQuickTuneWidget`.

### Selecting a component

1. Select a `PCGVolume` or another actor with a `PCGComponent`.
2. Open **Graph Inspector**.
3. The Inspector uses the selected PCG component and its graph.

### Upper panel: exposed graph parameters

The upper `SDetailsView` displays native `UPCGGraphInstance` parameters that have been exposed from the graph.

- These values are editable.
- This panel is empty when the graph has no exposed parameters.

### Lower panel: node parameters (read-only overview)

The lower panel (**Node Parameters (Read-only Inspector)**) lists the supported `PCG_Overridable` properties of the nodes in the graph.

- Properties are grouped by node and shown as text values.
- The list is an overview: change node values in the PCG Graph Editor, through [Quick Setup](10_Quick_Setup.md), or with the Reset buttons.
- Each supported property has **↺ Reset to C++ default**. On copies made by this version, **Template** sets a value back to the shipped template value and **Reset node to template** resets a whole node (see [14](14_Editable_Copies_and_Reset_to_Template.md)).
- Shipped plugin graphs are protected: Reset and Apply Preset first offer **Make Editable Copy**, which creates a project copy in `/Game/PCG/` and assigns it to the selected volume.

This panel is independent of graph-exposed parameters. It can contain entries even when the upper panel is empty.

---

## Seed Controls

The Graph Inspector changes `PCGComponent.Seed`. Native PCG nodes use this seed; **PCG Pro Tools nodes do not**:

- Blue Noise Scatter and Weighted Selection By Tag have their own **Seed** setting.
- Clump Scatter, Instance Variation, Random Subset, and Spline Offset always use a fixed seed, so the same input gives the same result and the seed buttons do not vary them.
- Noise Mask Filter has no seed; use its noise offset.

A seed change is one Undo step.

Available controls:

- Previous seed: `-1`
- Next seed: `+1`
- Randomize
- Direct seed entry
- Seed Lock

When Seed Lock is enabled:

- Previous and next controls are blocked.
- Randomize is blocked.
- Direct editing is read-only.

Seed Lock is widget state. It is not stored on the component, in the level, or in config, and resets when the Inspector/editor session is recreated.

When Auto-Regenerate is enabled, a seed change regenerates the selected component.

---

## Point Count

The Graph Inspector reads `PCGComponent->GetPCGData()` and counts points across all available component outputs.

The value refreshes after regeneration. It is a component-level total, not a count for one selected node.

---

## Auto-Regenerate and regeneration controls

### Auto-Regenerate

When enabled, a completed supported property edit triggers regeneration through `OnPropertyFinishedChanging`.

Disable Auto-Regenerate when making several changes before a manual refresh.

### Manual controls

- **Regenerate Selected** regenerates selected PCG actors/components.
- **Regenerate All** runs the broader regeneration action exposed by the widget.

### Undo and Redo

The Graph Inspector registers as an editor Undo client. After Undo or Redo of a change to a selected PCG component or its graph, it refreshes its panels and the Point Count. Unreal's own PCG refresh updates the affected components; PCG Pro Tools does not clear the PCG cache or force a full regeneration.

A component with **Regenerate In Editor** turned off is not regenerated automatically after Undo or Redo. Select it and use **Regenerate Selected**.

**Regenerate Selected** regenerates the selected components, including components that were already generated and unchanged. It does not clear Unreal's PCG cache (see [18 — Known limitations](18_Known_Limitations.md)).

---

## Presets

The Graph Inspector provides:

- **Apply Preset**
- **Save as Preset**
- **Refresh Presets**

Apply writes compatible property overrides into matching node settings classes. Incompatible overrides are skipped.

Save captures supported `PCG_Overridable` Float, Int32, Bool, and enum-as-Int32 values into a `UPCGProKitPresetAsset` through the Content Browser save dialog.

See [05 — Presets](05_Presets.md).

---

## Setup Check

The **Setup Check** section of the Graph Inspector checks the selected PCG actor's graph against the level without opening a dialog. **Validate** runs it and opens the section; **Re-check** runs it again, for example after you changed actor tags, splines, or the Landscape. It also runs when you select another PCG actor or when the actor regenerates.

Each result is an **Error** (certainly wrong or empty), a **Warning** (a node probably reads nothing), or **Info** (an optional input is absent, which is a valid setup, for example Spline Avoidance with nothing to avoid). It checks:

| Workflow | Check |
|---|---|
| Spline Avoidance, Align To Nearest Spline | Configured tag/reference and matching loaded spline actors |
| Distance To Nearest Tag, Get Actor Data | The actor tag the graph reads is carried by a loaded actor |
| Landscape workflows | A Landscape exists, overlaps the volume, and the layer names exist |
| Water Body Avoidance | Water Bodies exist (absent = Info) |
| Static Mesh Spawner | A usable mesh is assigned |

Setup Check is read-only and does not repair the level. It checks loaded actors only (World Partition cells that are not loaded can show missing actors) and one PCG actor at a time. Details and limits: [11 — Generation Report and Setup Check](11_Generation_Report_and_Setup_Check.md).

---

## Debug Overlay

The Debug Overlay draws PCG component bounds and labels in the editor viewport.

### Scope

Configure under **Project Settings → Plugins → PCG Pro Tools**:

| Setting | Default |
|---|---:|
| `OverlayScope` | SelectedOnly |
| `OverlayViewportMaxDistance` | 100000 cm |
| `MaxPointsPerComponent` | 5000 |
| `bShowLabelsOnlyForSelected` | true |
| `AttributeLabelDrawDistance` | 5000 cm |

Available scope values:

- Selected Only
- All In Active Viewport
- All In Level

### Node Type Debug Filter

v2.0.0 adds a persistent node-type filter:

- `bEnableNodeTypeFilter`
- `DebugNodeTypeFilter`

The Graph Inspector checkboxes update the filter and save it through `SaveConfig()`.

The filter applies to PCG components generally, not only nodes supplied by PCG Pro Tools.

---

## Recommended workflow

1. Start from a template: **Quick Setup…** for your own meshes and an editable graph, or **Add to Level** / **Add Copy to Level**.
2. Use **Create Editable Copy** or Quick Setup before structural graph changes.
3. Select the PCG actor and open Graph Inspector.
4. Run **Setup Check** and fix Errors and Warnings.
5. Tune exposed parameters, node parameters in the Graph Editor, or Quick Setup values.
6. Explore a result with seed controls, then lock the chosen seed.
7. Apply or save a preset, or save a [Saved Setup](13_Saved_Setups.md).
8. Use the point count, the **Generation Report**, and the Debug Overlay to inspect the result.
9. Verify runtime reference requirements before packaging.

---

## Important runtime notes

- Landscape Layer Sampler is editor-only and becomes passthrough in non-editor builds.
- Spline tags work in editor and cooked builds. Direct soft actor references require the actor to be packaged and loaded.
- Assign `LandscapeRef` explicitly for cooked Height Filter and Project To Landscape workflows.
- Water Body Avoidance needs the Water plugin enabled in the consuming project.

---

Next: [03 — Templates](03_Templates.md)
