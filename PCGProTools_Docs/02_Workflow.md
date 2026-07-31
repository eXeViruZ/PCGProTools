# 02 — Workflow

This page covers the v2.0.0 editor workflow: the toolbar, Graph Inspector, Setup Validator, Template Library, Create Editable Copy, and Debug Overlay.

---

## Toolbar and tabs

PCG Pro Tools registers three Level Editor toolbar commands:

| Tool | Purpose |
|---|---|
| **Debug Overlay** | Toggle viewport PCG bounds/debug rendering |
| **Template Library** | Search, filter, add, or copy graph templates |
| **Graph Inspector** | Tune selected PCG actors, manage seeds and presets, validate setup |

The tools can also be opened through **Window → PCG Pro Tools**. Template Library and Graph Inspector are dockable tabs.

---

## Template Library

The Template Library contains **23 graph templates**.

### Search and filters

- Search is case-insensitive.
- Search matches the template **asset name**.
- Search and category filters work together.
- Categories: **All, Scatter, Filter, Spline, Landscape, Water, Utility**.

> Search does not inspect a separate display name or description. Use terms from asset names such as `Spline`, `Forest`, `Distance`, or `Biome`.

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

### Lower panel: node parameters

The lower panel displays **all supported `PCG_Overridable` properties** from nodes in the graph.

- Properties are grouped by node.
- Supported direct editor types: Float, Int32, and Bool.
- `ClampMin` and `ClampMax` metadata are respected.
- Values are editable without opening the graph.
- Each supported property has a **Reset** button that restores the C++ default.

This panel is independent of graph-exposed parameters. It can contain entries even when the upper panel is empty.

---

## Seed Controls

The Graph Inspector changes `PCGComponent.Seed`.

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

### Undo, Redo, and Reset

The Graph Inspector registers as an editor Undo client.

After Undo, Redo, or Reset:

1. It waits for an active PCG generation to finish.
2. It flushes the selected component cache.
3. It calls local regeneration.

The retry ticker can cause a short delay, normally below one second.

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

## Setup Validator

Click **Validate** in the Graph Inspector.

The validator checks the selected graph for supported requirements, including:

| Workflow | Validation |
|---|---|
| Spline Avoidance | Configured tag/reference and matching loaded spline actor |
| Align To Nearest Spline | Configured tag/reference and matching loaded spline actor |
| Distance To Nearest Tag | Required Actor Selection Tag and matching loaded actor |
| Landscape workflows | A Landscape actor exists |
| Water Body Avoidance | An `AWaterBody`-derived actor exists when the Water plugin is loaded |
| Roadside Generator | An actor with tag `Road` exists |

The validator:

- Is read-only and does not repair the level.
- Checks loaded actors only.
- Can report missing actors in World Partition when the required cell is not loaded.
- Displays either a success dialog or a numbered warning list.

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

1. Add a template through the Template Library.
2. Use **Create Editable Copy** before structural graph changes.
3. Select the PCG actor and open Graph Inspector.
4. Validate the setup.
5. Tune exposed and node-level parameters.
6. Explore a result with seed controls, then lock the chosen seed.
7. Apply or save a preset.
8. Use the point count and Debug Overlay to inspect the result.
9. Verify runtime reference requirements before packaging.

---

## Important runtime notes

- Landscape Layer Sampler is editor-only and becomes passthrough in non-editor builds.
- Spline tags work in editor and cooked builds. Direct soft actor references require the actor to be packaged and loaded.
- Assign `LandscapeRef` explicitly for cooked Height Filter and Project To Landscape workflows.
- Water Body Avoidance needs the Water plugin enabled in the consuming project.

---

Next: [03 — Templates](03_Templates.md)
