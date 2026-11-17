# 16 — Exclusion zones

Keep scatter away from buildings, rocks, props or volumes by tagging them.

## Set up

1. In the Quick Setup dialog for a **new** graph, tick **Exclusion zones (actors with a tag)**.
2. Keep the tag `PCGExclude` or enter your own.
3. Set the **Clearance factor (x bounds)** — 1.2 makes the excluded area 20 % larger than the actor's bounds.
4. Create the setup, then add the tag to the actors that should stay clear (**Details → Actor → Tags**).

If no actor has the tag yet, the dialog and the Generation Report show this as information, not as an error — nothing is excluded until you tag something.

## Change later

Open **Quick Setup…** from the Graph Inspector to change the tag or the clearance, or untick the option to turn exclusion off (one undoable step; the graph keeps its structure).

## How it works and limits

The graph uses native nodes: Get Actor Data → Bounds Modifier → Difference. Exclusion uses each tagged actor's **bounding box** scaled by the clearance factor, not its exact shape; a round or L-shaped object excludes a box around it.

Exclusion is added only when a new editable graph is created. Templates without a suitable insertion point show the option disabled with the reason.

---

Next: [17 — Showcase Valley](17_Showcase_Valley.md)
