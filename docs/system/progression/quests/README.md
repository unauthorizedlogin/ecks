# 🎯 Quest System

The Quest System provides the centralized framework for defining, managing, and progressing quests within the RPG framework.

Quests are represented through data-driven `QuestDefinition` resources, allowing objectives, progression states, rewards, dialogue, and achievement content to share a common foundation.

The system provides:

* Quest definitions and classification
* Quest objectives and progression
* Quest runtime state
* Quest availability and requirements
* Quest lifecycle management
* External gameplay event processing
* Quest tracking and eligibility
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
                            ↓
                     Quest Manager
                      (Public API)
                            |
          ┌─────────────────┼─────────────────┐
          ↓                 ↓                 ↓
 Quest Event Processor  Quest Lifecycle   Quest Tracker
          |                 |                 |
          ↓                 ↓                 ↓
   Gameplay Events     Quest State       Tracking State
                            |
          ┌─────────────────┼─────────────────┐
          ↓                 ↓                 ↓
      Objectives         Dialogue          Rewards
          |                 |                 |
          ↓                 ↓                 ↓
    QuestInstance    Dialogue System    Reward Manager
```

`QuestDefinition` defines quest content.
`QuestManager` provides the public API for the Quest System.
`QuestEventProcessor` processes external gameplay events that advance objectives.
`QuestLifecycle` manages quests from start through completion, failure, or cancellation.
`QuestTracker` manages quest tracking state and eligibility.

---

# 🔗 Quest System Documentation

The following documents cover the Quest System components.

| System                   | Purpose                                                                         | Documentation                                       |
| ------------------------ | ------------------------------------------------------------------------------- | --------------------------------------------------- |
| 🧠 Quest Manager         | Public API for accessing and controlling quest system functionality             | [Quest Manager](quest_manager.md)                   |
| ⚙️ Quest Event Processor | Processes external gameplay events such as pickups, kills, and collections      | [Quest Event Processor](quest_event_processor.md)   |
| 🔄 Quest Lifecycle       | Manages quests from start through completion, failure, or cancellation          | [Quest Lifecycle](quest_lifecycle.md)               |
| 🎯 Quest Tracker         | Manages quest tracking state and tracking eligibility                           | [Quest Tracker](quest_tracker.md)                   |
| ⭐ Reward Manager         | Processes quest rewards and routes XP, currency, items, and other reward types  | [Reward Manager](reward_manager.md)                 |
| 🎯 Quest Resource        | Defines quest identity, objectives, requirements, rewards, and progression data | [Quest Resource](resource_quest.md)                 |
| 🎯 Objective Resource    | Defines objective types, targets, progress requirements, & completion data      | [Objective Resource](resource_quest_obj_def.md)     |
| 🔑 Quest Requirement     | Defines prerequisites that must be satisfied before a quest can be started      | [Quest Requirement](resource_quest_requirements.md) |
| 🎁 Quest Reward          | Defines reward type, reward identity, display information, and reward amount    | [Quest Reward](resource_quest_reward.md)            |
| 📋 Quest Instance        | Tracks runtime objective and quest progression state                            | [Quest Instance](quest_instance.md)                 |
| 🧩 Quest Behavior Matrix | Defines default runtime behavior rules for each quest category                  | [Quest Behavior Matrix](quest_behavior_matrix.md)   |
| 📜 Quest Menu            | Journal for quest visibility, selection, tracking, cancellation, & display      | [Quest Menu](quest_menu.md)                         |

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

The Quest System owns quest definitions and runtime progression while integrated systems provide gameplay events, presentation, rewards, and persistence.

---

# 📌 Design Rule

**`QuestDefinition` defines the quest.**
**`QuestManager` provides the public API.**
**`QuestEventProcessor` processes external gameplay events.**
**`QuestLifecycle` manages quest state transitions.**
**`QuestTracker` manages tracking state and eligibility.**

The Quest System keeps the public interface centralized while delegating event processing, lifecycle management, and tracking responsibilities to dedicated handlers.
