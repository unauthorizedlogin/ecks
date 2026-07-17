# 📜 Quest Database Architecture

## Overview

The Quest Database provides the centralized runtime authority for all quest definitions within the engine.

It manages the static quest content layer by loading and exposing `QuestDefinition` resources to gameplay systems.

The Quest Database is responsible for **quest data availability**, while runtime quest progression, tracking, completion, rewards, and player state are handled by the Quest Runtime systems.

The architecture separates:

1. **Quest Database API**
2. **Quest Resource Database**
3. **Quest Resource Index**

This follows the engine-wide database architecture pattern by separating gameplay access, resource loading, and generated content indexing.

---

# 🏗️ Architecture Structure

```text
QuestDatabase (Autoload)
        |
        |
        v
QuestResourceDatabase
        |
        |
        v
Generated Quest Index
        |
        |
        v
QuestDefinition (.tres)
```

---

# 🧠 QuestDatabase

## Responsibilities

`al_quest_database.gd` is the gameplay-facing quest definition interface.

It is responsible for:

* Initializing quest resources
* Providing quest lookup access
* Exposing all available quests
* Providing quest identifiers

It does **not**:

* Manage active quests
* Track player progress
* Handle objective completion
* Grant rewards
* Control quest states
* Handle dialogue integration
* Manage quest UI

Those responsibilities belong to the Quest Runtime architecture.

---

# 🚀 Initialization Pipeline

When initialized:

1. QuestDatabase starts loading.
2. QuestResourceDatabase scans the quest database directory.
3. Quest resources are loaded.
4. Quest definitions are registered into runtime memory.
5. Gameplay systems can query quest data.

Example:

```text
QuestDatabase.initialize()

        ↓

QuestResourceDatabase.load_directory()

        ↓

Quest resource discovery

        ↓

QuestDefinition registration

        ↓

Runtime quest lookup available
```

---

# 📥 Quest Lookup API

## Single Quest Access

```gdscript
get_quest(id)
```

Returns a `QuestDefinition` resource.

Used by systems requiring static quest information:

* Quest Manager
* Dialogue Systems
* Quest Menus
* Reward Systems
* Quest Chains

Example:

```gdscript
QuestDatabase.get_quest("quest_help_blacksmith")
```

---

## Get All Quest IDs

```gdscript
get_all_ids()
```

Returns all registered quest identifiers.

Used for:

* Debug tools
* Quest browsers
* Validation systems
* Content inspection

---

## Get All Quests

```gdscript
get_all_quests()
```

Returns all loaded `QuestDefinition` resources.

Used for:

* Quest catalogs
* Developer tools
* Testing utilities

---

# 🗂️ Quest Resource Database

## Responsibilities

`quest_resource_database.gd` handles the internal loading and caching layer.

It manages:

* Recursive directory scanning
* Quest resource loading
* Quest registration
* Duplicate protection
* Runtime lookup

---

# 📂 Quest Registry

## quest_map

All quests are stored by their unique quest ID.

Example:

```text
quest_map

quest_starting
        |
        v
QuestDefinition


quest_help_blacksmith
        |
        v
QuestDefinition
```

This provides fast direct lookup.

---

# 🔍 Resource Loading

The resource database performs recursive scanning:

```text
quests/

 ├── story/
 │     └── quest_starting.tres
 │
 ├── side/
 │     └── quest_help_blacksmith.tres
 │
 ├── faction/
 │     └── quest_faction_jackson.tres
 │
 └── daily/
       └── quest_daily_poop.tres
```

All valid `.tres` resources are discovered automatically.

---

# 📦 Resource Registration

Each quest is validated before entering the database.

Validation includes:

---

## Quest Type Validation

Only valid:

```gdscript
QuestDefinition
```

resources are registered.

Invalid resources are ignored.

---

## Missing ID Protection

Every quest requires:

```gdscript
quest.id
```

Resources without IDs are rejected.

Example:

```text
❌ Quest missing ID
```

---

## Duplicate Protection

Duplicate quest IDs are prevented:

```text
❌ Duplicate quest id: quest_starting
```

This guarantees every quest has a unique runtime identifier.

---

# 🗂️ Generated Quest Index

## quest_index.gd

The quest index contains generated paths to quest resources.

Example:

```gdscript
const QUEST_PATHS := [

"res://data/databases/quests/story/quest_starting.tres",

"res://data/databases/quests/side/quest_help_blacksmith.tres"

]
```

---

# ⚙️ Generator Workflow

Quest resources follow a generator-driven workflow:

```text
QuestDefinition Created

        ↓

Quest Index Generator

        ↓

quest_index.gd

        ↓

QuestDatabase Startup

        ↓

Quest Runtime Availability
```

---

# 📜 Quest Organization

The database supports organizational folders for different quest categories:

## Story Quests

Main progression content.

Examples:

* Main storyline
* Campaign progression
* Narrative quests

---

## Side Quests

Optional world content.

Examples:

* NPC requests
* Exploration objectives
* Optional rewards

---

## Faction Quests

Faction-related progression.

Examples:

* Reputation systems
* Organization objectives

---

## Daily Quests

Repeatable content.

Examples:

* Daily objectives
* Rotating activities

---

## Chained Quests

Multi-stage quest lines.

Examples:

```text
Quest A
   ↓
Quest B
   ↓
Quest C
```

---

# 🔧 Expansion Support

Adding new quests requires:

1. Creating a new `QuestDefinition`
2. Assigning a unique quest ID
3. Placing it inside the quest database structure
4. Running the quest index generator

No database code changes are required.

---

# 🔗 Runtime Separation

The Quest Database provides **definition authority**.

The Quest Runtime provides **player state authority**.

Example:

```text
QuestDatabase

"What is this quest?"

        ↓

QuestDefinition


QuestManager

"What is the player's progress?"

        ↓

QuestInstance
```

This separation allows:

* Multiple players/saves
* Persistent quest state
* Dynamic quest tracking
* Quest chains
* Objective systems

without modifying static quest content.

---

# ✅ Design Benefits

The Quest Database provides:

* Centralized quest authority
* Fast quest lookup
* Automatic content discovery
* Duplicate protection
* Generator-driven scalability
* Separation of static and runtime data
* Support for large quest libraries

This architecture creates a scalable foundation for ARPG quest systems with story progression, side content, faction systems, daily activities, and chained questlines.
