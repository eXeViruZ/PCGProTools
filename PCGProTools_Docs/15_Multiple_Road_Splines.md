# 15 — Multiple road splines

**Roadside Generator** and **Spline Road** setups can follow more than one road. Choose **Road splines** in the Quick Setup dialog:

| Option | Which splines are used |
|---|---|
| One spline tagged 'Road' (template behaviour) | One spline actor with the tag `Road` |
| Road splines overlapping this volume | Every `Road` spline whose bounds overlap the PCG Volume (default for new setups) |
| All splines tagged 'Road' | Every `Road` spline in the level |
| Splines with a custom tag | Every spline with the tag you enter (for example `Trail`) |

- Spline Avoidance uses the same tag, so points keep the road clearance from every selected road.
- Quick Setup never changes the tags of your actors. Add the tag yourself under **Details → Actor → Tags**.
- A new volume gets a helper spline tagged `Road` (50 m along +X); edit its points to follow your road.
- **Both Sides** stays symmetric. **Left Only** uses the left width, **Right Only** the right width.
- To change the option later, open **Quick Setup…** from the Graph Inspector; the dialog shows the current choice.

---

Next: [16 — Exclusion zones](16_Exclusion_Zones.md)
