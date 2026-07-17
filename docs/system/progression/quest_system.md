# 📜 Quest System

## Overview

The Quest System provides the framework layer for creating, tracking, progressing, and completing quests.

It manages:

- Quest definitions
- Active quest instances
- Quest progression
- Objective updates
- Quest categories
- Quest tracking
- Quest chains
- Quest completion
- Quest rewards
- Quest persistence

The system separates static quest data from runtime quest progress.

---

# 📘 Quest Definitions

Quest definitions contain the static data for available quests.

The Quest Manager loads and registers quest definitions during initialization.

Definitions provide:

- Quest identity
- Quest category
- Objectives
- Rewards
- Quest line information
- Dialogue references
- Tracking rules

---

# 🎯 Quest Lifecycle

Quests follow a controlled lifecycle:

```

Not Started
|
↓
In Progress
|
↓
Ready for Completion
|
↓
Completed

```

Additional states:

- Failed
- Canceled

---

# 🚀 Quest Starting

When a quest starts:

- A runtime QuestInstance is created
- Objectives are initialized
- The quest is added to active quests
- Quest start events are broadcast
- Auto-tracking rules are applied

Quest start events can trigger:

- Objective progression
- UI updates
- Event notifications

---

# 📌 Quest Tracking

The system supports quest tracking management.

Tracked quests are separated into:

## Story Tracking

Story quests use a dedicated tracked quest slot.

Only one story quest can be actively tracked.

## Additional Tracking

Non-story quests can be tracked independently.

Tracked quests are used by:

- Quest HUD
- Quest menu
- Objective displays

---

# 🎯 Objective Progression

The Quest Manager receives gameplay events and routes them to active quests.

Supported event-driven progression includes:

- Item collection
- Enemy kills
- Quest start
- Quest completion
- Dialogue interactions
- Equipment actions
- Custom objective events

Objectives update through centralized event notifications.

---

# 🔒 Objective Gating

Objectives can be locked behind previous objectives.

Gated objectives remain unavailable until their required objectives are completed.

Example:

```

Objective 1
|
↓
Objective 2 unlocked
|
↓
Objective 3 unlocked

```

---

# 🔗 Quest Chains

Quest lines allow sequential quest progression.

When a quest completes:

- The current quest is finalized
- Rewards are granted
- The next quest in the chain can automatically begin

Quest chains are ordered through quest indexes.

---

# 🏷️ Quest Categories

Quests use category-based behavior rules.

Categories determine:

- Tracking behavior
- Cancellation rules
- Reset behavior
- Quest management rules

Examples include:

- Story
- Side
- Daily
- Faction
- Chained

---

# 🔄 Quest Reset System

Resettable quests can have their progress cleared.

Supported behavior:

- Reset active progress
- Remove completion status
- Allow replay
- Reset daily quests

Reset behavior is controlled by quest category rules.

---

# ❌ Quest Cancellation

Quests can be abandoned when allowed.

Cancellation:

- Removes the active quest instance
- Records the failed/canceled state
- Clears tracking references
- Broadcasts quest cancellation events

---

# 🏆 Quest Completion

Quest completion handles:

- Finalizing objectives
- Marking the quest complete
- Granting rewards
- Updating related quests
- Advancing quest chains
- Broadcasting completion events

Completing one quest can progress objectives requiring another quest to finish.

---

# 📡 Quest Signals

## `quest_started`

Triggered when a quest begins.

## `quest_updated`

Triggered whenever quest progress changes.

## `quest_selected`

Triggered when tracking changes.

## `quest_completed`

Triggered after successful completion.

## `quest_failed`

Triggered when a quest fails.

## `quest_canceled`

Triggered when a quest is abandoned.

---

# 💬 Dialogue Integration

The Quest System supports dialogue-driven quest states.

It tracks:

- Quest offers
- Available quests
- Active quest dialogue
- Completion dialogue states

Dialogue systems can query quest state to determine available interactions.

---

# 💾 Serialization

Quest progress supports save/load functionality.

Saved data includes:

- Active quests
- Quest objective progress
- Completed quests
- Failed quests

Quest definitions are restored from the registered quest database during loading.

---

# 🔗 System Relationship

```

QuestDefinition
|
|
QuestManager
|
|
QuestInstance
|
|
Objectives
|
|
Rewards / Dialogue / UI

```

The Quest System acts as the central authority for quest state, progression, and lifecycle management.
```

# 📜 Quest Menu System

## Overview

The Quest Menu is the primary UI interface for viewing and managing player quests.

It acts as a **visualization layer only**, displaying quest data owned by `QuestManager`.

The Quest Menu does not control quest progression, objective logic, validation, rewards, or quest state changes.

---

# 🧩 Architecture Role

## UI Authority

The Quest Menu is responsible for:

* Displaying active quests
* Displaying completed quests
* Showing objective progress
* Showing objective states
* Showing quest rewards
* Allowing quest tracking changes
* Allowing quest cancellation

The Quest Menu does not:

* Start quests
* Complete quests
* Update objectives
* Grant rewards
* Validate requirements

---

# 🔄 Data Flow

```
QuestManager
      |
      |
      v
 QuestMenu
      |
      |
      v
 UI Rendering
```

Quest state is owned by `QuestManager`.

The menu reads that state and updates the interface.

---

# 🎯 Quest Display

The menu displays quests separated into two groups:

## Active Quests

Active quests display:

* Quest title
* Tracking state
* Objective progress
* Available actions

Tracked quests are visually marked:

```
➤ Quest Name
```

---

## Completed Quests

Completed quests are displayed separately.

Completed quests:

* Receive a completion check mark
* Use completed quest coloring
* Remain viewable for reference

Example:

```
✔ Completed Quest
```

---

# 📋 Quest Details Panel

Selecting a quest displays:

## Quest Information

* Quest title
* Quest description

## Objectives

Each objective displays:

* Objective description
* Current progress
* Target amount
* Completion state
* Locked state

## Rewards

Rewards display:

* Reward name
* Reward quantity

---

# 🎯 Objective States

The Quest Menu supports multiple objective visual states.

## Active Objective

Displayed when:

* Objective is unlocked
* Objective is incomplete

Shows:

* Active icon
* Progress counter

Example:

```
Collect Herbs 3/10
```

---

## Gated Objective

Displayed when:

* Objective requirements are not met
* Previous objectives must be completed first

Shows:

* Locked icon
* Gated color

---

## Ready For Turn In

Displayed when:

* Objective type is `TURN_IN`
* Objective requirements are complete
* Quest has not yet completed

Shows:

* Turn-in icon
* Highlighted turn-in color

---

## Completed Objective

Displayed when:

* Objective has been completed

Shows:

* Completed icon
* Completed objective color

Supports alternate completion descriptions through:

```
completed_description
```

---

# 🎨 Visual Configuration

The menu exposes inspector controls for UI customization.

## Objective Colors

Configurable colors:

* Active objective
* Locked objective
* Ready for turn-in
* Completed objective

## Objective Icons

Custom icons:

* Active
* Locked
* Ready for turn-in
* Completed

## Layout

Configurable:

* Objective text alignment
* Objective icon size

---

# 🎮 Quest Tracking

The Quest Menu manages player tracking selection.

## Story Quests

Story quests:

* Cannot be manually untracked
* Automatically become the tracked story quest

---

## Side Quests

Side quests support:

* Track
* Untrack

The menu enforces:

```
max_tracked_quests
```

to limit simultaneously tracked side quests.

---

# ❌ Quest Cancellation

The Cancel button allows abandoning quests.

Cancellation is controlled by:

```
QuestManager.can_show_cancel_button()
```

The menu only requests cancellation.

Quest rules remain controlled by `QuestManager`.

---

# 📡 Signals

The Quest Menu listens to QuestManager events.

## quest_started

Updates the quest display when new quests begin.

## quest_updated

Refreshes quest progress and objective states.

## quest_completed

Updates completed quest presentation.

## quest_selected

Refreshes the currently viewed quest.

## quest_canceled

Removes canceled quests from the interface.

## quest_failed

Removes failed quests from the interface.

---

# 🔄 Refresh Pipeline

The menu uses a two-stage refresh system.

```
QuestManager Signal
        |
        v
QuestMenu Refresh
        |
        +--> Populate Quest List
        |
        +--> Update Quest Details
```

---

# 📦 Quest List Population

The quest list rebuilds from current `QuestManager` state.

Process:

1. Clear existing list
2. Separate active and completed quests
3. Add active quests
4. Add completed quests
5. Restore selected quest

---

# 🖥 UI Controls

The menu supports:

## Open

When opened:

* Shows menu
* Notifies `UIManager`
* Populates quests
* Restores selection

## Close

When closed:

* Hides menu
* Preserves selected quest state

## Toggle

Controlled through:

```
toggle_quest_menu
```

---

# 🏗 System Dependencies

## QuestManager

Primary data source.

Provides:

* Active quests
* Completed quests
* Tracking state
* Quest rules

## QuestInstance

Provides:

* Quest status
* Objective states
* Completion state

## QuestDefinition

Provides:

* Quest metadata
* Objective definitions
* Rewards

## ItemDatabase

Used for displaying reward names and item objective fallback text.

## UIManager

Controls UI interaction state.

---

# System Responsibility Summary

| System          | Responsibility                               |
| --------------- | -------------------------------------------- |
| QuestDefinition | Static quest data                            |
| QuestInstance   | Runtime quest state                          |
| QuestManager    | Quest authority                              |
| QuestMenu       | Quest presentation and player UI interaction |

---

# Current Features

✅ Active quest display
✅ Completed quest display
✅ Objective progress rendering
✅ Objective gating visualization
✅ Ready-for-turn-in state
✅ Completed objective state
✅ Quest tracking controls
✅ Side quest tracking limits
✅ Quest cancellation controls
✅ Reward display
✅ Signal-driven UI updates

# 🎯 Quest HUD Tracker Systems

## Overview

The Quest HUD system is split into two dedicated UI trackers:

- **QuestHUDTracker** → Displays the active STORY quest
- **SideQuestTracker** → Displays multiple tracked non-STORY quests

Both trackers are **pure UI presentation layers**.

They do not control:
- Quest progression
- Objective completion
- Quest tracking rules
- Rewards
- Validation

All quest authority remains inside:

```

QuestManager
|
v
QuestInstance
|
v
Quest HUD Trackers
|
v
UI Rendering

````

---

# 🛡️ QuestHUDTracker (Story Quest HUD)

## Purpose

The Story Quest HUD displays the player's primary story progression quest.

It is designed around a **single active quest experience**.

Responsibilities:

- Display current story quest title
- Display active story objectives
- Display objective progress
- Display locked objectives
- Display turn-in objectives
- Animate objective updates
- React to QuestManager signals

---

## Data Authority

The tracker reads from:

```gdscript
QuestManager.story_tracked_quest_id
````

The currently tracked STORY quest is selected by QuestManager.

The tracker does not choose quests.

---

## Quest Selection Logic

Priority:

1. Use the currently tracked story quest

```
QuestManager.story_tracked_quest_id
```

2. If no tracked story quest exists:

Search active quests for the first available STORY quest.

Completed story quests are ignored.

---

## Objective Display Rules

The Story HUD displays:

### Active Objectives

Displayed normally:

```
☐ Collect 5 Iron Ore (2/5)
```

---

### Locked Objectives

Objectives blocked by gates are displayed:

```
🔒 Speak with the Captain
```

with reduced opacity/color.

---

### Turn-In Objectives

Completed TURN_IN objectives remain visible:

```
✓ Return to Captain
```

Highlighted using the turn-in color.

---

### Completed Objectives

Completed objectives are removed from the HUD unless they require player interaction.

Example:

Removed:

```
✓ Kill 10 Wolves
```

Kept:

```
✓ Return to Town
```

---

## Objective Ordering

Objectives are sorted by unlock state:

Priority:

1. Available objectives
2. Locked objectives

This keeps the player's current actions visible first.

---

## Animation System

The tracker supports:

### Quest Title Transitions

* Fade out old title
* Replace text
* Fade in new title

### Objective Row Transitions

New objectives:

* Fade in

Removed objectives:

* Fade out
* Cleanup after animation

### Objective Updates

Progress changes animate:

```
Collect Ore (2/5)

        ↓

Collect Ore (3/5)
```

---

# 🧭 SideQuestTracker

## Purpose

The SideQuestTracker displays multiple optional quests simultaneously.

Designed for:

* Side quests
* Faction quests
* Daily quests
* Optional chains

---

## Data Authority

The tracker reads from:

```gdscript
QuestManager.tracked_quests
```

Only quests manually or automatically tracked by QuestManager appear.

---

## Multi Quest Support

Unlike the Story HUD, SideQuestTracker supports multiple active panels.

Example:

```
SIDE QUEST
────────────
Lost Supplies

☐ Find Supplies
☐ Return Supplies


SIDE QUEST
────────────
Bandit Trouble

☐ Kill Bandits
☐ Loot Camp
```

---

## Panel Independence

Each side quest has its own:

* Quest title
* Objective rows
* Animation state
* Tween handling
* Objective cache

Each panel is managed independently.

---

## Objective Limits

Side quests support configurable limits:

```gdscript
max_objectives_per_quest
```

This prevents the HUD from becoming overloaded.

Example:

```
Quest:
  Objective 1 ✓
  Objective 2 ✓
  Objective 3 Active
  Objective 4 Hidden
```

---

# 🔄 Shared Features

Both trackers support:

## Signal Driven Updates

Connected signals:

```gdscript
quest_started
quest_updated
quest_completed
quest_selected
quest_canceled
quest_failed
```

The HUD updates only when quest state changes.

---

## Objective States

Both trackers understand:

| State         | Display                            |
| ------------- | ---------------------------------- |
| Active        | Normal text/icon                   |
| Locked        | Disabled text/icon                 |
| Turn In Ready | Highlighted                        |
| Completed     | Hidden unless interaction required |

---

## Shared Rendering Features

Both systems support:

* Dynamic objective rows
* Icon swapping
* Color states
* Fade animations
* Localization
* Objective progress display

---

# 🧩 Architectural Difference

| Feature          | QuestHUDTracker          | SideQuestTracker  |
| ---------------- | ------------------------ | ----------------- |
| Quest Type       | STORY                    | Non-STORY         |
| Active Quests    | 1                        | Multiple          |
| Authority Source | `story_tracked_quest_id` | `tracked_quests`  |
| Purpose          | Main progression         | Optional content  |
| Layout           | Single panel             | Multiple panels   |
| Tracking Control | Automatic                | Player controlled |
| Objective Limit  | Global max               | Per quest max     |

---

# Final Architecture

```
                    QuestManager
                         |
        --------------------------------
        |                              |
story_tracked_quest_id          tracked_quests
        |                              |
        v                              v

 QuestHUDTracker              SideQuestTracker

 (Main Story)                 (Side Content)

        |                              |
        --------------------------------
                         |
                         v

                    HUD Rendering
```

The result is a Diablo-style quest HUD architecture where the main story always has priority while optional content can coexist without competing for the same display space.

```


