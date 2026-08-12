# 📜 Quest Menu

## Overview

`QuestMenu` is the primary quest UI controller.

It manages quest visibility, selection, tracking, cancellation, automatic tracking, list population, detail rendering, objective states, rewards, and quest-related UI input.

---

## Architecture

```text
                 QuestManager
                      │
          ┌───────────┼───────────┐
          ▼           ▼           ▼
       Events       State       Queries
          │           │           │
          └───────────┼───────────┘
                      ▼
                  QuestMenu
                      │
        ┌─────────────┼─────────────┐
        ▼             ▼             ▼
   Quest List     Quest Details   Controls
        │             │             │
        ▼             ▼             ▼
     Sorting      Objectives      Track
     Filtering    Rewards         Cancel
     Selection    Progress        Select
```

---

## Responsibilities

`QuestMenu` handles:

* Opening, closing, and toggling the quest menu
* Receiving quest lifecycle events
* Selecting the viewed quest
* Filtering visible quests
* Separating active and completed quests
* Sorting quest lists
* Displaying tracked quest indicators
* Automatically tracking eligible quests
* Tracking and untracking quests
* Canceling quests
* Rendering quest descriptions
* Rendering objective progress and states
* Rendering objective gates
* Rendering turn-in readiness
* Rendering rewards
* Managing quest UI controls
* Maintaining the currently viewed quest

---

## Quest Flow

```text
Quest Started
      │
      ▼
QuestMenu evaluates visibility
      │
      ├── Story ──────► Auto-select
      │
      └── Other ─────► Auto-track if capacity allows
                              │
                              ▼
                         Refresh UI
```

Quest updates, completion, selection, cancellation, and failure events trigger the appropriate UI refresh.

---

## Objective Rendering

Each objective is rendered according to its runtime state:

```text
ObjectiveState
      │
      ├── Locked
      ├── Active      → current / target
      ├── Completed
      └── Ready       → Turn-in available
```

The menu applies configurable icons, colors, alignment, and sizing to each state.

---

## Quest List

The list displays:

* Visible active quests
* Completed quests
* Tracked indicators
* Sorted quest ordering

Hidden quests and achievements are excluded from the standard quest list.

Completed quests can still be reconstructed from their `QuestDefinition` for display after leaving the active quest collection.

---

## Tracking & Cancellation

Tracking is controlled through `QuestManager`, while `QuestMenu` enforces UI-level rules such as:

* Maximum tracked quest count
* Story quest restrictions
* Achievement restrictions
* Track/untrack state
* Cancel button visibility

```text
Player Input
     │
     ▼
 QuestMenu
     │
     ▼
QuestManager
     │
     ▼
Quest State
     │
     ▼
QuestMenu.refresh()
```

---

## Architectural Role

`QuestMenu` is the **quest interaction and presentation controller**.

It does not own quest progression or quest state, but it actively coordinates player interaction with `QuestManager` and translates quest runtime state into the complete quest interface.
