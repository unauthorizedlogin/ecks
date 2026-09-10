# 🖼️ Credits Background Architecture

### Purpose

The Credits Background component manages the visual background displayed behind the Credits screen. It supports multiple configured textures, a selectable starting background, optional automatic cycling, and crossfade transitions between backgrounds.

### Architecture Role

This component is a **presentation-layer background controller** attached to a `NinePatchRect`.

```text
CreditsRoot / Credits UI
        ↓
Credits Background
        ↓
Background Texture
```

It operates independently from Credits content rendering and controls only the background presentation.

### Responsibilities

* Maintain the configured background texture collection.
* Select the initial background using `background_start_index`.
* Validate and clamp the selected background index.
* Support optional automatic background cycling.
* Track the configured cycle interval.
* Transition between backgrounds using a fade-out/fade-in tween.
* Refresh the displayed texture when background configuration changes in the editor.
* Provide an exported directory path for Credits screenshot assets.

### Background Configuration

The component exposes:

* `background_textures` — available background textures.
* `background_start_index` — initial texture selection.
* `background_cycle_enabled` — enables automatic cycling.
* `background_cycle_interval` — time between background changes.
* `screenshot_directory` — configured source directory for Credits screenshot assets.

Changes to the texture collection or starting index immediately trigger a background refresh.

### Runtime Behavior

When initialized, the component selects the configured starting texture.

If cycling is enabled and multiple textures are available, `_process()` tracks elapsed time until `background_cycle_interval` is reached.

The next texture is selected cyclically:

```text
Background A
    ↓
Background B
    ↓
Background C
    ↓
Background A
```

A single configured texture disables cycling automatically.

### Transition System

Background changes use a `Tween` to create a crossfade:

```text
Current Background
       ↓
Fade Out
       ↓
Swap Texture
       ↓
Fade In
       ↓
Next Background
```

Any active background transition is terminated before a new transition begins, preventing overlapping background tweens.

### Editor Integration

The script is marked `@tool`, allowing background configuration changes to be reflected directly in the editor.

Texture and starting-index property setters call `_refresh_background()`, keeping the displayed background synchronized with the exported configuration.

### File(s) Affected

* Credits background script (`NinePatchRect` background controller)
* Credits background texture assets
* Credits screenshot asset directory

### Notes

The component is intentionally isolated from Credits content and animation logic. Its responsibility is limited to **background asset selection, cycling, and transition presentation**.
