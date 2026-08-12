# 📜 Quest Definition

## Overview

The `QuestDefinition` is the central data resource for defining quests.

It stores quest identity, classification, objectives, rewards, requirements, behavior rules, dialogue integration, system triggers, and progression data.

---

# Resource Structure

```text
QuestDefinition
│
├── Identity
│   ├── quest_id
│   ├── display_name
│   ├── icon
│   └── description
│
├── Configuration
│   ├── quest_category
│   ├── sort_order
│   ├── quest_line_id
│   ├── quest_index
│   ├── objective_defs[]
│   └── rewards[]
│
├── Behaviors
│   ├── repeatable
│   ├── timed
│   ├── time_limit
│   └── fail_on_death
│
├── Requirements
│   └── requirements[]
│
├── Dialogue Integration
│   ├── dialogue_keys
│   └── state prefixes
│
├── System Events
│   ├── hidden
│   ├── auto_start
│   └── auto_complete
│
└── Progression
    ├── progression_domain
    ├── progression_id
    ├── progression_xp
    └── progression_rep
```

---

# Quest Categories

| Category      | Purpose                       |
| ------------- | ----------------------------- |
| `ACHIEVEMENT` | Achievement-based progression |
| `CHAINED`     | Linked multi-step quests      |
| `DAILY`       | Repeatable daily content      |
| `FACTION`     | Reputation-based quests       |
| `SIDE`        | Optional content              |
| `STORY`       | Main narrative content        |
| `NONE`        | System or hidden content      |

---

# Quest Composition

```text
QuestDefinition
│
├── ObjectiveDefinition[]
├── QuestReward[]
└── QuestRequirement[]
```

The quest definition provides the configuration while the Quest System handles runtime progress, completion, requirements, and rewards.

---

# Runtime Behaviors

Quests can be configured for:

* Repeatable execution
* Timed objectives
* Death-based failure
* Automatic starting
* Automatic completion
* Hidden/system content

---

# Dialogue Integration

Quest definitions provide dialogue identifiers and state-specific button prefixes.

```text
Quest State
    │
    ├── QUEST_START  → quest_prefix
    ├── QUEST_ACTIVE → active_prefix
    └── QUEST_TURNIN → turn_in_prefix
```

`get_quest_button_label()` combines the appropriate localized prefix with the localized quest name.

---

# Progression Integration

Quests can optionally contribute to a progression domain through:

* `progression_domain`
* `progression_id`
* `progression_xp`
* `progression_rep`

This allows quest definitions to feed external progression systems without owning their runtime logic.
