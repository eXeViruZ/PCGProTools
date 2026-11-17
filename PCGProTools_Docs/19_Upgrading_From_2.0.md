# 19 — Upgrading from 2.0 to 3.0

PCG Pro Tools 3.0 is a major workflow update, **not a breaking update**. Your 2.0 graphs need no migration and no Core Redirects: node classes, property names, templates and presets keep their names.

## What you may notice after updating

- **Validate** now opens the **Setup Check** section in the Graph Inspector instead of the Setup Validator dialog. The checks stay visible as Error, Warning or Info; use **Re-check** after a fix.
- **Deterministic Mode** and **Default Random Seed** are no longer shown in Project Settings. They never had an effect; values saved in your project still load.
- **Undo/Redo** no longer clears Unreal's PCG cache and regenerates everything. Unreal's own PCG refresh updates the affected components. A component with **Regenerate In Editor** turned off is not regenerated automatically after Undo/Redo — select it and use **Regenerate Selected**.
- **Apply Preset** now lists which entries were applied, unchanged or skipped, and marks assets as modified only when a value really changes. The 13 included presets behave as before. A custom preset with several entries for the same node class and property is applied in graph order when the number of entries matches the number of matching nodes; otherwise those entries are skipped and the reason is listed.
- **Shipped plugin graphs are protected:** Reset and Apply Preset on a plugin or engine graph first offer an editable project copy.
- **Reset to Template** works on copies made with this version. Copies made with 2.0 or by hand do not carry the origin information, so their reset buttons are disabled.
- **Distance Tag copies keep their old setting.** The 3.0 Distance Tag template reads `POI` actors with *Track Actors Only Within Bounds* turned off, so it also updates when a `POI` actor outside the volume changes. A Distance Tag graph you copied from 2.0 keeps the setting it had and is not rewritten. To adopt the change, open your copy in the PCG Graph Editor, select the Get Actor Data node that reads the `POI` tag and turn off *Track Actors Only Within Bounds* in its Details. Nothing else changes. With the setting off, a change to any actor with that tag, even a distant one, regenerates the volume; the result stays the same.

## Saved setups (recipes)

Saved setups are new in 3.0. A recipe asset cannot be opened by PCG Pro Tools 2.0. Your existing 2.0 graphs do not use recipes, so there is nothing to convert.

## For C++ users

Only relevant if your own code calls PCG Pro Tools:

- `UPCGProKitPresetAsset::ApplyToComponent(UPCGComponent*)` keeps its signature. It now returns `false` when nothing changes (every target already has the preset value, or no entry matches). Treat `false` as "nothing to change", not as an error.
- New `PreviewApply(const UPCGGraph*)` returns an `FPCGProKitPresetApplyReport` without changing anything; new `ApplyToGraph(UPCGGraph*, FPCGProKitPresetApplyReport&)` applies the preset and fills the report. Both are C++ only.
- `UPCGProKitSettings::bDeterministicMode` and `DefaultRandomSeed` keep their names, types, defaults and config keys.

## Requirements

Unreal Engine 5.8 with the PCG plugin. PCG Pro Tools 3.0 was tested with Unreal Engine 5.8.4.
