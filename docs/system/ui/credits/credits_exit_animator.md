# 🚪 Credits Exit Animator Architecture

### Purpose

`CreditsExitAnimator` provides the dedicated exit-animation coordination layer for the Credits system. It processes individual labels or complete label collections and delegates the actual exit animation to `CreditsTextAnimator`.

### Architecture Role

```text id="9k2x4m"
Credits Exit Animator
        ↓
CreditsTextAnimator
        ↓
Exit Animation
```

`CreditsExitAnimator` does not implement animation effects itself. It provides a focused orchestration interface for triggering the exit phase.

### Responsibilities

* Process exit animation for individual Credits labels.
* Process exit animation across a collection of labels.
* Validate Credits root and label instances before processing.
* Delegate exit behavior to `CreditsTextAnimator`.
* Maintain a reference to the associated `CreditsTextAnimator`.

### Animation Flow

For each valid label:

```text id="q7p1ns"
CreditsExitAnimator
        ↓
CreditsTextAnimator.animate_exit()
        ↓
CreditsRoot.exit_animation
        ↓
Fade / Scale / Blur / Dissolve / None
```

The selected exit animation and its configuration remain owned by `CreditsRoot` and implemented by `CreditsTextAnimator`.

### Collection Processing

`animate_all()` provides batch processing for generated Credits labels.

Each valid label is passed individually through `animate()`, allowing the same exit pipeline to be applied consistently across the entire Credits content set.

### Separation of Responsibilities

The exit system is divided into clear layers:

| Component             | Responsibility                    |
| --------------------- | --------------------------------- |
| `CreditsExitAnimator` | Exit orchestration and delegation |
| `CreditsTextAnimator` | Exit animation implementation     |
| `CreditsRoot`         | Exit animation configuration      |
| Credits labels        | Animation targets                 |

This keeps the exit controller lightweight while allowing the text animator to remain the single implementation point for the available exit effects.

### File(s) Affected

* `credits_exit_animator.gd`
* `credits_text_animator.gd`
* `credits_root.gd`

### Notes

`CreditsExitAnimator` acts as a **dedicated exit-phase facade** rather than an animation implementation. The actual visual behavior remains centralized in `CreditsTextAnimator`, preventing duplicate exit-animation logic across the Credits system.
