# 🎨 Credits Hold Animator Architecture

### Purpose

`CreditsHoldAnimator` manages the **hold phase** of Credits text animation. It applies looping visual effects to individual labels for the configured hold period, then signals `CreditsTextAnimator` to begin the exit phase.

### Architecture Role

`CreditsHoldAnimator` is a reusable `RefCounted` animation service.

```text
CreditsTextAnimator
        ↓
CreditsHoldAnimator
        ├── Hold Effect
        ├── Hold Timer
        └── Completion Callback
                ↓
        CreditsTextAnimator
                ↓
          Exit Animation
```

It owns hold-specific animation behavior and does not manage entrance or exit animations.

### Responsibilities

* Apply the configured hold animation to a Credits label.
* Manage looping hold-effect tweens.
* Track active tweens for cleanup.
* Enforce the configured hold duration.
* Trigger the supplied completion callback when the hold phase ends.
* Reset and terminate active animation state.
* Provide multiple shader-based and property-based hold effects.

### Hold Animations

Supported through `CreditsRoot.HoldAnimation`:

* `NONE`
* `GLOW_PULSE`
* `SHIMMER`
* `WAVE`
* `FLICKER`
* `GLITCH`

Each effect is implemented independently, allowing hold behavior to be extended without changing the surrounding Credits animation pipeline.

### Shader Effects

The following effects use dedicated shaders:

* `credits_glow_pulse.gdshader`
* `credits_shimmer.gdshader`
* `credits_wave.gdshader`
* `credits_glitch.gdshader`

Shader materials are created for the active label and driven through looping tweens.

`FLICKER` operates directly on the label's alpha without requiring a shader.

### Hold Lifecycle

When `animate()` is called:

1. The configured hold effect is started.
2. A hold-duration tween is created.
3. The tween waits for `root.hold_duration`.
4. The supplied completion callback executes.
5. `CreditsTextAnimator` begins the label's exit animation.

If `hold_duration` is zero or negative, the completion callback is invoked immediately after the hold effect is initialized.

```text
Entrance Complete
       ↓
Start Hold Effect
       ↓
Hold Duration
       ↓
Completion Callback
       ↓
Exit Animation
```

### Tween Management

All created hold tweens are tracked in `_active_tweens`.

`reset()`:

* Kills all active tweens.
* Clears the tween registry.

`stop(label)`:

* Validates the target label.
* Terminates active tracked tweens.
* Clears the active tween registry.

This provides explicit lifecycle control when Credits content is rebuilt or animation state needs to be discarded.

### File(s) Affected

* `credits_hold_animator.gd`
* `credits_text_animator.gd` — lifecycle coordinator
* `credits_root.gd` — hold configuration source
* `credits_glow_pulse.gdshader`
* `credits_shimmer.gdshader`
* `credits_wave.gdshader`
* `credits_glitch.gdshader`

### Notes

`CreditsHoldAnimator` is intentionally isolated from entrance and exit logic. Its architectural responsibility is the **middle phase of the Credits animation pipeline: apply a hold effect, maintain it for the configured duration, then hand control back to the text animator**.
