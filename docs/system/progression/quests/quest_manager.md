# 📜 Quest Manager Architecture

The **Quest Manager** is the central public API for the quest system.

It exposes quest state, lifecycle operations, tracking, event notification, dialogue offers, quest-line progression, and save/load functionality while delegating specialized behavior to dedicated quest subsystems.

The manager acts as the primary integration point used by UI, dialogue, gameplay, minimap, save systems, and other runtime systems.

---

## Responsibilities

The Quest Manager is responsible for:

* Centralized quest state ownership
* Public quest lifecycle API
* Quest definition lookup
* Quest availability and requirement checks
* Quest tracking access
* Quest event routing
* Quest-line construction and progression
* Dialogue quest-offer state
* Timed quest processing
* Death-based quest failure
* Quest serialization and deserialization
* Forwarding quest signals to external systems

Specialized quest behavior is delegated to internal helper classes rather than being implemented entirely inside the manager.

---

## Architecture

```text
                         ┌──────────────────────┐
                         │     QuestManager      │
                         │   Public Quest API    │
                         └──────────┬───────────┘
                                    │
             ┌──────────────────────┼──────────────────────┐
             │                      │                      │
             ▼                      ▼                      ▼
     ┌───────────────┐      ┌─────────────────┐    ┌────────────────┐
     │ QuestTracker  │      │ QuestEvent      │    │ QuestLifecycle │
     │               │      │ Processor       │    │                │
     │ Tracking      │      │                 │    │ Start /        │
     │ Selection     │      │ Gameplay events │    │ Complete /     │
     │ Track rules   │      │ Objective       │    │ Fail / Cancel  │
     └───────────────┘      │ progression     │    │ Reset          │
                            └─────────────────┘    └────────────────┘

                                    │
                                    ▼
                           ┌──────────────────┐
                           │ Quest Definitions │
                           │   QuestDatabase    │
                           └──────────────────┘
```

---

## Public API Layer

`QuestManager` provides the stable interface other systems use to interact with quests.

External systems should communicate with the Quest Manager rather than directly manipulating the internal quest subsystems.

Primary API areas include:

| API Area        | Purpose                                       |
| --------------- | --------------------------------------------- |
| Quest Lifecycle | Start, complete, fail, cancel, reset quests   |
| Quest State     | Query active/completed/failed quests          |
| Tracking        | Select, track, untrack, and validate tracking |
| Definitions     | Retrieve quest definitions                    |
| Events          | Notify the quest system of gameplay events    |
| Quest Lines     | Register and advance quest sequences          |
| Dialogue        | Manage active NPC quest offers                |
| Serialization   | Save and restore quest state                  |
| Rules           | Resolve category-based quest behavior         |

---

## Quest State

The manager owns the primary runtime quest collections:

```text
active_quests
completed_quests
failed_quests
quest_lines
_active_offers
```

### Active Quests

Contains currently active `QuestInstance` objects.

### Completed Quests

Stores quest IDs that have been completed.

### Failed Quests

Stores quest IDs that have failed.

### Quest Lines

Stores ordered quest IDs grouped by quest-line ID.

### Active Offers

Stores NPC → quest mappings for dialogue-driven quest offers.

---

# Quest Subsystems

## QuestTracker

`QuestTracker` owns quest tracking and selection behavior.

Responsibilities include:

* Selected quest state
* Story quest tracking
* Tracked quest collection
* Track/untrack validation
* Category-based tracking rules
* Track/cancel button availability

The manager exposes this functionality through methods such as:

```text
select_quest()
can_track_quest()
can_cancel_quest()
can_show_track_button()
can_show_cancel_button()
is_tracked()
untrack_quest()
```

The manager also exposes:

```text
story_tracked_quest_id
tracked_quests
```

as read-only accessors backed by `QuestTracker`.

---

## QuestEventProcessor

`QuestEventProcessor` receives gameplay events and updates active quest objectives.

It handles:

* Generic quest events
* Item count changes
* Target kills
* Player state synchronization
* Objective progression

Gameplay systems connect into the processor through the Quest Manager:

```text
ItemCountManager
        │
        ▼
QuestEventProcessor

CombatManager
        │
        ▼
QuestEventProcessor
```

Other systems can submit events through:

```text
QuestManager.notify_event(event)
```

and synchronize player state through:

```text
QuestManager.sync_player_state(level)
```

---

## QuestLifecycle

`QuestLifecycle` owns quest state transitions.

Responsibilities include:

* Starting quests
* Completing quests
* Failing quests
* Canceling quests
* Resetting quests
* Applying category behavior
* Advancing quest lines
* Emitting lifecycle callbacks

The manager exposes lifecycle operations through:

```text
start_quest()
complete_quest()
fail_quest()
cancel_quest()
reset_quest()
```

The lifecycle subsystem receives manager callbacks for signal emission and quest-line progression.

---

# Quest Behavior

Quest behavior is resolved through `QuestBehaviorMatrix`.

```text
QuestDefinition
       │
       ▼
QuestBehaviorMatrix
       │
       ▼
QuestBehavior
```

The manager provides:

```text
get_behavior(def)
```

as the public behavior lookup API.

Behavior can determine category-specific rules such as:

* Tracking
* Cancellation
* Reset behavior
* Other quest-category lifecycle rules

This keeps category-specific behavior outside the core manager.

---

# Quest Initialization

Initialization establishes the quest system's runtime dependencies.

```text
initialize()
    │
    ├── QuestBehaviorMatrix.setup()
    │
    ├── build_quest_lines_from_definitions()
    │
    ├── QuestTracker.setup()
    │
    ├── QuestEventProcessor.setup()
    │
    ├── QuestLifecycle.setup()
    │
    ├── ItemCountManager → QuestEventProcessor
    │
    └── CombatManager → QuestEventProcessor
```

Initialization is guarded by:

```text
is_initialized
```

to prevent duplicate setup.

---

# Quest Definition Access

Quest definitions remain data-driven through `QuestDatabase`.

The manager provides:

```text
get_definition(quest_id)
```

as the public definition lookup API.

This keeps callers from needing to interact with the database directly for common quest operations.

---

# Quest Availability

The manager determines whether a quest can begin through:

```text
can_start_quest(def)
```

Validation includes:

* Existing active quest state
* Completed/non-repeatable state
* Timed quest validity
* Quest requirements
* Definition validity

Automatic quests are evaluated through:

```text
start_auto_quests()
```

which scans available definitions and starts eligible quests marked for automatic activation.

---

# Quest Lines

Quest lines are constructed from quest definitions during initialization.

Definitions containing a `quest_line_id` are grouped and ordered using:

```text
quest_index
```

The resulting runtime structure is:

```text
quest_lines[line_id] = [
    quest_id,
    quest_id,
    quest_id,
    ...
]
```

When a quest completes, the manager can advance the associated quest line through:

```text
_advance_quest_line()
```

The next quest is started and selected automatically.

Quest lines can also be registered directly through:

```text
register_quest_line()
```

---

# Dialogue Integration

The Quest Manager provides lightweight state management for NPC quest offers.

```text
NPC ID
  │
  ▼
_active_offers
  │
  ▼
Quest ID
```

Public methods:

```text
set_active_offer()
get_active_offer()
clear_offer()
```

This allows `DialogueManager` to manage quest conversations without directly owning quest-offer state.

---

# Timed Quests

The manager processes active quest timers from `_process()`.

```text
QuestManager
      │
      ▼
active_quests
      │
      └── QuestInstance.update_timer(delta)
```

Timer behavior remains owned by the individual `QuestInstance`, while the manager provides the runtime update loop.

---

# Death-Based Failure

The manager listens for player death handling through:

```text
_on_player_died()
```

Active quests are evaluated for:

```text
definition.fail_on_death
```

Eligible quests are routed through:

```text
fail_quest(id)
```

rather than directly modifying their state.

---

# Signals

The manager exposes the primary quest lifecycle signals used by external systems.

```text
quest_selected(id)
quest_started(id)
quest_updated(id)
quest_completed(id)
quest_failed(id)
quest_canceled(id)
```

These signals provide the external event interface for systems such as:

* Quest UI
* Dialogue
* Minimap
* Event Viewer
* Notifications
* Other gameplay systems

Internal helper methods forward lifecycle events into these public signals.

---

# Serialization

The Quest Manager owns the public quest save/load interface.

### Serialize

```text
serialize_quests()
```

Stores:

* Active quest instances
* Completed quest IDs
* Failed quest IDs
* Story tracked quest
* Tracked quests

### Deserialize

```text
deserialize_quests(data)
```

Restores:

1. Quest history
2. Tracking state
3. Active quest definitions
4. Active `QuestInstance` objects
5. Serialized quest progress

Definitions are re-resolved through `QuestDatabase` during restoration.

Missing definitions are skipped with a warning rather than creating invalid quest instances.

---

# External Integration

The Quest Manager serves as the integration boundary for the quest system.

```text
                         QuestManager
                              │
       ┌──────────────┬───────┼────────┬──────────────┐
       ▼              ▼       ▼        ▼              ▼
   Dialogue        Minimap   Quest UI  Save         Gameplay
       │              │       │        │              │
       └──────────────┴───────┴────────┴──────────────┘
                              │
                              ▼
                    Quest Manager Public API
```

### Dialogue

Uses quest availability, active state, offers, and lifecycle operations.

### Minimap

Uses active quest state, objective state, quest definitions, and lifecycle signals.

### Quest UI

Uses tracking APIs, quest state, definitions, sorting helpers, and lifecycle signals.

### Save System

Uses `serialize_quests()` and `deserialize_quests()`.

### Gameplay Systems

Submit objective events through `notify_event()` and synchronize player state through `sync_player_state()`.

---

# Design Principle

`QuestManager` is intentionally structured as a **public API and orchestration layer** rather than a monolithic implementation.

```text
QuestManager
    │
    ├── Public API
    ├── State ownership
    ├── Integration boundary
    └── Signal boundary
         │
         ├── QuestTracker
         ├── QuestEventProcessor
         └── QuestLifecycle
```

Specialized quest behavior remains inside dedicated components while the manager provides a consistent interface to the rest of the engine.

This allows external systems to interact with quests without needing to understand the internal implementation of tracking, event processing, or lifecycle management.
