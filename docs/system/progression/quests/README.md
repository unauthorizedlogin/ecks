# 🎯 Quest System

The Quest System provides the centralized framework for defining, managing, and progressing quests within the RPG framework.

Quests are represented through data-driven `QuestDefinition` resources, allowing objectives, progression states, rewards, dialogue, and achievement content to share a common foundation.

The system provides:

* Quest definitions and classification
* Quest objectives and progression
* Quest runtime state
* Quest availability and requirements
* Quest rewards
* Quest dialogue integration
* NPC quest interactions
* Achievement integration
* Quest UI integration

---

# 🧠 Quest Architecture

```text
                       Quest System

                            |
                     QuestDefinition
                            |
          ┌─────────────────┼─────────────────┐
          ↓                 ↓                 ↓
      Objectives         Dialogue          Rewards
          |                 |                 |
          ↓                 ↓                 ↓
    QuestInstance    Dialogue System    Item Database
          |
          ↓
     QuestManager
          |
     ┌────┴─────┐
     ↓          ↓
  Quest UI   Achievement
```

`QuestDefinition` defines quest content.

`QuestManager` owns quest runtime state and progression.

Supporting systems consume quest data for dialogue, UI, rewards, and achievements.

---

# 🔗 Quest System Documentation

The following documents cover the Quest System components.

| SystemPurposeDocumentation |                                                                                 |                         |
| -------------------------- | ------------------------------------------------------------------------------- | ----------------------- |
| 🧠 Quest Manager           | Manages quest runtime state, progression, acceptance, and completion            | [Quest Manager](quest_manager.md)   |
| ⭐ Reward Manager           | Processes quest rewards and routes XP, currency, items, and other reward types | [Reward Manager](reward_manager.md)   |
| 🎯 Quest Resource          | Defines quest identity, objectives, requirements, rewards, and progression data | [Quest Resource](resource_quest.md) |
| 🎯 Objective Resource      | Defines objective types, targets, progress requirements, & completion data      | [Objective Resource](resource_quest_obj_def.md) |
| 🔑 Quest Requirement      | Defines prerequisites that must be satisfied before a quest can be started       | [Quest Requirement](resource_quest_requirements.md) |
| 🎁 Quest Reward            | Defines reward type, reward identity, display information, and reward amount    | [Quest Reward](resource_quest_reward.md) |
| 📋 Quest Instance          | Tracks runtime objective and quest progression state                            | [Quest Instance](quest_instance.md) |
| 🧩 Quest Behavior Matrix   | Defines default runtime behavior rules for each quest category                  | [Quest Behavior Matrix](quest_behavior_matrix.md) |
| 📜 Quest Menu              | Journal for quest visibility, selection, tracking, cancellation, & display      | [Quest Menu](quest_menu.md) |

---

# 🔗 System Integration

The Quest System integrates with:

* 💬 Dialogue System
* 👤 NPC System
* 🖥️ UI System
* 🎁 Item System
* 🏆 Achievement System
* 💾 Save System
* 🌍 World System

The Quest System owns quest definitions and runtime progression while integrated systems handle their respective presentation and gameplay responsibilities.

---

# 📌 Design Rule

**`QuestDefinition` defines the quest.**

**`QuestManager` owns quest runtime state.**

**`QuestInstance` tracks active quest progression.**

The Quest System acts as the authority for quest availability, progression, objectives, and completion while exposing quest state to dialogue, UI, rewards, achievements, and other gameplay systems.
