# 📖 Progression System

## Overview

The Progression System provides the framework responsible for player progression through dialogue, quests, and narrative-driven gameplay.

The system is designed around a **data-driven progression architecture**, separating:

- NPC conversations
- Quest progression
- Dialogue playback
- Objective tracking
- Player progression state
- Runtime progression management

Dialogue and quests remain independent systems while integrating through shared runtime managers and data resources.

---

# 🧠 System Architecture

The Progression System is organized into two primary systems:

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
```

Both systems own their own runtime state while communicating through shared progression events.

---

## 🎭 Dialogue System

The Dialogue System manages conversations between the player and NPCs.

Responsibilities include:

- NPC interaction
- Dialogue playback
- Conversation state
- Dynamic dialogue options
- Quest dialogue integration
- Shop dialogue integration

Dialogue content is authored through data-driven resources, allowing conversations to respond dynamically to player progression.

---

Documentation:

📄 [Dialogue System Documentation](progression/dialogue_system.md)

---

## 📜 Quest System

The Quest System manages player objectives, progression, and rewards.

Responsibilities include:

- Quest lifecycle management
- Objective tracking
- Quest state progression
- Reward distribution
- Quest persistence
- Dialogue integration

The Quest System acts as the authoritative source for quest progression while remaining independent from dialogue presentation.

---

Documentation:

📄 [Quest System Documentation](progression/quest_system.md)
