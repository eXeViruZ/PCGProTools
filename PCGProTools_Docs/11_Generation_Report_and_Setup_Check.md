# 11 — Generation Report and Setup Check

The **Generation Report** in the Graph Inspector explains the result of the selected PCG component.

## What it shows

- **Status and points:** generated, generating, cancelled or cleaned up; the current point count, the previous count and the difference.
- **Since the previous run:** what changed — the graph, node settings, the seed, the volume (moved or resized), partitioning, or an Undo/Redo. If nothing changed in the graph, the report says so and points to level changes.
- **Node messages:** errors and warnings that Unreal's PCG reported for nodes, with the node name.
- **Slowest nodes:** CPU time of the last run.
- **Performance Guard warnings:** when a PCG Pro Tools node outputs more points than the Performance Guard threshold (Project Settings → Plugins → PCG Pro Tools, default 50,000), the warning appears on that node and in the node messages, and in the Output Log.

Setup problems (missing inputs, tags, Landscape) are listed in the **Setup Check** section above the report.

## Setup Check

The **Setup Check** section of the Graph Inspector checks the selected PCG actor's graph against the level, without opening a dialog. **Validate** runs it and opens the section; **Re-check** runs it again, for example after you changed actor tags, splines or the Landscape. It also runs when you select another PCG actor or when the actor regenerates. Each result shows the time and the actor it was checked for.

It checks:

- a graph is assigned and required node inputs are connected;
- actor tags read by Get Actor Data, Spline Avoidance, Align To Nearest Spline and Distance To Nearest Tag (for example no actor tagged `POI`);
- the Landscape: present when the graph reads one, overlapping the volume, and the layer names used by Landscape Layer Sampler;
- Water Bodies for Water Body Avoidance;
- Static Mesh Spawners without a usable mesh.

Severities: **Error** — the result is certainly wrong or empty; **Warning** — a node probably reads nothing; **Info** — an optional input is absent, which is a valid setup (for example Spline Avoidance or Water Body Avoidance with nothing to avoid, or an exclusion tag nobody carries).

It does not check other graph logic, partitioning or runtime generation settings, and it checks loaded actors only. It never changes the graph or the level.

## Find First Empty Node

When a result has 0 points, click **Find First Empty Node**. The tool runs one inspection and names the first node that outputs no points although its input had data — usually the node whose settings need attention.

## Good to know

- Node messages are kept by Unreal only for generations in the current editor session. After a restart the report still shows the point count (the Setup Check works at any time), and says that messages are not available yet; regenerate to get them.
- For partitioned or hierarchical components the report shows a note, because results are split across partition actors.
- The report never changes your graph. It runs no project-wide scans and does not update every frame.

---

Next: [12 — Multi-Mesh: settings per mesh](12_Multi_Mesh_Per_Mesh_Settings.md)
