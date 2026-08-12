# 🎯 Quest HUD Tracker

## Overview

The Quest HUD Tracker is the **presentation controller for the active *story* quest HUD**.

It reads quest state from `QuestManager` and dynamically builds the objective list shown to the player. It does not modify quest state or execute quest logic.

```text
QuestManager
     │
     ▼
QuestInstance
     │
     ▼
QuestHUDTracker
     │
     ├── Quest Title
     └── Objective Rows
```

---

# Responsibilities

`ui_quest_tracker.gd` handles:

* Displaying the active story quest.
* Selecting the tracked story quest.
* Displaying the quest title.
* Building objective rows dynamically.
* Showing objective progress.
* Sorting unlocked and gated objectives.
* Displaying objective state through icons and colors.
* Limiting the number of visible objectives.
* Animating title and objective updates.
* Showing and hiding the tracker based on quest availability.

It is strictly a **UI layer** and does not own quest progression.

---

# Active Quest Selection

The tracker prioritizes the quest explicitly tracked by `QuestManager`:

```text
Tracked Quest
     │
     ├── Exists?
     ├── Not completed?
     └── Story category?
          │
          └── Use it
```

If no valid tracked story quest exists, it searches the active quest collection for the first incomplete Story quest.

If none exists, the tracker hides itself.

---

# Quest Refresh Flow

The tracker listens to quest lifecycle signals:

```text
quest_started
quest_updated
quest_completed
quest_selected
quest_canceled
quest_failed
        │
        ▼
     _refresh()
        │
        ▼
_get_active_story_quest()
        │
        ▼
      _update()
```

This keeps the HUD synchronized with quest state without requiring the QuestManager to directly manipulate UI elements.

---

# Objective Rendering

Objectives are staged before being applied to the UI.

```text
Quest Definition
      +
Quest Objective State
      │
      ▼
_build_stage()
      │
      ▼
staged_data
      │
      ▼
_apply_stage()
      │
      ▼
Objective Rows
```

The staging process determines:

* Objective text.
* Current/target progress.
* Whether the objective is unlocked.
* Whether it is ready for turn-in.
* Which objectives should be visible.

Completed objectives are generally removed from the display, except for completed `TURN_IN` objectives.

---

# Objective Row Structure

Rows are generated dynamically:

```text
HBoxContainer
├── TextureRect
│   └── Objective State Icon
│
└── Label
    └── Objective Text
```

Each row is stored in:

```gdscript
objective_rows: Dictionary
```

using the objective index as its identifier.

This allows existing rows to be updated rather than rebuilding every row whenever quest state changes.

---

# Objective States

The tracker visually distinguishes three states:

### Active

Unlocked objectives use:

```text
active_icon
color_active
```

### Gated

Locked objectives use:

```text
gated_icon
color_gated
```

### Ready for Turn-In

Completed turn-in objectives use:

```text
turn_in_icon
color_turn_in
```

State selection is handled entirely by `_update_row()`.

---

# Objective Ordering

After staging, objectives are sorted so unlocked objectives appear before gated objectives.

The UI then physically reorders the existing rows using:

```gdscript
objective_list.move_child(row, index)
```

This keeps the visual order synchronized with the current quest state.

---

# Dynamic Row Lifecycle

Rows are created only when required.

```text
Objective appears
      │
      ▼
_create_row()
      │
      ▼
Store in objective_rows
      │
      ▼
Fade in
```

When an objective is no longer required:

```text
Objective removed
      │
      ▼
Fade out
      │
      ▼
queue_free()
```

This allows the tracker to update incrementally rather than reconstructing the entire UI.

---

# Animation System

The tracker uses Godot Tweens for presentation transitions.

### Quest Title

Changing the active quest fades the existing title out, changes the text, then fades it back in.

### Objective Text

When progress text changes, the label fades out, updates, and fades back in.

### Objective Icons

Icons briefly fade during state changes before the new texture is applied.

### Objective Rows

New rows fade into visibility while removed rows fade out before being freed.

These animations are presentation-only and have no effect on quest state.

---

# Configuration

The tracker exposes configuration for:

### Layout

```text
row_spacing
objective_alignment
max_objectives
```

### Title Animation

```text
title_fade_out_time
title_fade_in_time
```

### Objective Animation

```text
objective_fade_out_time
objective_fade_in_time
update_fade_out_time
update_fade_in_time
```

### Objective Colors

```text
color_active
color_gated
color_turn_in
```

### Objective Icons

```text
active_icon
gated_icon
turn_in_icon
icon_size
```

This keeps presentation tuning separate from quest definitions and quest logic.

---

# Architectural Boundaries

### `ui_quest_tracker.gd` owns

* Quest HUD presentation.
* Active story quest selection for display.
* Objective staging.
* Objective row creation.
* Objective ordering.
* Visual state representation.
* UI animations.

### `QuestManager` owns

* Active quests.
* Tracked quest state.
* Quest lifecycle.
* Quest progression.
* Quest signals.

### `QuestInstance` owns

* Runtime quest state.
* Objective states.
* Objective unlock state.

### `QuestDefinition` owns

* Quest metadata.
* Objective definitions.
* Objective descriptions.
* Objective targets.
* Quest category.

---

# Key Architectural Principle

`ui_quest_tracker.gd` acts as a **read-only presentation layer over the quest runtime**.

```text
Quest Runtime
     │
     │ state + signals
     ▼
Quest HUD Tracker
     │
     │ presentation
     ▼
Player HUD
```

The tracker determines **what quest information should be shown and how it should look**, while the quest system remains responsible for determining **what the quest actually is and what state it is in**.
