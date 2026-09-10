# ✨ Credits Text Animator Architecture

### Purpose

`CreditsTextAnimator` is the animation controller for individual Credits labels. It coordinates entrance, hold, and exit animation phases while synchronizing entrance timing with the Credits scroll position.

### Architecture Role

`CreditsTextAnimator` is a reusable, non-node animation service implemented as a `RefCounted` class.

```text
CreditsRoot
    ↓
CreditsRenderer
    ↓
CreditsTextAnimator
    ├── Entrance Animation
    ├── CreditsHoldAnimator
    └── Exit Animation
```

It reads presentation settings from `CreditsRoot` while operating directly on the generated `Label` controls.

### Responsibilities

* Animate individual Credits labels.
* Animate collections of labels.
* Calculate entrance timing from the label's position within the scrolling content.
* Apply configured entrance delays and stagger timing.
* Coordinate entrance → hold → exit animation flow.
* Delegate hold-phase effects to `CreditsHoldAnimator`.
* Support multiple entrance and exit animation types.
* Apply shader-based blur and dissolve effects.
* Reset animation sequencing between render cycles.

### Entrance Timing

Entrance animations are synchronized with the Credits scroll position rather than beginning immediately when labels are created.

The animator calculates:

```text
Label Content Position
        ↓
Viewport Height
        ↓
Scroll Speed
        ↓
Trigger Offset
        ↓
Scroll Delay
        ↓
Entrance Delay
        ↓
Entrance Stagger
        ↓
Animation Start
```

Each animated label receives an incremental stagger based on `_entrance_index`.

This allows labels to enter as they approach the visible Credits area during automatic scrolling.

### Entrance Animations

Supported entrance modes are provided by `CreditsRoot.EntranceAnimation`:

* `NONE`
* `FADE`
* `SLIDE`
* `SCALE`
* `TYPEWRITER`
* `BLUR`
* `DISSOLVE`

The implementation supports both label-only animation and, where applicable, an optional `animation_target` for transforming a parent or surrounding control.

### Hold Phase

After a successful entrance animation, the label is passed to `CreditsHoldAnimator`.

```text
Entrance Complete
       ↓
CreditsHoldAnimator
       ↓
Hold Effect
       ↓
animate_exit()
```

The hold animator is lazily recreated if necessary and is reset whenever the text animator is reset.

### Exit Animations

Supported exit modes are:

* `NONE`
* `FADE`
* `SCALE`
* `BLUR`
* `DISSOLVE`

Exit animation is triggered by the hold animator after the configured hold behavior completes.

### Shader Effects

Two entrance/exit effects use dedicated shaders:

* `credits_blur.gdshader`
* `credits_dissolve.gdshader`

Shader materials are created per animated label, configured with the appropriate starting and target values, then removed from the label after the animation completes.

This keeps shader state local to the animated label rather than requiring a permanent material assignment.

### Animation State

`_entrance_index` tracks the current label position in the entrance sequence.

`reset()` clears this sequence and resets the associated hold animator, allowing a newly rendered Credits list to begin its animation sequence from the beginning.

### File(s) Affected

* `credits_text_animator.gd`
* `credits_hold_animator.gd` — hold-phase dependency
* `credits_blur.gdshader` — blur animation
* `credits_dissolve.gdshader` — dissolve animation
* `credits_root.gd` — animation configuration source
* `credits_renderer.gd` — animation orchestration dependency

### Notes

`CreditsTextAnimator` owns **per-label animation orchestration**, while `CreditsHoldAnimator` owns hold effects. `CreditsRenderer` remains responsible for generating the Credits UI, keeping content generation separate from animation behavior.
