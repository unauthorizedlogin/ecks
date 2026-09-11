# 🎯 Quest Event Processor Architecture

### Purpose

`QuestEventProcessor` processes external gameplay events and applies them to active quests. It provides a centralized event-processing layer for objective progress, objective unlocking, player-state synchronization, and quest update notifications.

### Architecture Role

```text
External Game Events
        ↓
QuestEventProcessor
        ↓
QuestInstance
        ↓
Objective State / Progress
        ↓
Quest Manager Callback
```

The processor operates on the Quest Manager's active quest collection without owning the quests themselves.

### Responsibilities

* Maintain a reference to active quests.
* Process external gameplay events.
* Update objective progress across active quests.
* Enforce objective prerequisite gates.
* Refresh objective states after event processing.
* Process inventory/item-count changes.
* Convert target-kill events into standardized quest events.
* Track the player's current level snapshot.
* Notify the Quest Manager when quest state changes.
* Synchronize player state into the event-processing layer.

### Setup

`setup()` receives:

* The Quest Manager's `active_quests` dictionary.
* A callback used to report quest updates.

This keeps quest ownership and signal management outside the processor while allowing the processor to operate directly on the active quest collection.

### External Event Handling

#### Item Count Changes

`on_item_count_changed()` refreshes objective states for every active quest and reports each affected quest through the `quest_updated` callback.

#### Target Kills

`on_target_killed()` converts actor information into the standardized `KILL` objective event format.

The generated event includes:

* Target ID
* Target category
* Target subtype
* Killer ID
* Killer category
* Killer subtype
* Kill amount

This provides objective processing with normalized actor information rather than coupling quest logic directly to combat actors.

### Objective Validation

Before an event targeting a specific objective is processed, `_is_objective_unlocked()` checks that objective's `objective_gate` requirements.

An objective is unlocked when:

* It has no prerequisite gate, or
* Every referenced prerequisite exists in the quest's `completed_objectives`.

Locked objectives are rejected before progress processing.

```text
Quest Event
    ↓
Objective ID?
    ↓
Check Objective Gate
    ↓
Unlocked ───────→ Update Progress
    │
    └─ Locked ──→ Reject Event
```

### Event Processing

`notify_event()` provides the primary generic event entry point.

For each active quest it:

1. Identifies the event type.
2. Updates the stored player level for `level_snapshot` events.
3. Validates objective gates when an `objective_id` is supplied.
4. Passes the event type and payload to `QuestInstance.update_progress()`.
5. Refreshes objective states.
6. Invokes the Quest Manager's update callback.

This allows different gameplay systems to submit standardized quest events without directly modifying quest instances.

### Player State Synchronization

`sync_player_state()` sends the current player level through the normal event-processing pipeline using a `level_snapshot` event.

This keeps player-state synchronization consistent with other quest events rather than introducing a separate quest update mechanism.

### File(s) Affected

* `quest_event_processor.gd`
* `QuestInstance` — objective progress/state dependency
* `QuestDefinition`
* `ObjectiveDefinition`
* Quest Manager — active quest ownership and update callback

### Notes

`QuestEventProcessor` is a **stateless processing layer with shared quest-state references**, rather than a quest owner. Its primary architectural purpose is to decouple gameplay event sources from `QuestInstance` progress handling while enforcing objective dependency gates before events are applied.
