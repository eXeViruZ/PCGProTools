# 02 — Workflow

This page covers the day-to-day editor workflow with PCG Pro Tools v1.1: the toolbar, the Graph Inspector, the Template Library, and the Debug Overlay.

---

## The Toolbar

PCG Pro Tools adds three buttons to the Level Editor toolbar:

| Button | Name | Shortcut |
|---|---|---|
| 🔲 | Debug Overlay | toolbar toggle |
| 📋 | Template Library | toolbar button |
| 🔍 | Graph Inspector | toolbar button |

All three open as dockable tabs and can be repositioned freely within the editor.

---

## Template Library

The Template Library gives you a one-click way to spawn a pre-built PCG graph into the current level.

**How to use:**
1. Click the **Template Library** toolbar button.
2. Browse the list of 17 templates.
3. Click **Add to Level** on the template you want.
4. The plugin spawns a **PCGVolume** at the camera position, assigns the template graph, and (for templates that require it) spawns any helper actors (splines, etc.) and wires them up automatically.
5. Press **Generate** to run the graph.

**Templates that require a Landscape** (e.g. `PCGT_HillsideVegetation`, `PCGT_LandscapeLayerSampler`) will warn you if no Landscape actor is present in the level before spawning.

**Templates that require a spline** (e.g. `PCGT_SplineRoad`, `PCGT_SplineAvoidance`) automatically spawn a helper spline actor tagged with the correct Actor Tag and assign it to the node's `SplineActors` reference.

---

## Graph Inspector

The Graph Inspector (formerly called Quick Tune) lets you inspect and edit node parameters of a selected PCG actor without opening the PCG Graph editor.

**How to use:**
1. Select a **PCGVolume** or any actor with a **PCGComponent** in the level.
2. Click the **Graph Inspector** toolbar button (or right-click the actor → **Open in PCG Graph Inspector**).
3. The top half shows the graph's **exposed parameters** (native UE Graph Instance parameters).
4. The bottom half shows a **read-only overview** of all `PCG_Overridable` node properties across every node in the graph, grouped by node.

> **Note:** To make a parameter directly editable, open the PCG Graph, right-click the property on a node, and choose **Expose to Graph Instance**. It will then appear in the editable top section of the Inspector.

### Auto-Regenerate

When **Auto-Regenerate** is enabled (checkbox at the top of the Inspector), the PCG graph re-generates automatically whenever you finish editing an exposed parameter. Disable it when tweaking multiple parameters at once to avoid redundant regeneration.

### Presets in the Graph Inspector

The Inspector has a **Preset** dropdown:

- **Apply Preset** — writes all stored overrides from the selected preset into the matching nodes of the current graph. Supports Undo.
- **Save as Preset** — opens a save dialog, captures all current `PCG_Overridable` values from the graph as a new `UPCGProKitPresetAsset`, and saves it to your chosen location.

See [`05_Presets.md`](05_Presets.md) for the full preset workflow.

---

## Debug Overlay

The Debug Overlay draws bounding boxes around PCG actors in the editor viewport so you can see at a glance which actors have been generated.

**Toggle:** click the **Debug Overlay** toolbar button. Click again to disable.

### Scope settings

Control which actors are drawn via **Project Settings → Plugins → PCG Pro Tools → Debug Overlay**:

| Scope | Description |
|---|---|
| Selected Only | (default) Only selected PCG actors |
| All In Active Viewport | All PCG actors within `OverlayViewportMaxDistance` of the camera |
| All In Level | Every PCG actor in the editor world (can drop FPS on large levels) |

Additional settings:

| Setting | Default | Description |
|---|---|---|
| `OverlayViewportMaxDistance` | 100 000 cm | Draw distance for the viewport scope |
| `MaxPointsPerComponent` | 5 000 | Max points processed per component for overlay labels |
| `bShowLabelsOnlyForSelected` | true | Attribute labels only on selected actors |
| `AttributeLabelDrawDistance` | 5 000 cm | Max distance for drawing attribute labels |

---

## Project Settings

**Edit → Project Settings → Plugins → PCG Pro Tools** exposes:

### Performance Guards

| Setting | Default | Description |
|---|---|---|
| `DensityCapWarningThreshold` | 1 000 000 | Shows an editor notification when a node outputs more points than this. Set to 0 to disable. |

### Determinism

| Setting | Default | Description |
|---|---|---|
| `bDeterministicMode` | false | Forces all PCG Pro Tools nodes that use randomness to use `DefaultRandomSeed` |
| `DefaultRandomSeed` | 42 | Global seed used when deterministic mode is on |

---

## Recommended workflow for a new scene

1. **Spawn a template** via the Template Library as a starting point.
2. **Open the Graph Inspector** — use the read-only node param overview to understand what each node is doing.
3. **Expose the parameters you care about** in the PCG Graph editor.
4. **Tune parameters** in the Graph Inspector with Auto-Regenerate on.
5. **Save a Preset** once you have settings you like, so you can reapply them to other actors.
6. **Enable the Debug Overlay** to check actor coverage at a glance.
7. **Use Print Stats nodes** in the graph during development — disable them (`bPrintEnabled = false`) before shipping.

---

## Important rules

- **Always assign `SplineActors` or `SplineActorTag` explicitly** when using Spline Avoidance or Align To Nearest Spline in async PCG execution (the default). The auto-discovery fallback only works in synchronous execution.
- **Always assign `WaterBodyActors` explicitly** in async execution for the same reason.
- **Always assign `LandscapeRef`** for Project To Landscape and Height Filter in cooked builds.

---

Next: [`03_Templates.md`](03_Templates.md)
