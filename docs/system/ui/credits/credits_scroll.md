# 📜 Credits Scroll

## Overview

ScrollControl controls the **automatic vertical scrolling of the Credits screen**.

It waits for the Credits UI to finish building and its layout to settle, then smoothly moves the `ScrollContainer` from the top to the bottom at a configurable speed.

---

## Architecture Role

```text
Credits Root
     │
     ▼
ScrollContainer
     │
     ▼
Credits Content
     │
     ▼
Credits Scroll Controller
     │
     ▼
Automatic Vertical Scrolling
```

The controller does not generate or modify Credits content. It only controls the scroll position of the existing Credits layout.

---

## Responsibilities

* Control the Credits `ScrollContainer`.
* Delay scrolling until the Credits UI has been populated.
* Allow additional frames for Godot's layout system to calculate content size.
* Reset the scroll position to the top.
* Determine the maximum available scroll distance.
* Advance scrolling at a configurable speed.
* Smoothly interpolate the scrollbar movement.
* Stop automatically when the bottom of the Credits is reached.
* Provide immediate editor scrolling while allowing a runtime startup delay.

---

## Startup Flow

At runtime, scrolling waits for the configured delay:

```gdscript
@export var auto_scroll_delay := 1.5
```

```text
Scene Loaded
     │
     ▼
Wait 1.5 seconds
     │
     ▼
Enable Scrolling
     │
     ▼
Wait for layout frames
     │
     ▼
Reset scroll to 0
     │
     ▼
Begin automatic scrolling
```

In the editor, scrolling starts immediately rather than waiting for the runtime delay.

---

## Layout Settling

Because `credits_root.gd` dynamically creates the Credits content, the final `ScrollContainer` height is not immediately available.

`_ensure_scroll_check()` waits several frames before reading the scrollbar:

```gdscript
@export var settle_frames := 2
```

Additional frames are then allowed for the layout to finish calculating.

After settling:

```gdscript
scroll.scroll_vertical = 0
scroll_target = 0
scroll_position = 0
```

This ensures scrolling always begins from the top using the correct calculated content height.

---

## Scroll Calculation

The controller reads the maximum vertical scroll value from the container:

```gdscript
var max_scroll: float = vbar.max_value
```

The target position advances according to:

```gdscript
scroll_position += scroll_speed * delta
```

and is clamped to the bottom:

```gdscript
min(scroll_position + scroll_speed * delta, max_scroll)
```

The exported speed controls how quickly the Credits travel:

```gdscript
@export var scroll_speed := 60
```

---

## Smooth Movement

Rather than directly assigning the target position, the controller interpolates the actual scrollbar toward it:

```gdscript
var smooth_scroll = lerp(
    current_scroll,
    scroll_position,
    10.0 * delta
)
```

The resulting value is rounded before being applied to the `ScrollContainer`.

This produces continuous, smooth movement instead of visibly stepping between positions.

---

## Scroll Completion

Scrolling only occurs while:

```text
max_scroll > 0
```

and:

```text
scroll_position < max_scroll
```

Once the target reaches the maximum scroll position, the controller stops advancing.

```text
Top
 │
 │  scroll_speed
 ▼
Credits Content
 │
 │
 ▼
Bottom
 └── Stop
```

---

## Configuration

### Scroll Speed

```gdscript
@export var scroll_speed := 60
```

Controls the automatic movement rate in pixels per second.

### Settle Frames

```gdscript
@export var settle_frames := 2
```

Controls how many frames the controller waits for dynamically generated Credits content to finish calculating its layout.

### Auto Scroll Delay

```gdscript
@export var auto_scroll_delay := 1.5
```

Controls how long the runtime Credits remain at the top before automatic scrolling begins.

### Name Spacing

```gdscript
@export var name_spacing := 32
```

This property is currently exposed by the controller but is not used by the scrolling logic.

---

### Key Design Principle

**The Scroll Controller is independent of Credits content generation.**

It waits for the dynamically generated Credits layout to settle, determines the resulting scroll range, and then provides smooth automatic movement from the top of the Credits to the bottom.
