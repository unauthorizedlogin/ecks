# 🎯 Quest Tracker Architecture

### Purpose

`QuestTracker` manages quest tracking state and tracking eligibility. It distinguishes between the dedicated story-tracked quest and general tracked quests while providing validation for tracking and cancellation UI actions.

### Architecture Role

```text
QuestManager
    ↓
QuestTracker
    ├── Active Quest State
    ├── QuestDefinition
    └── Quest Behavior
```

`QuestTracker` is a `RefCounted` service that operates against the Quest Manager's active quest collection through callbacks.

### Responsibilities

* Maintain tracked quest IDs.
* Maintain the currently story-tracked quest.
* Validate whether quests can be tracked.
* Validate whether quests can be canceled.
* Select and untrack quests.
* Clear tracking state.
* Restore tracking state from saved data.
* Notify the Quest Manager when tracking selection changes.
* Generate tracking event logs.

### Tracking Model

Quest tracking uses two separate paths:

```text
STORY Quest
    → story_tracked_quest_id

Non-STORY Quest
    → tracked_quests[]
```

Achievement quests cannot be tracked.

Completed quests cannot be tracked.

### Tracking Validation

`can_track_quest()` validates that:

* The quest exists and is active.
* Valid behavior configuration exists.
* The quest is not completed.
* The quest is not an achievement.

`can_cancel_quest()` delegates cancellation eligibility to the quest's behavior configuration.

The `can_show_track_button()` and `can_show_cancel_button()` methods expose these same checks directly for UI consumers.

### Quest Selection

`select_quest()` validates the quest before assigning tracking state.

Story quests replace the current `story_tracked_quest_id`.

Other eligible quests are added to `tracked_quests` without duplicate entries.

Selection generates a quest tracking log and invokes the `quest_selected` callback.

### Untracking

`untrack_quest()` removes a quest from the general tracking collection and notifies the Quest Manager through the selection callback.

`clear_tracking()` removes the quest from either tracking path without generating a tracking event.

### Save Restoration

`restore_tracking()` reconstructs tracking state from persisted data:

* Restores the story-tracked quest ID.
* Clears existing general tracking.
* Restores the saved general tracked quest IDs.

### File(s) Affected

* `quest_tracker.gd`
* `QuestManager`
* Quest tracking UI systems

### Notes

`QuestTracker` isolates **tracking state and eligibility** from the Quest Manager's lifecycle and event-processing responsibilities.

```text
QuestManager
├── QuestLifecycle
│   └── Quest state transitions
│
├── QuestEventProcessor
│   └── Gameplay events → objective progress
│
└── QuestTracker
    └── Tracking state and eligibility
```
