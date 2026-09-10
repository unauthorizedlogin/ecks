# 🎬 Credits Root Architecture

### Purpose

`CreditsRoot` is the primary runtime and editor-facing controller for the Credits screen. It owns the Credits UI lifecycle, exposes the complete visual configuration surface, loads the configured credits data source, and delegates credit rendering to `CreditsRenderer`.

### Architecture Role

`CreditsRoot` acts as the **top-level Credits UI controller**.

```text
Credits Data
     ↓
CreditsRoot
     ↓
CreditsRenderer
     ↓
Credits UI Container
```

The root controller handles screen-level state and configuration while keeping credit construction and rendering inside `CreditsRenderer`.

### Responsibilities

* Initialize the Credits screen at runtime.
* Provide editor-time automatic refresh of rendered credits.
* Load the configured credits data resource/script.
* Pass credits data and UI configuration to `CreditsRenderer`.
* Control the delayed visibility of the Return button.
* Define all Credits text, animation, alignment, color, and spacing configuration.
* Expose Credits animation modes through typed enums.
* Maintain the Credits UI container and Return button references.

### Credits Data

The source is configured through:

```text
credits_data_path
```

At render time, `CreditsRoot` loads the configured data provider and retrieves its credits dataset through `get_credits_data()`.

The resulting data is passed to `CreditsRenderer`, which is responsible for constructing the visible Credits content.

### Animation Configuration

`CreditsRoot` defines the available animation categories:

**Entrance**

* NONE
* FADE
* SLIDE
* SCALE
* TYPEWRITER
* BLUR
* DISSOLVE

**Hold**

* NONE
* GLOW_PULSE
* SHIMMER
* WAVE
* FLICKER
* GLITCH

**Exit**

* NONE
* FADE
* SCALE
* BLUR
* DISSOLVE

Animation timing and behavior are configured through exported properties including delay, stagger, trigger offset, duration, scale, hold duration, speed, intensity, and exit duration.

### UI Configuration

The root exposes centralized configuration for:

* Section titles
* Contributor names
* Contributor titles
* Music credits
* Legal text
* Attribution entries
* Special Thanks
* Dedication
* Text colors and outlines
* Horizontal alignment
* Control size flags
* Section and content spacing

This allows the Credits presentation to be configured from the scene without modifying rendering logic.

### Runtime Behavior

At runtime:

1. The Return button starts hidden.
2. Credits data is rendered immediately.
3. A timer tracks the configured `return_button_reveal_delay`.
4. Once the delay expires, the Return button becomes visible.

The Return button timing is independent of the Credits rendering process.

### Editor Behavior

When running in the editor, `CreditsRoot` does not execute runtime button behavior.

Instead, it periodically re-renders the Credits screen using `UPDATE_INTERVAL`, allowing changes to the configured Credits data and presentation settings to propagate without continuously rebuilding the UI every frame.

### Rendering Architecture

`CreditsRoot` does not directly construct individual credit entries.

It provides:

* The root UI node
* Credits container
* Credits data
* Presentation configuration

`CreditsRenderer` owns the actual generation and presentation of the credit elements.

This separation keeps screen lifecycle/configuration concerns isolated from content rendering.

### File(s) Affected

* `credits_root.gd`
* `CreditsRenderer` — rendering dependency
* Configured Credits Data provider defined by `credits_data_path`

### Notes

`CreditsRoot` is the **configuration and lifecycle layer** of the Credits system. `CreditsRenderer` remains responsible for translating the supplied data into the actual Credits UI.
