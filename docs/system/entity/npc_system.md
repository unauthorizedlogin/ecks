# 👤 NPC System

The NPC System provides the centralized identity and runtime data layer for all non-player characters within the RPG framework.

NPCs are represented through data-driven `NPCData` resources, allowing characters to be referenced consistently across gameplay systems such as:

* Dialogue
* Quests
* Shops
* Future reputation/faction systems
* World interactions

The system separates **NPC identity data** from the actual NPC scene behavior, allowing NPCs to remain flexible and expandable as more gameplay systems are added.

---

## ⚠️ Current Development Status

The NPC System is currently an active foundation system.

Current functionality focuses on:

* ✅ NPC identity management
* ✅ Runtime NPC database loading
* ✅ NPC data lookup
* ✅ Quest system integration
* ✅ Shop system integration
* 🔄 Expanding NPC behaviors and world simulation features

The current system provides the core data layer required for NPC-driven interactions, while advanced NPC features such as schedules, factions, relationships, AI routines, and dynamic world behaviors are planned for future expansion.

---

# 🧠 NPC Database

The `NPCDatabase` acts as the central runtime access point for NPC definitions.

Responsibilities:

* Initialize NPC resource data
* Load NPC definitions into memory
* Provide gameplay-facing queries
* Expose runtime NPC information

The database does **not**:

* Scan directories directly
* Handle dialogue logic
* Handle shop behavior
* Control NPC movement
* Manage NPC AI

Those responsibilities remain owned by their respective systems.

---

# 📦 Data-Driven NPC Resources

Each NPC is defined through an `NPCData` resource.

Example:

```gdscript
class_name NPCData

@export var npc_id: String
@export var display_name: String
@export var title: String
@export var portrait: String
@export var type: String
@export var dialogue_id: String
@export var shop_id: String
```

This provides a centralized identity profile for every NPC.

---

# 👤 NPC Identity

NPC resources define the information needed to identify and present characters:

### 🏷️ Identity

```gdscript
npc_id
display_name
title
```

Used for:

* Quest targets
* Dialogue references
* UI display
* World interactions

---

### 🖼️ Presentation

```gdscript
portrait
type
```

Supports:

* Dialogue portraits
* NPC categories
* Future classification systems

---

### 💬 Dialogue Integration

```gdscript
dialogue_id
```

Connects NPCs to the dialogue framework.

Allows NPC scenes to remain independent from their conversation data.

---

### 🏪 Shop Integration

```gdscript
shop_id
```

Connects NPCs to available shop definitions.

NPCs can expose merchant functionality without hardcoding shop logic into the character scene.

---

# 🔎 Runtime Queries

The NPC Database provides gameplay-facing lookup methods.

### Get NPC

```gdscript
NPCDatabase.get_npc("blacksmith_npc")
```

Returns the complete NPC definition.

---

### Check NPC Exists

```gdscript
NPCDatabase.has_npc("merchant_npc")
```

Used for validation before requesting NPC data.

---

### Retrieve NPC Collections

Supports:

```gdscript
get_all_npc_ids()
get_all_npcs()
```

Useful for:

* Debug tools
* Editors
* Future NPC management systems

---

# 🔗 System Integrations

## 📜 Quest System

NPC identities are used as authoritative references for quest objectives.

Examples:

* TALK objectives
* Quest givers
* Turn-in NPCs
* Story characters

Quest data references the NPC ID rather than a scene instance, keeping quests independent from world placement.

---

## 💬 Dialogue System

NPCData provides the bridge between world characters and dialogue content.

Flow:

```
NPC Scene
    ↓
npc_id
    ↓
NPCDatabase
    ↓
NPCData
    ↓
DialogueManager
    ↓
Conversation
```

---

## 🏪 Shop System

NPCs can expose merchant functionality through their assigned shop ID.

Flow:

```
NPC
 ↓
NPCData.shop_id
 ↓
Shop Database
 ↓
Shop Menu
 ↓
Trading
```

---

# 🏗️ Current Architecture

```
NPC Scene
    |
    | npc_id
    ↓
NPCDatabase
    |
    ↓
NPCData Resource
    |
    ├── Dialogue
    ├── Shops
    ├── Quests
    └── Future Systems
```

---

# Current Implementation Summary

✅ Data-driven NPC resource system
✅ Central NPC database singleton
✅ Runtime NPC lookup
✅ Identity separation from character scenes
✅ Quest system compatibility
✅ Dialogue system compatibility
✅ Shop system compatibility

🔄 Future Expansion:

* NPC schedules
* Faction relationships
* Reputation
* AI behaviors
* NPC state persistence
* Dynamic world reactions
* Procedural NPC generation

The NPC System establishes the foundation for scalable character-driven gameplay, allowing NPCs to become persistent, data-driven entities that multiple RPG systems can reference without tightly coupling them together.
