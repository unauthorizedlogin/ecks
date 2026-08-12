# 🎯 Quest Objective Definition

## Overview

`ObjectiveDefinition` defines a single objective within a quest.

It specifies **what the player must do**, **what is being targeted**, and **how much progress is required**.

---

# Resource Structure

```text
ObjectiveDefinition
│
├── Identity
│   └── id
│
├── Objective
│   ├── description
│   ├── type
│   └── completed_description
│
├── Target
│   ├── target_id
│   ├── target_category
│   ├── target_amount
│   └── data
│
├── Quest / Shop
│   ├── shop_id
│   └── turn_in_npc_id
│
└── Gates
    └── objective_gate
```

---

# Objective Types

| Type             | Purpose                 |
| ---------------- | ----------------------- |
| `COLLECT`        | Collect items           |
| `EQUIP`          | Equip an item           |
| `INTERACT`       | Interact with an object |
| `KILL`           | Defeat a target         |
| `LEVEL_UP`       | Gain a level            |
| `OPEN_UI`        | Open a UI               |
| `PURCHASE`       | Purchase from a shop    |
| `QUEST_START`    | Start a quest           |
| `QUEST_COMPLETE` | Complete a quest        |
| `TALK`           | Talk to an NPC          |
| `TURN_IN`        | Turn in a quest         |
| `GAIN_XP`        | Gain XP                 |
| `SPEND_XP`       | Spend XP                |
| `ENTER_AREA`     | Enter an area           |
| `EXIT_AREA`      | Exit an area            |
| `TRAVEL`         | Travel                  |
| `CRAFT`          | Craft an item           |
| `UNEQUIP`        | Unequip an item         |
| `UPGRADE_ITEM`   | Upgrade an item         |
| `USE_SKILL`      | Use a skill             |
| `CUSTOM`         | Custom system event     |

---

# Target & Progress

```text
ObjectiveDefinition
        │
        ├── type
        ├── target_id
        ├── target_category
        └── target_amount
```

* `target_id` identifies the target.
* `target_category` provides additional target classification.
* `target_amount` defines the required progress.
* `data` provides optional system-specific parameters.

---

# Gates & Restrictions

`objective_gate` provides a list of prerequisite objective identifiers that can be used to control objective progression.

`shop_id` optionally restricts purchase objectives to a specific shop.

`turn_in_npc_id` identifies the NPC responsible for quest turn-in objectives.

---

# Runtime Boundary

`ObjectiveDefinition` is a **data definition only**.

```text
ObjectiveDefinition
        │
        ▼
   QuestManager
        │
        ▼
 Event / Progress Tracking
```

The resource defines the objective; `QuestManager` and related systems handle progress, completion, and event processing.
