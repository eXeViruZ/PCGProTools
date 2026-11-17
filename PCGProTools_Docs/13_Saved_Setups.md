# 13 — Saved setups (recipes)

Save a Quick Setup configuration and use it again in another level or project folder.

## Save

1. Configure the Quick Setup dialog (meshes, weights, per-mesh settings, parameters, seed, volume size).
2. Under **Saved setup (recipe)**, enter the save path (inside `/Game`).
3. Click **Save Setup**.

An existing asset is never overwritten. Plugin and engine folders cannot be used as targets.

## Load

- In the Quick Setup dialog: pick the recipe and click **Load Setup**, or
- in the Template Library: select the recipe in the Content Browser and click **Quick Setup from Recipe…**.

Loading **only fills the dialog**. Review the values, then click **Create & Generate**.

## Safety checks

- A recipe for a different template, a newer recipe format, or an unknown template is refused, and the dialog stays unchanged.
- Missing meshes stay in the list and block creation until you replace them; renamed meshes are followed through redirectors.
- Values outside the allowed range are clamped and listed; unknown or broken values are skipped and listed.

Recipes are editor data: they are not needed in a packaged game.

---

Next: [14 — Editable copies and Reset to Template](14_Editable_Copies_and_Reset_to_Template.md)
