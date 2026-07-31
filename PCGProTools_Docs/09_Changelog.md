# 09 — Changelog

## v2.0.0 — Major Update

**Engine:** Unreal Engine 5.8  
**Previous public release:** v1.1.1

PCG Pro Tools v2.0.0 adds five custom nodes, six templates, nine presets, six demo maps, expanded editor tooling, setup validation, and stability improvements.

Existing v1.1.1 graphs remain compatible. No v1.1.1-to-v2.0.0 asset rename or Core Redirect is required.

The existing `UPCGCurvatureFilterSettings` node now uses the public display title **PCG Pro: Curvature Filter**. Its C++ class and template asset remain unchanged, so existing graphs do not require migration.

---

### New custom PCG nodes

#### Instance Variation

Randomizes:

- Uniform or independent-axis scale
- Yaw, pitch, and roll jitter
- Point Color through configurable HSV ranges

Yaw-only variation is configured by setting Pitch Jitter and Roll Jitter to zero.

#### Spline Offset

Moves spline-sampled points laterally with:

- Both, Left Only, and Right Only modes
- `ScatterWidth` for Both mode
- Separate `LeftWidth` and `RightWidth`
- Center-to-edge density falloff
- Deterministic random offsets

#### Distance LOD

Reduces Density based on distance to `FixedLocation`.

The node does not delete points; use downstream density-aware filtering or spawning.

#### Random Subset

Keeps a deterministic percentage of each input dataset.

Includes optional density scaling. No fixed-count mode is included.

#### Biome Mask

Shapes Density radially from the PCG volume center and detects transition boundaries through local-neighbor analysis.

Supports Linear, SmoothStep, and Inverse falloff modes. It has no external biome actor/shape input and does not delete points.

PCG Pro Tools now includes **22 custom nodes**.

---

### New templates

- `PCGT_InstanceVariation`
- `PCGT_SplineOffset`
- `PCGT_DistanceLOD`
- `PCGT_RandomSubset`
- `PCGT_BiomeMask`
- `PCGT_RoadsideGenerator`

PCG Pro Tools now includes **23 graph templates**.

#### Roadside Generator

Prepared On Demand spline workflow with:

- Road helper spline
- `Road` Actor Tag setup
- PCGVolume creation
- Spline Offset
- Spline Avoidance
- Left/right/both-side workflows
- Three included presets

The default spawner uses `/PCG/SampleContent/SimpleForest/Meshes/PCG_Tree_01` from Unreal Engine's built-in PCG plugin as a visualization example. It can be replaced with a project mesh.

---

### New presets

#### Instance Variation

- `Preset_NaturalTrees`
- `Preset_WildVegetation`

#### Spline Offset

- `Preset_RoadBorderWide`
- `Preset_RoadBorderTight`

#### Distance LOD

- `Preset_LODNearOnly`
- `Preset_LODPerformance`

#### Roadside Generator

- `Preset_RoadsideHighway`
- `Preset_RoadsideAvenue`
- `Preset_RoadsideNatural`

### Updated presets

- `Preset_BeachSparse`
- `Preset_ForestDense`
- `Preset_MountainRocky`
- `Preset_UrbanGrid`

Invalid legacy override entries and obsolete property/class references were removed.

PCG Pro Tools now includes **13 presets**: 9 new and 4 updated.

---

### Graph Inspector improvements

- Previous Seed
- Next Seed
- Randomize Seed
- Direct Seed Entry
- Seed Lock
- Point-count display
- Direct editing of supported `PCG_Overridable` node properties
- Per-property Reset to C++ default
- Apply, Save, and Refresh Presets
- Regenerate Selected
- Regenerate All
- Improved regeneration after Undo, Redo, and Reset

Seed controls modify `PCGComponent.Seed`.

Seed Lock is widget state and is not persistent across editor restarts.

---

### Setup Validator

Added a read-only Validate action for supported graph requirements.

Checks include:

- Spline tags and loaded spline actors
- Distance-to-tag Actor Selection Tags
- Landscape presence
- Water Body presence when the Water plugin is active
- `Road` tag requirements for Roadside Generator

Validation shows either a success dialog or a numbered warning list.

---

### Template Library improvements

- Case-insensitive asset-name search
- Category filters
- Combined search and category filtering
- Improved Landscape requirement checks
- Create Editable Copy into `/Game/PCG/`
- Automatic deduplicated names
- Automatic opening in the PCG Graph Editor
- Improved Add to Level behavior

---

### Debug Overlay improvements

Added persistent Node Type Debug Filter settings:

- `bEnableNodeTypeFilter`
- `DebugNodeTypeFilter`

The filter is saved through editor config and applies to PCG components generally.

---

### New demo maps

- `Demo_InstanceVariation`
- `Demo_SplineOffset`
- `Demo_DistanceLOD`
- `Demo_RandomSubset`
- `Demo_BiomeMask`
- `Demo_RoadsideGenerator`

PCG Pro Tools now includes **21 demo maps**.

---

### Updated demo maps and workflows

- Updated `Demo_BiomeTransition`
- Updated `Demo_SplineRoad`
- Updated `Demo_SplineAvoidance`
- Improved supported spline live updates

#### Fixed Demo_DistanceTag from v1.1.1

The shipped v1.1.1 demo graph was incorrectly configured.

v2.0.0:

- Replaces the incorrect secondary Surface Sampler target
- Uses Get Actor Data with Actor Tag `POI`
- Enables Select Multiple
- Adds target tag `TargetPoints`
- Corrects the inverted Attribute Filter
- Restores external POI detection
- Supports live regeneration when relevant POI actors move or change tags

---

### Fixes and stability

- Removed invalid legacy preset override entries
- Removed obsolete preset property/class references
- Improved PCG regeneration after Undo and Redo
- Improved Reset regeneration reliability
- Improved regeneration while Auto-Regenerate is active
- Improved helper spline setup
- Improved live updates for spline workflows
- Corrected Distance Tag demo target-data and filtering setup

---

### Content totals

| Category | v1.1.1 | v2.0.0 |
|---|---:|---:|
| Custom nodes | 17 | 22 |
| Templates | 17 | 23 |
| Presets | 4 | 13 |
| Demo maps | 15 | 21 |

---

### Runtime notes

- v2.0.0 targets Unreal Engine 5.8.
- Landscape Layer Sampler remains editor-only and becomes passthrough in non-editor builds.
- Spline tags are recommended for cooked spline workflows.
- Height Filter and Project To Landscape should use explicit Landscape references in cooked builds.
- Distance LOD and Biome Mask modify Density rather than deleting points.
- No custom mesh or environment art assets are included.

---

## v1.1.x

The previous public v1.1.1 feature inventory contained:

- 17 custom nodes
- 17 templates
- 4 presets
- 15 demo maps

### v1.1.0 feature release

Added:

- Clump Scatter
- Relax Points
- Noise Mask Filter
- Spline Avoidance
- Water Body Avoidance
- Landscape Layer Sampler
- Print Stats
- Debug Overlay
- Template Library
- Graph Inspector
- DataAsset-based preset system
- Expanded templates and demo maps

Historical v1.0-to-v1.1 changes included the rename from `SurfaceSlopeFilter` to the v1.1 public title **PCG Pro: Slope Filter**, plus template/map renames. In v2.0.0 the same `UPCGCurvatureFilterSettings` class is displayed as **PCG Pro: Curvature Filter**. The v2 title change does not require graph migration.

---

## v1.0.0

Initial release.

- 10 custom nodes
- 6 templates
- 4 presets
- 5 demo maps
- Win64 validated
