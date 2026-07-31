# 05 — Presets

PCG Pro Tools v2.0.0 includes **13 DataAsset-based presets**.

A preset is a `UPCGProKitPresetAsset` containing property overrides targeted at PCG settings classes.

---

## Preset behavior

Each `FPCGProKitPresetEntry` stores:

- Target node settings class
- Exact property name
- Parameter type
- Value

Supported stored types:

- Float
- Int32
- Bool
- Enum values through Int32 storage

Not captured:

- Seed values
- Vector, FName, Color, and String values unless represented by a supported stored entry type

`TargetGraph` is informational. A preset can be applied to any graph containing compatible settings classes and properties.

Incompatible overrides are skipped without aborting the rest of the preset.

---

## Included presets

| Preset asset | Target graph | Status | Purpose |
|---|---|---|---|
| `Preset_NaturalTrees` | `PCGT_InstanceVariation` | New | Scale 0.8–1.2, yaw variation, light color variation |
| `Preset_WildVegetation` | `PCGT_InstanceVariation` | New | Scale 0.5–1.5, yaw and pitch variation |
| `Preset_RoadBorderWide` | `PCGT_SplineOffset` | New | Wide Both-side spline placement, strong edge falloff |
| `Preset_RoadBorderTight` | `PCGT_SplineOffset` | New | Narrow Both-side placement |
| `Preset_LODNearOnly` | `PCGT_DistanceLOD` | New | Near 3000, far 10000, far density 0 |
| `Preset_LODPerformance` | `PCGT_DistanceLOD` | New | Near 5000, far 50000, far density 0.1 |
| `Preset_RoadsideHighway` | `PCGT_RoadsideGenerator` | New | Sparse left-side roadside placement with 8 m clearance |
| `Preset_RoadsideAvenue` | `PCGT_RoadsideGenerator` | New | Dense Both-side roadside placement |
| `Preset_RoadsideNatural` | `PCGT_RoadsideGenerator` | New | Narrower organic Both-side placement |
| `Preset_BeachSparse` | `PCGT_ForestSetup` | Updated | Sparse coastal scatter |
| `Preset_ForestDense` | `PCGT_ForestSetup` | Updated | Dense forest scatter |
| `Preset_MountainRocky` | `PCGT_HillsideVegetation` | Updated | Rocky hillside configuration |
| `Preset_UrbanGrid` | `PCGT_GridSnap` | Updated | Grid-aligned urban placement |

---

## Applying a preset

1. Select a PCG actor.
2. Open **Graph Inspector**.
3. Select a preset.
4. Click **Apply Preset**.
5. Regenerate manually, or keep Auto-Regenerate enabled.

Apply matches overrides by settings class and property name.

### Multiple nodes of the same class

A class-targeted override can apply to every compatible node using that settings class. Review the graph when several nodes of one type need different values.

### Incompatible entries

Entries with missing classes, missing properties, or incompatible types are skipped silently. The remaining valid entries continue to apply.

---

## Saving a preset

1. Configure the graph.
2. Open Graph Inspector.
3. Click **Save as Preset**.
4. Choose a project content path in the Content Browser dialog.
5. Save the new `UPCGProKitPresetAsset`.

`CaptureFromComponent()` scans supported `PCG_Overridable` values in the selected component graph.

Saved project presets remain outside plugin content and are safe from plugin updates.

---

## Refreshing the list

Click **Refresh Presets** after creating or adding preset assets.

The preset picker discovers shipped plugin presets and compatible project presets.

---

## Editing a preset manually

1. Create a Data Asset using `PCGProKitPresetAsset`.
2. Set:
   - `PresetName`
   - `Description`
   - optional `TargetGraph`
3. Add `Overrides`.
4. For each entry, specify:
   - `NodeClass`
   - exact C++ `PropertyName`
   - `Type`
   - corresponding stored value

Property names must match the C++ `UPROPERTY` name, not the UI label.

---

## Spline Offset presets

Spline Offset uses:

- `SideMode`
- `ScatterWidth` for Both
- `LeftWidth` for Left Only
- `RightWidth` for Right Only
- Center and edge density values

`ScatterWidth` is still the active width for Both mode. It is not replaced by LeftWidth and RightWidth.

---

## Preset limitations

- Seed values are not stored.
- Unsupported property types are not captured.
- Incompatible overrides do not produce a blocking error.
- Preset application is class-based; graphs containing several instances of one settings class require verification.
- Editing shipped preset assets directly is not recommended.

---

Next: [06 — Runtime Usage](06_Runtime_Usage.md)
