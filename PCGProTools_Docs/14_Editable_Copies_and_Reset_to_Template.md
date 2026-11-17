# 14 — Editable copies and Reset to Template

## Add Copy to Level

In the Template Library, **Add Copy to Level** creates an editable copy of the template in `/Game/PCG`, places a PCG Volume that uses the copy and adds required helpers (for example a road spline for Roadside).

- The original template is not changed.
- **Ctrl+Z** removes the placed volume and helpers. The copied graph asset stays in your project, so nothing you might still need is deleted.
- **Add to Level** (unchanged) places a volume that uses the original template directly.

## Reset to Template

Copies made by Add Copy to Level, Copy or Quick Setup remember which template they came from. In the **Graph Inspector** parameter list:

- **Template** next to a value sets it back to the **shipped template value** (the tooltip shows that value).
- **Reset node to template** sets every listed parameter of that node back in one undoable step.
- **↺ Reset to C++ default** works as before.

The buttons are disabled, with the reason in the tooltip, when:

- the graph has no origin information (for example a copy made by hand or with an older version),
- the node was added to the copy or does not exist in the template,
- the template node is a different node type, or
- the template is not installed.

Shipped template values are today's values of the installed template, not a history of the copy. Mesh lists and the seed are never changed by these buttons.

---

Next: [15 — Multiple road splines](15_Multiple_Road_Splines.md)
