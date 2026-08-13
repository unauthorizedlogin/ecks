# 🎯 Side Quest Tracker

## Overview

The Side Quest Tracker is the **HUD presentation controller for tracked *side* quests**.

It displays multiple active non-Story quests and keeps their titles, objectives, progress, icons, colors, and visibility synchronized with `QuestManager`.

The tracker handles **presentation only**. Quest state and progression remain owned by the quest system.

---

## Architecture Role

```text
QuestManager
     │
     ▼
Active / Tracked QuestInstances
     │
     ▼
ui_side_quest_tracker.gd
     │
     ├── Quest Panel 1
     ├── Quest Panel 2
     ├── Quest Panel 3
     └── ...
          │
          ├── Quest Title
          └── Objective Rows
```

The tracker sits between quest runtime data and the HUD.

It does not modify quest state.

---

# Responsibilities

`ui_side_quest_tracker.gd` is responsible for:

* Displaying multiple tracked side quests.
* Excluding Story quests.
* Ignoring completed quests.
* Updating quest titles.
* Rendering objective progress.
* Limiting objectives per quest.
* Sorting objectives by their existing quest order.
* Displaying locked, active, and turn-in-ready states.
* Managing objective icons and colors.
* Creating and removing objective rows dynamically.
* Showing and hiding quest panels.
* Handling title and objective animations.
* Reacting to quest lifecycle signals.

---

# Quest Selection

The tracker obtains its quests from:

```gdscript
QuestManager.tracked_quests
```

It resolves each ID against:

```gdscript
QuestManager.active_quests
```

Only quests that meet these conditions are displayed:

```text
Tracked
  │
  ├── Active
  ├── Not completed
  └── Not STORY category
```

This makes the tracker specifically responsible for **tracked side-quest presentation** rather than general quest management.

---

# Panel Architecture

The tracker supports multiple pre-authored quest panels.

At initialization it scans its children for `ColorRect` panels and creates a `PanelState` for each:

```text
SideQuestTracker
│
├── Quest Panel
│   ├── QuestTitle
│   └── ObjectiveList
│
├── Quest Panel
│   ├── QuestTitle
│   └── ObjectiveList
│
└── Quest Panel
```

Each panel maintains independent runtime state:

```text
PanelState
├── panel
├── current_title
├── title_tween
└── objective_rows
```

This allows each tracked quest to update independently.

---

# Quest Panel Assignment

The current implementation assigns the returned side quests to available panels by index:

```text
Side Quest 0 → Panel 0
Side Quest 1 → Panel 1
Side Quest 2 → Panel 2
...
```

When fewer quests are tracked than available panels, unused panels are faded out and hidden.

When a new quest occupies a previously hidden panel, that panel fades back in.

---

# Objective Rendering

Each quest has a maximum number of displayed objectives:

```gdscript
max_objectives_per_quest
```

Objectives are processed from the quest definition and skipped when:

* Already completed, unless they are a Turn-In objective.
* The maximum display count has been reached.

Each displayed objective becomes:

```text
HBoxContainer
├── TextureRect
└── Label
```

The label displays:

```text
Objective Description (current/target)
```

Completed objectives can use the definition's alternate:

```gdscript
completed_description
```

---

# Objective States

The tracker presents three visual objective states.

### Active

Unlocked objective:

```text
active_icon
color_active
```

### Gated

Locked objective:

```text
gated_icon
color_gated
```

### Ready to Turn In

Completed Turn-In objective:

```text
turn_in_icon
color_turn_in
```

The quest system determines the state; the tracker determines how that state looks.

---

# Dynamic Row Management

Objective rows are stored per panel:

```gdscript
objective_rows: Dictionary
```

The tracker reuses existing rows when possible.

```text
Objective exists
    │
    ├── Existing row → update it
    │
    └── Missing row → create it
```

Rows no longer required are faded out and freed.

This avoids rebuilding the entire objective list every time quest progress changes.

---

# Quest Title Updates

Quest titles are localized through:

```gdscript
tr(quest.definition.display_name)
```

When a title changes, the tracker performs a fade transition:

```text
Current Title
     │
     ▼
Fade Out
     │
     ▼
Replace Text
     │
     ▼
Fade In
```

Each panel maintains its own title tween through `PanelState`.

---

# Objective Updates

Objective text changes are also animated.

```text
Old Progress
     │
     ▼
Fade Out
     │
     ▼
Update Text
     │
     ▼
Fade In
```

Icon changes use a shorter transition so state changes remain visually noticeable without delaying the HUD.

---

# Quest Lifecycle Integration

The tracker listens to:

```gdscript
QuestManager.quest_started
QuestManager.quest_updated
QuestManager.quest_completed
QuestManager.quest_selected
QuestManager.quest_canceled
QuestManager.quest_failed
```

These signals trigger a refresh of the HUD.

The tracker therefore reacts to quest state changes without owning the underlying quest logic.

---

# Quest Selection Handling

When a quest is selected, the tracker clears the corresponding panel's current objective rows and title before refreshing.

This gives the selected quest a clean presentation update rather than leaving stale objective content visible during the transition.

---

# Configuration

### Layout

```gdscript
row_spacing
objective_alignment
max_objectives_per_quest
```

Controls objective row structure and display limits.

### Title Animation

```gdscript
title_fade_in_time
title_fade_out_time
```

Controls quest title transitions.

### Tracking Animation

```gdscript
track_fade_in_time
track_fade_out_time
```

Controls entire quest panel visibility.

### Objective Animation

```gdscript
objective_fade_in_time
objective_fade_out_time
update_fade_in_time
update_fade_out_time
```

Controls objective creation, removal, and progress updates.

### Objective Colors

```gdscript
color_active
color_gated
color_turn_in
```

Defines visual states.

### Objective Icons

```gdscript
active_icon
gated_icon
turn_in_icon
icon_size
```

Defines objective state indicators.

---

# Architectural Boundaries

### `ui_side_quest_tracker.gd` owns

* Side-quest HUD presentation
* Panel assignment
* Objective row creation
* Objective state visuals
* Quest title presentation
* Animations
* HUD visibility

### `QuestManager` owns

* Quest tracking
* Active quests
* Quest lifecycle
* Quest progression
* Quest state

### `QuestInstance` owns

* Runtime quest state
* Objective state
* Objective progress
* Unlock state

### `QuestDefinition` owns

* Quest definition data
* Display name
* Objective definitions
* Objective descriptions
* Quest category

---

# Key Architectural Principle

`ui_side_quest_tracker.gd` is a **multi-quest HUD renderer**.

Its job is essentially:

```text
QuestManager State
       │
       ▼
Select tracked non-Story quests
       │
       ▼
Assign quests to HUD panels
       │
       ▼
Render titles + objectives
       │
       ▼
Apply state visuals + animations
```

The quest system decides **what is happening**.

The Side Quest Tracker decides **how those active side quests appear on the HUD**.
