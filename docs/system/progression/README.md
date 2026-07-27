# 📖 Progression System

## Overview

The Progression System provides the framework responsible for player progression through dialogue, quests, and narrative-driven gameplay.

The system is designed around a data-driven progression architecture, separating:

- NPC conversations
- Quest progression
- Dialogue playback
- Objective tracking
- Player progression state
- Runtime progression management

Dialogue and quests remain independent systems while integrating through shared runtime managers and data resources.

---

# 🧠 System Architecture

The Progression System is organized into dedicated systems responsible for narrative interaction and player progression.

```text
                    NPC Interaction
                           |
                           ↓
                  Progression System
                           |
          ┌────────────────┴────────────────┐
          ↓                                 ↓

   Dialogue System                  Quest System
          |                                 |
          ↓                                 ↓

 Dialogue Runtime                 Quest Manager
 Dialogue Resources               Quest Resources
 Quest UI Adapter                 Objective System
 Quest Offer Controller
````

Each subsystem maintains its own runtime authority while communicating through shared progression events and data resources.

---

# 🔗 Progression System Documentation

The following systems make up the Progression layer.

| System | Purpose | Documentation |
|---|---|---|
| 🎭 Dialogue System | NPC conversations, dialogue playback, and narrative interactions | [Dialogue System](./progression/dialogue_system.md) |
| 📜 Quest System    | Quest progression, objectives, rewards, and lifecycle management | [Quest System](./progression/quest_system.md)       |

---

# Summary

The Progression System provides a unified narrative framework through modular dialogue and quest systems.

Each subsystem manages its own gameplay responsibilities while integrating through shared resources, runtime managers, and progression events.

```
