<!-- Copyright (c) 2026 Tom Leon Vincent Hanke -->

# 01 — Installation

PCGProKit is a drop-in plugin for **Unreal Engine 5.7.4**. This page covers every supported install path and how to verify the install.

---

## 1.1 Requirements

| Requirement | Value |
|---|---|
| Engine | **UE 5.7.4** (exact) |
| Platform | Win64 validated (Shipping). Other platforms are code-compatible, untested in 1.0. |
| Required built-in plugin | **Procedural Content Generation Framework** (`PCG`) |
| Optional | A `Landscape` actor in the test level (most templates project onto it) |

> Other 5.x versions are **not** supported in the 1.0 release. PCGProKit uses the PCG 5.7 API surface (metadata `InitializeOnSet` semantics, `UPCGPointArrayData`, `PCGLandscapeData`).

---

## 1.2 Install from Fab (recommended)

1. On the Fab listing page, click **Add to Library**.
2. Open the Epic Games Launcher → **Library → Fab**.
3. Locate *PCGProKit*, click **Install to Engine**, and select **UE 5.7**.
4. Open your project.
5. `Edit → Plugins → Project → Procedural` → enable **PCGProKit**.
6. Also verify **Procedural Content Generation Framework** is enabled (shipped with the engine).
7. Restart the editor when prompted.

---

## 1.3 Install as Project Plugin (from source)

1. Copy the `PCGProKit` folder into `<YourProject>/Plugins/PCGProKit/`.
2. Right-click the `.uproject` → **Generate Visual Studio project files**.
3. Build **Development Editor**, **Win64**.
4. Launch the editor. Project plugins are enabled by default.

Expected folder layout:
```
<YourProject>/
  Plugins/
    PCGProKit/
      PCGProKit.uplugin
      Source/
        PCGProKit/            (Runtime)
        PCGProKitEditor/      (Editor)
      Content/
      Docs/
```

---

## 1.4 Module Layout (for reference)

PCGProKit ships **two modules**. Do not add editor-only code to the runtime module or runtime dependencies on the editor module.

**`PCGProKit` — Runtime**
- Public dependencies: `Core`, `CoreUObject`, `Engine`, `PCG`, `DeveloperSettings`, `Landscape`

**`PCGProKitEditor` — Editor**
- Public dependencies: `Core`, `CoreUObject`, `Engine`, `PCGProKit`, `Slate`, `SlateCore`, `EditorStyle`, `UnrealEd`, `ToolMenus`, `WorkspaceMenuStructure`, `PropertyEditor`, `LevelEditor`, `InputCore`, `Projects`, `AssetRegistry`
- **No `PCGEditor` dependency** — `PCGEditor` exports no public API in 5.7.

---

## 1.5 Verify the Install

1. Open `Plugins/PCGProKit Content/Maps/DemoVillageCorner` (enable **Show Plugin Content** in the Content Browser filter if hidden).
2. Select the existing `PCGVolume` in the level. Confirm:
   - Location `(-12600, -12600, 130)`
   - Scale `(50, 50, 5)`
   - Seed `42`
3. Press **Generate** on the `PCGComponent`.
4. Expected: ~1061 instances appear.

If nothing appears, see **08_Troubleshooting**.

---

## 1.6 Uninstall

- Disable PCGProKit in `Edit → Plugins`, restart the editor, then delete `<YourProject>/Plugins/PCGProKit/`.
- Levels referencing `PCGT_*` templates will show missing-asset warnings until the references are removed or the plugin is reinstalled.

---

## 1.7 Upgrading

- Close the editor before replacing plugin files.
- After upgrade, delete `<YourProject>/Intermediate/` and `<YourProject>/Binaries/`, then rebuild.
- Do **not** edit plugin-content assets in place (templates, presets) — they will be overwritten on upgrade. Duplicate into your project content first.

---

**Next:** [02_Workflow](02_Workflow.md)
