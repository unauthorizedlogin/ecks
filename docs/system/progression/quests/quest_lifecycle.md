# 🔄 Quest Lifecycle Architecture

### Purpose

`QuestLifecycle` manages the lifecycle transitions of quests. It creates and activates quest instances, handles completion and rewards, supports resets, cancellation, and failure, and coordinates the associated Quest Manager callbacks and quest-line progression.

### Architecture Role

```text id="q2n7vf"
QuestManager
    ↓
QuestLifecycle
    ├── QuestDatabase
    ├── QuestInstance
    ├── RewardManager
    ├── EventManager
    └── Quest Manager Callbacks
```

`QuestLifecycle` operates as a `RefCounted` service and uses references to the Quest Manager's shared quest state rather than owning that state independently.

### Responsibilities

* Start valid quests.
* Create and initialize `QuestInstance` objects.
* Complete active quests.
* Grant quest rewards.
* Notify other active quests when a quest completes.
* Reset eligible quests.
* Cancel active quests.
* Fail quests.
* Maintain completed and failed quest collections.
* Handle automatic quest tracking.
* Trigger quest lifecycle callbacks.
* Advance quest lines after completion.
* Generate quest and achievement event logs.

### Quest Start

`start_quest()` retrieves the quest definition from `QuestDatabase` and validates whether the quest can begin.

Validation prevents:

* Missing quest definitions.
* Duplicate active quests.
* Starting non-repeatable completed quests.
* Invalid timed quests.
* Failed requirement validation.

A new `QuestInstance` is created and started before being added to `active_quests`.

Starting a quest also:

* Sends a `QUEST_START` event through `QuestManager`.
* Synchronizes the player's current level snapshot.
* Applies behavior configuration.
* Automatically tracks the quest when configured.
* Emits the quest-started callback.
* Supports deferred automatic completion.

### Quest Completion

`complete_quest()` transitions an active `QuestInstance` to completed state.

The completion pipeline:

```text id="4v2m81"
Active Quest
    ↓
QuestInstance.complete()
    ↓
Notify Other Active Quests
    ↓
Refresh Objectives
    ↓
Record Completion
    ↓
Remove From Active Quests
    ↓
Grant Rewards
    ↓
Emit Quest/Achievement Log
    ↓
Lifecycle Callbacks
    ↓
Advance Quest Line
```

Other active quests receive a `QUEST_COMPLETE` event, allowing quest chains and dependent objectives to respond to completed quests.

Achievement-category quests use the dedicated achievement event channel and messaging.

### Quest Reset

`reset_quest()` supports replayable/resettable quest behavior.

The definition is retrieved and its behavior configuration checked before resetting.

For an active quest, its instance progress is reset.

For a completed quest, its ID is removed from `completed_quests`, allowing it to become startable again.

### Quest Cancellation

`cancel_quest()` removes an active quest and records it in `failed_quests`.

It also clears:

* Story tracking when the canceled quest is currently story-tracked.
* General tracked-quest membership.

The operation generates an abandonment log and emits the cancellation callback.

### Quest Failure

`fail_quest()` builds on the cancellation pathway.

```text id="k8w2rp"
fail_quest()
    ↓
cancel_quest()
    ↓
Failure Log
    ↓
quest_failed Callback
```

This preserves the same active-state cleanup behavior while providing a distinct failure lifecycle notification.

### Lifecycle Callbacks

The service receives manager-owned callbacks during `setup()` for:

* Behavior retrieval
* Quest selection
* Quest started
* Quest updated
* Quest completed
* Quest failed
* Quest canceled
* Quest-line advancement

This keeps signal emission, selection behavior, and higher-level manager responsibilities outside the lifecycle service.

### Start Validation

`_can_start_quest()` centralizes quest-start eligibility.

Checks include:

* Valid definition
* Not already active
* Repeatability rules
* Timed quest configuration
* Quest requirements

This prevents lifecycle operations from creating invalid quest instances.

### File(s) Affected

* `quest_lifecycle.gd`
* `QuestManager`
* `QuestInstance`
* `QuestDefinition`
* `QuestDatabase`
* `RewardManager`
* `RequirementsManager`
* `EventManager`

### Notes

`QuestLifecycle` is the **state-transition layer** of the Quest architecture. It does not own quest definitions or independently manage the Quest Manager's collections; instead, it operates against shared state and communicates upward through callbacks.

Together with `QuestEventProcessor`, this separates two major Quest Manager responsibilities:

```text id="3e9x6p"
QuestManager
├── QuestLifecycle
│   └── State transitions
│
└── QuestEventProcessor
    └── Gameplay event → objective progress
```
