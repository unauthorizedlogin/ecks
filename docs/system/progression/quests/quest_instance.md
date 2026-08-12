# 🎯 Quest Instance

## Overview

`QuestInstance` is the runtime state container for a single quest.

It connects a static `QuestDefinition` to live objective progress, quest status, timers, completion, and save/load state.

---

# Architecture

```text
QuestDefinition
      │
      ▼
QuestInstance
      │
      ├── Quest Status
      ├── Objective States
      ├── Timer State
      │
      ├── Progress Events
      │       ▼
      │   Objective Updates
      │
      └── Serialization
              ▼
          Save / Load
```

---

# Quest States

| State               | Purpose                                         |
| ------------------- | ----------------------------------------------- |
| `NOT_STARTED`       | Quest has not begun                             |
| `IN_PROGRESS`       | Quest is actively progressing                   |
| `READY_FOR_TURN_IN` | Objectives are complete and turn-in is required |
| `COMPLETED`         | Quest has been completed                        |
| `FAILED`            | Quest has failed                                |

---

# Runtime Responsibilities

`QuestInstance` handles:

* Starting and resetting quests
* Tracking objective progress
* Processing quest events
* Objective dependency gates
* Timed quest countdowns
* Turn-in requirements
* Quest completion/failure
* Runtime state serialization
* Runtime state restoration

It contains no UI responsibilities.

---

# Objective Flow

```text
Gameplay Event
      │
      ▼
update_progress()
      │
      ▼
_is_objective_unlocked()
      │
      ▼
_apply_objective()
      │
      ▼
ObjectiveState
      │
      ▼
_all_completed()
      │
      ├── No Turn-In ──► COMPLETED
      │
      └── Turn-In ─────► READY_FOR_TURN_IN
```

`TURN_IN` objectives are resolved against the current item count through `ItemCountManager`.

---

# Objective Gates

Objectives can depend on other objectives:

```text
Objective A
    │
    ▼
Objective B
    │
    ▼
Objective C
```

`objective_gate` prevents an objective from progressing until its required objective states are completed.

---

# Serialization

Runtime quest state is stored as:

```text
QuestInstance
│
├── quest_id
├── status
├── objective_states
│   ├── current_amount
│   └── completed
└── time_remaining
```

This allows quest progress to be reconstructed from saved game data without storing the entire `QuestDefinition`.

---

# Runtime Boundary

```text
QuestDefinition
   Static Data
       │
       ▼
QuestInstance
   Runtime State
       │
       ├── QuestManager
       ├── Event Processing
       └── Save Manager
```

`QuestDefinition` defines the quest.

`QuestInstance` tracks what is currently happening with that quest.
