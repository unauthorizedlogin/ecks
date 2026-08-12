# 📜 Quest Manager

## Overview

The **Quest Manager** is the central runtime authority for the game's quest system.

It manages quest definitions, active quest instances, quest progression, objectives, tracking, completion, failure, cancellation, quest lines, event-driven objective updates, dialogue quest offers, and quest persistence.

The Quest Manager separates **quest definition data** from **runtime quest state**.

Quest definitions describe what a quest is, while `QuestInstance` stores the player's current progress.

The Quest Manager coordinates the two.

This includes:

* 📜 Quest definition loading
* 🎯 Quest activation and progression
* 📊 Objective event processing
* 🧩 Quest category behavior
* 🧭 Quest tracking
* 🏆 Achievement handling
* 🔗 Quest line progression
* ⏱️ Timed quest updates
* ❌ Quest failure and cancellation
* 🔄 Quest reset and repeat behavior
* 💬 Dialogue quest offers
* 💾 Quest serialization
* 📂 Quest deserialization

---

# 🧠 Core Responsibilities

The Quest Manager provides:

* Quest definition registration
* Quest database initialization
* Quest instance creation
* Quest start validation
* Quest progression routing
* Objective gating
* Quest completion
* Quest failure
* Quest cancellation
* Quest reset
* Quest tracking
* Quest line advancement
* Quest event notification
* Quest timer updates
* Quest state serialization
* Quest state restoration
* Quest behavior resolution

The manager does not define individual quest content.

Quest behavior is driven by:

* `QuestDefinition`
* `QuestInstance`
* `QuestBehaviorMatrix`
* `ObjectiveDefinition`
* `RequirementsManager`

---

# ⚙️ Initialization

```gdscript
func initialize() -> void
```

Registers the Quest Manager as an active framework system.

Initialization is protected against duplicate execution.

During initialization the manager connects to external systems that can generate quest progression events.

```text
QuestManager.initialize()
        │
        ├── ItemCountManager.item_count_changed
        │
        └── CombatManager.target_killed
```

Output:

```text
📜 QuestManager initialized
```

The Quest Manager also provides a separate disk initialization stage for loading quest definitions.

```gdscript
func initialize_from_disk() -> void
```

This loads quest resources through the generated quest index and builds the quest-line registry.

---

# 📂 Quest Definition Loading

Quest definitions are loaded from the generated `QuestIndex`.

```text
QuestIndex
    ↓
QuestDefinition Resources
    ↓
register_definition()
    ↓
quest_definitions
    ↓
Quest Line Registry
```

The loading pipeline consists of:

```gdscript
initialize_from_disk()
load_quests()
register_definition()
finalize_definitions()
build_quest_lines_from_definitions()
```

Each `QuestDefinition` is registered using its `quest_id`.

```gdscript
quest_definitions[def.quest_id] = def
```

The resulting dictionary provides centralized runtime lookup.

```text
quest_id
   ↓
QuestDefinition
```

---

# 🧩 Quest Definition vs Runtime Instance

The Quest Manager maintains a strict separation between static quest data and runtime progress.

```text
QuestDefinition
│
├── Quest identity
├── Category
├── Requirements
├── Objective definitions
├── Rewards
├── Quest line
├── Timing rules
└── Behavior configuration
          │
          ↓
    QuestInstance
          │
          ├── Current status
          ├── Objective progress
          ├── Completed objectives
          ├── Timer
          └── Runtime state
```

`QuestDefinition` represents the reusable quest configuration.

`QuestInstance` represents the player's active runtime copy of that definition.

---

# 🎯 Quest Tracking

The Quest Manager maintains three major quest-state collections:

```gdscript
var active_quests: Dictionary
var completed_quests: Array[String]
var failed_quests: Array[String]
```

Active quests contain runtime `QuestInstance` objects.

Completed and failed quests contain quest IDs.

Tracking is maintained separately:

```gdscript
var story_tracked_quest_id: String = ""
var tracked_quests: Array[String] = []
```

Story quests use a dedicated tracked quest slot.

Other trackable quests use the general tracking collection.

```text
Quest State
│
├── Active
│   └── QuestInstance
│
├── Completed
│   └── Quest ID
│
├── Failed
│   └── Quest ID
│
└── Tracking
    ├── Story Quest
    └── Other Tracked Quests
```

Achievements cannot be tracked through the normal quest tracker.

---

# 🚀 Quest Starting

```gdscript
func start_quest(id: String)
```

Starting a quest performs validation before creating a runtime instance.

```text
Quest ID
   ↓
QuestDefinition
   ↓
can_start_quest()
   │
   ├── Already active?
   ├── Already completed?
   ├── Repeatable?
   ├── Valid timer?
   └── Requirements satisfied?
   ↓
QuestInstance
   ↓
active_quests
```

The Quest Manager creates a new `QuestInstance`, assigns the definition, starts the instance, and registers it in `active_quests`.

Quest start also generates a quest-start objective event so other active quests can respond to the newly started quest.

If the quest behavior specifies automatic tracking, the Quest Manager selects the quest automatically.

If the quest is configured for automatic completion, completion is deferred until after the start operation.

---

# 🔐 Quest Start Validation

```gdscript
func can_start_quest(def: QuestDefinition) -> bool
```

A quest cannot start when:

* The definition is missing
* The quest is already active
* The quest has already been completed and is not repeatable
* A timed quest has an invalid time limit
* Its requirements are not satisfied

Requirements are evaluated through:

```gdscript
RequirementsManager.validate_quest_requirements()
```

This keeps requirement logic outside the Quest Manager.

---

# 🧠 Quest Category Behavior

Quest behavior is resolved through the centralized behavior matrix.

```gdscript
func get_behavior(def: QuestDefinition) -> QuestBehavior
```

```text
QuestDefinition
      ↓
Quest Category
      ↓
QuestBehaviorMatrix
      ↓
QuestBehavior
```

The behavior matrix controls category-specific rules such as:

* Automatic tracking
* Reset capability
* Cancellation
* Other category-specific behavior

This prevents the Quest Manager from hardcoding separate logic for every quest category.

---

# 📊 Quest Event Pipeline

The Quest Manager uses an event-driven progression model.

```gdscript
func notify_event(event: Dictionary)
```

External systems publish gameplay events to the Quest Manager.

Examples include:

* Item count changes
* Enemy kills
* Quest completion
* Level snapshots
* Other objective events

The general pipeline is:

```text
Gameplay System
      ↓
Quest Event
      ↓
QuestManager.notify_event()
      ↓
Objective Gate Check
      ↓
QuestInstance.update_progress()
      ↓
refresh_objective_states()
      ↓
quest_updated
```

This allows quest objectives to react to gameplay without tightly coupling individual gameplay systems to quest logic.

---

# ⚔️ Combat Integration

The Quest Manager listens to:

```gdscript
CombatManager.target_killed
```

When a target is killed, the manager converts the combat event into a quest objective event.

```text
CombatManager
      │
      │ target_killed
      ↓
QuestManager
      │
      ↓
KILL Objective Event
      │
      ├── target_id
      ├── target_category
      ├── target_subtype
      ├── killer_id
      ├── killer_category
      ├── killer_subtype
      └── amount
      ↓
Active Quest Instances
```

This allows kill objectives to use actor identity and classification rather than relying only on a display name.

---

# 📦 Item Integration

The Quest Manager listens to:

```gdscript
ItemCountManager.item_count_changed
```

When inventory item counts change, active quests refresh their objective states.

```text
ItemCountManager
       ↓
item_count_changed
       ↓
QuestManager
       ↓
Active Quest Instances
       ↓
refresh_objective_states()
       ↓
quest_updated
```

This allows collection objectives to remain synchronized with inventory state.

---

# 🔒 Objective Gating

Objectives can depend on other objectives being completed.

```gdscript
func _is_objective_unlocked(
    quest: QuestInstance,
    objective_id: String
) -> bool
```

An objective without an `objective_gate` is immediately available.

An objective with gates remains locked until every required objective has been completed.

```text
Objective A
    │
    └── Completed
          ↓
Objective B
    │
    └── Unlocked
          ↓
Objective C
```

The gating check occurs before an event is forwarded to the quest instance.

```text
Quest Event
    ↓
Objective ID?
    ↓
_is_objective_unlocked()
    │
    ├── NO → Ignore Event
    │
    └── YES
         ↓
    update_progress()
```

This prevents progression events from affecting objectives that are not yet available.

---

# 🎯 Quest Event Notification

```gdscript
func notify_event(event: Dictionary)
```

The Quest Manager broadcasts an incoming gameplay event to every active quest.

Before processing, player level snapshots are cached when applicable.

Each active quest then receives:

```gdscript
quest.update_progress(
    event.type,
    event.data
)
```

Afterward:

```gdscript
quest.refresh_objective_states()
```

and:

```gdscript
quest_updated
```

is emitted.

This makes the Quest Manager the central routing layer between gameplay events and quest objectives.

---

# ⏱️ Timed Quests

The Quest Manager updates every active quest instance each frame.

```gdscript
func _process(delta: float) -> void
```

```text
QuestManager
     ↓
_process(delta)
     ↓
Active Quest Instances
     ↓
QuestInstance.update_timer(delta)
```

Timer behavior remains inside `QuestInstance`, while the Quest Manager provides the centralized runtime update.

This keeps timing execution separate from quest lifecycle management.

---

# 🏆 Quest Completion

```gdscript
func complete_quest(id: String) -> void
```

Completion performs several coordinated operations.

```text
QuestInstance
      ↓
complete()
      ↓
Notify Other Active Quests
      ↓
Record Completed Quest
      ↓
Remove From Active Quests
      ↓
RewardManager
      ↓
Quest Events / Logs
      ↓
Quest Line Advancement
```

When a quest completes:

1. The quest instance is marked completed.
2. Other active quests receive a `QUEST_COMPLETE` objective event.
3. The quest ID is added to `completed_quests`.
4. The active quest instance is removed.
5. `RewardManager.grant_rewards()` grants the configured rewards.
6. The appropriate quest or achievement message is emitted.
7. `quest_updated` and `quest_completed` are emitted.
8. The next quest in the quest line is evaluated.

---

# 🏆 Achievement Integration

Achievements use the same quest runtime architecture.

```gdscript
QuestDefinition.QuestCategory.ACHIEVEMENT
```

is treated as a quest category with specialized behavior.

When an achievement completes, the Quest Manager uses the achievement-specific event channel and message presentation.

```text
Quest System
      ↓
QuestDefinition
      ↓
Category = ACHIEVEMENT
      ↓
QuestManager
      ↓
Achievement Completion
```

Achievements share the same objective, progression, completion, reward, and persistence infrastructure while remaining distinct from normal tracked quests.

---

# 🔗 Quest Lines

Quest definitions may belong to a quest line through:

```gdscript
quest_line_id
quest_index
```

The Quest Manager builds the quest-line registry during definition finalization.

```text
Quest Definitions
      ↓
quest_line_id
      ↓
Group Definitions
      ↓
Sort by quest_index
      ↓
Quest Line
```

Example:

```text
Quest Line: MAIN_STORY
        │
        ├── quest_001
        ├── quest_002
        ├── quest_003
        └── quest_004
```

When a quest completes, `_advance_quest_line()` searches for the completed quest and starts the next quest in the sequence.

---

# 🔄 Quest Reset

```gdscript
func reset_quest(id: String)
```

Reset behavior is controlled by `QuestBehavior`.

Only resettable quest categories may be reset.

For an active quest, its runtime progress is reset through:

```gdscript
QuestInstance.reset()
```

For a completed quest, its ID is removed from `completed_quests`, allowing it to become available again.

Daily quests can be reset collectively through:

```gdscript
func reset_daily_quests()
```

```text
Daily Quest
     ↓
QuestBehavior
     ↓
Resettable?
     ↓
reset_quest()
     ↓
Progress Cleared
```

---

# ❌ Quest Cancellation and Failure

Quest cancellation removes an active quest from the runtime state and records it as failed.

```gdscript
func cancel_quest(id: String)
```

Cancellation is governed by the quest category's behavior rules.

```gdscript
func can_cancel_quest(id: String) -> bool
```

Quest failure uses the same cancellation path and then emits the dedicated failure signal.

```text
Active Quest
     ↓
Failure / Cancellation
     ↓
active_quests.remove()
     ↓
failed_quests
     ↓
Quest Event
```

Player death can automatically fail quests whose definitions specify:

```gdscript
fail_on_death
```

---

# ☠️ Player Death Integration

```gdscript
func _on_player_died(_player: Node)
```

When the player dies, the Quest Manager evaluates every active quest.

```text
Player Death
     ↓
QuestManager
     ↓
Active Quests
     ↓
fail_on_death?
     │
     ├── YES → fail_quest()
     └── NO  → Continue
```

This allows quest failure behavior to be configured per quest definition.

---

# 🧭 Quest Tracking Rules

Tracking is controlled by quest category behavior and quest state.

```gdscript
func can_track_quest(id: String) -> bool
```

A quest cannot be tracked when:

* It does not exist as an active quest
* It is completed
* Its category behavior does not permit tracking
* It is an achievement

Story quests are tracked through:

```gdscript
story_tracked_quest_id
```

Other quests use:

```gdscript
tracked_quests
```

The Quest Manager also exposes UI-facing helpers:

```gdscript
can_show_track_button()
can_show_cancel_button()
is_tracked()
untrack_quest()
```

This allows the Quest Menu to query quest actions without implementing quest rules itself.

---

# 💬 Dialogue Quest Offers

The Quest Manager maintains temporary quest offers associated with NPCs.

```gdscript
set_active_offer(npc_id, quest_id)
get_active_offer(npc_id)
clear_offer(npc_id)
```

```text
NPC Dialogue
      ↓
Quest Offer
      ↓
QuestManager
      ↓
NPC ID → Quest ID
```

This allows dialogue systems to determine which quest an NPC is currently offering without embedding quest state directly into dialogue logic.

---

# 💾 Quest Serialization

```gdscript
func serialize_quests() -> Dictionary
```

The Quest Manager serializes the complete player quest state.

```text
QuestManager
     │
     ├── Active Quest Instances
     ├── Completed Quest IDs
     ├── Failed Quest IDs
     ├── Story Tracked Quest
     └── Tracked Quests
             ↓
       Serialized Dictionary
```

Serialized data contains:

```text
{
    active_quests,
    completed_quests,
    failed_quests,
    story_tracked_quest_id,
    tracked_quests
}
```

Active quest instances are responsible for serializing their individual runtime progress.

---

# 📂 Quest Deserialization

```gdscript
func deserialize_quests(data: Dictionary)
```

Deserialization clears the current runtime state and reconstructs it from saved data.

```text
Save Data
    ↓
deserialize_quests()
    │
    ├── Restore completed quests
    ├── Restore failed quests
    ├── Restore story tracking
    ├── Restore tracked quests
    │
    └── Restore active quests
             ↓
        QuestDefinition Lookup
             ↓
        QuestInstance
             ↓
        QuestInstance.deserialize()
             ↓
        active_quests
```

Active quest definitions are resolved from the current `quest_definitions` registry.

If a saved quest no longer has a corresponding definition, the Quest Manager skips it and emits a warning.

---

# 🔗 System Relationships

```text
                         QuestManager
                              │
          ┌───────────────────┼───────────────────┐
          ↓                   ↓                   ↓
 QuestDefinition        QuestInstance      QuestBehaviorMatrix
          │                   │                   │
          │                   ├── Objectives      └── Category Rules
          │                   ├── Progress
          │                   └── Timer
          │
          ↓
     Quest Lines
          
          QuestManager
               │
     ┌─────────┼─────────┬──────────────┐
     ↓         ↓         ↓              ↓
 Combat    ItemCount  Dialogue    Requirements
 Manager   Manager      System       Manager
     │         │         │              │
     └─────────┴─────────┴──────────────┘
                       ↓
                 Quest Progression
                       │
          ┌────────────┼────────────┐
          ↓            ↓            ↓
    RewardManager   EventManager   Quest UI
```

---

# 🧭 System Boundaries

| Responsibility               | System                      |
| ---------------------------- | --------------------------- |
| Quest definition             | `QuestDefinition`           |
| Quest runtime state          | `QuestInstance`             |
| Quest category rules         | `QuestBehaviorMatrix`       |
| Objective definition         | `ObjectiveDefinition`       |
| Quest requirement validation | `RequirementsManager`       |
| Quest rewards                | `RewardManager`             |
| Kill events                  | `CombatManager`             |
| Item count events            | `ItemCountManager`          |
| Quest dialogue offers        | Dialogue System             |
| Quest presentation/logging   | `EventManager`              |
| Quest UI                     | Quest Menu                  |
| Quest persistence            | Save System / Quest Manager |
| Quest lifecycle coordination | `QuestManager`              |

The Quest Manager coordinates these systems but does not absorb their specialized responsibilities.

---

# 🌱 Data-Driven Quest Architecture

The quest system separates **content definition**, **runtime state**, and **system behavior**.

```text
Quest Content
     ↓
QuestDefinition
     ↓
QuestManager
     │
     ├── QuestBehaviorMatrix
     ├── RequirementsManager
     └── QuestInstance
              ↓
        Runtime Progress
              ↓
        Quest Events
              ↓
        Completion / Failure
              ↓
        Rewards + Persistence
```

This allows quest content to be created and generated independently from the runtime systems that execute it.

---

# 🚀 Launch Flow Integration

The Quest Manager is initialized as part of the framework's centralized launch process.

Quest definitions are subsequently loaded through the generated quest index.

```text
Framework Launch
      ↓
QuestManager.initialize()
      ↓
Connect Gameplay Events
      ↓
QuestManager.initialize_from_disk()
      ↓
QuestIndex
      ↓
Quest Definitions
      ↓
Quest Line Registry
      ↓
Runtime Quest System Ready
```

This establishes quest progression as framework infrastructure rather than requiring individual maps, NPCs, or gameplay systems to implement their own quest state management.

---

# ✅ Design Rule

**QuestManager is the central authority for quest runtime state and progression.**

`QuestDefinition` defines **what a quest is**.

`QuestInstance` stores **what the player has currently done**.

`QuestBehaviorMatrix` defines **how the quest category behaves**.

Gameplay systems provide **events**.

The Quest Manager routes those events into active quest instances and coordinates the resulting lifecycle:

```text
Definition
    ↓
Runtime Instance
    ↓
Gameplay Events
    ↓
Objective Progress
    ↓
Quest Completion / Failure
    ↓
Rewards
    ↓
Persistence
```

Future quest functionality should extend this centralized, data-driven pipeline rather than embedding quest progression logic directly into individual gameplay systems, NPCs, maps, or UI.
