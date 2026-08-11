# 👤 NPC System

The NPC System provides the centralized identity and data layer for non-player characters within the RPG framework.

NPCs are defined through data-driven `NPCData` resources and referenced by `npc_id`, allowing multiple gameplay systems to interact with the same NPC without coupling directly to its scene or runtime behavior.

The system currently provides the foundation for:

* NPC identity
* Dialogue references
* Quest references
* Shop references
* NPC classification
* Future NPC gameplay systems

---

# 🧠 NPC Architecture

```text
                       NPC System

                            |
                       NPCDatabase
                            |
                         NPCData
                            |
          ┌─────────────────┼─────────────────┐
          ↓                 ↓                 ↓
      Dialogue           Quests            Shops
          |                 |                 |
          └─────────────────┼─────────────────┘
                            ↓
                    Future NPC Systems
```

`NPCData` defines **who the NPC is and what systems they connect to**.

The individual gameplay systems own their respective behavior.

---

# 📦 NPC Data

`NPCData` provides the shared identity layer for NPCs.

Core data includes:

* NPC ID
* Display name
* Title
* Portrait
* NPC type
* Dialogue ID
* Shop ID

This keeps NPC identity separate from the scene used to represent the character in the world.

---

# 🔗 NPC System Documentation

| System           | Purpose                                                  | Documentation                   |
| ---------------- | -------------------------------------------------------- | ------------------------------- |
| 👤 NPC Data      | Defines NPC identity and system references               | [NPC Data](npc_data.md)         |
| 🗄️ NPC Database | Provides centralized NPC registration and runtime lookup | [NPC Database](npc_database.md) |

Additional NPC-specific documentation can be added as the system expands.

---

# 🔗 System Integration

The NPC System currently provides integration points for:

| System       | NPC Integration                       |
| ------------ | ------------------------------------- |
| 💬 Dialogue  | Conversation and dialogue identity    |
| 📜 Quests    | Quest givers, targets, and turn-ins   |
| 🏪 Shops     | Merchant and shop references          |
| 🔨 Crafting  | Future crafting interaction           |
| 🎓 Training  | Future trainer interaction            |
| 💼 Jobs      | Future profession/job interaction     |
| ⭐ Reputation | Future faction and reputation systems |

NPCs therefore act as a **shared identity hub** for character-driven gameplay systems.

---

# 🏗️ Data Flow

```text
NPC Scene
    ↓
npc_id
    ↓
NPCDatabase
    ↓
NPCData
    ↓
Gameplay System
    ↓
Dialogue / Quest / Shop / Crafting / Training / Jobs
```

Gameplay systems reference the NPC through its data identity rather than depending on a specific world scene or NPC implementation.

---

# 🚀 Future Expansion

The NPC System is designed to support additional character-driven systems without changing the core identity architecture.

Potential integrations include:

* 🏪 Shops
* 🔨 Crafting
* 🎓 Training
* 💼 Jobs
* ⭐ Reputation
* 🏛️ Factions
* 🗓️ Schedules
* 🤖 NPC Behaviors
* ❤️ Relationships
* 🌍 Dynamic World Interactions
* 💾 Persistent NPC State

---

# 📌 Design Rule

**NPCData defines the NPC. Gameplay systems define what the NPC does.**

The NPC System provides the shared identity and connection point, while specialized systems own dialogue, quests, commerce, crafting, training, jobs, and future NPC behaviors.
