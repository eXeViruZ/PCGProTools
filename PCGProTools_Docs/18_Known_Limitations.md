# 18 — Known limitations

- **Exclusion zones** use bounding boxes, not exact shapes, and are added only to new editable graphs.
- **Reset to Template** needs the origin information stored in copies made by this version; older or hand-made copies show the buttons disabled. Values are today's shipped template values.
- **Generation Report** node messages are available only for generations in the current editor session; partitioned and hierarchical components have limited detail.
- **Find First Empty Node** inspects one component at a time. It is not available while the component is generating, for partitioned components, or for components driven by the runtime generation system.
- **Multi-Mesh per-mesh settings** support up to 16 meshes; switching between shared and per-mesh mode creates a new graph.
- **Road splines** — a spline must carry the chosen tag; Quick Setup does not edit actor tags.
- **Quick Setup** supports 20 of 23 templates (not Biome Transition, Water Body Avoidance, Weighted Selection).
- Some templates give 0 points with their start values in unsuitable levels (for example Distance Tag without a `POI` actor); Quick Setup warns before creation.
- **Setup Check** checks one PCG actor at a time and only loaded actors; it does not jump to the node or actor and does not check partitioning or runtime generation settings.
- **Seed buttons in the Graph Inspector** change the PCG component seed. Native PCG nodes use it; PCG Pro Tools nodes do not. Blue Noise Scatter and Weighted Selection By Tag use their own node Seed. Clump Scatter, Instance Variation, Random Subset and Spline Offset always use a fixed seed, so the same input always gives the same result and the seed buttons do not vary them. Noise Mask Filter has no seed; use its noise offset.
- **Deterministic Mode** and **Default Random Seed** were shown in Project Settings in earlier versions but never had an effect. They are hidden since PCG Pro Tools 3.0; values saved in your project still load.
- **Performance Guard** warns in the Output Log and on the node; there is no popup and generation is never blocked. A threshold of 0 turns it off. Warnings are produced when the nodes execute: after you change the threshold, a regeneration that reuses cached results shows the new warnings only on the next real execution (for example after a seed change).
- **Tagged actors outside the PCG volume** (Get Actor Data nodes): by default Unreal's PCG updates the result automatically only when a tagged actor *inside the volume bounds* changes. This is the engine setting *Track Actors Only Within Bounds* on the Get Actor Data node. In 3.0 the **Distance Tag template** and the Showcase Valley diagnostic graph have it turned off, so a `POI` actor above, beside or far from a flat volume updates the result when you change its tag, move it or delete it. With the setting off, a change to *any* actor that carries the tag regenerates the volume, even a distant one whose change does not alter the result. In our checks with the small Distance Tag graph a regeneration took about 15–20 ms; large graphs were not measured. These checks covered the Distance Tag template and its test setup only, not every graph that reads actors by tag. Copies of the Distance Tag graph made before 3.0, and your own graphs, keep the setting they have: open the Get Actor Data node and turn off *Track Actors Only Within Bounds*, or keep tagged actors inside the volume bounds, or run `pcg.FlushCache` and regenerate. Other graphs that read actors by tag, including the other Showcase Valley graphs, keep Unreal's default.
- **Regenerate Selected** regenerates the selected components; it does not clear Unreal's PCG cache. For a graph that still tracks tagged actors only inside the bounds (see the previous point) it therefore does not pick up changes made outside the bounds.
- **Graph Inspector node parameter list** is a read-only overview. Change node values in the PCG Graph Editor, through Quick Setup, or with the Reset buttons; the editable fields at the top of the Inspector are the graph's exposed parameters.
- **Showcase Valley** is plugin content: open it, then save the level into your own project before you change it. Its meshes ship without source files, so Reimport asks for a new source file. Its look depends on the project's rendering settings.
- Tested with Unreal Engine 5.8.4. Other 5.8 patch versions were not tested for PCG Pro Tools 3.0.

---

Next: [19 — Upgrading from 2.0 to 3.0](19_Upgrading_From_2.0.md)
