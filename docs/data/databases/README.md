# Resource Database Architecture

## Overview

The Databases provides centralized runtime access to dialogue resources through the engine's standard database architecture.

Like all framework databases, it uses a three-layer pipeline:

1. **Autoload Database Manager**
   - Runtime-facing API
   - Handles initialization
   - Provides gameplay access

2. **Registry Scanner / Resource Database**
   - Loads indexed resources
   - Validates entries
   - Maintains runtime lookup maps

3. **Generated Index File**
   - Created automatically by the generator system
   - Provides deterministic resource paths
   - Removes runtime filesystem scanning

This architecture provides fast, scalable resource access while maintaining a creator-friendly workflow.

---

# 🏗️ Architecture

```text
Database System
│
├── Autoload Database Manager
│
├── Registry Scanner
│
└── Generated Index
````

---

# 🚀 Database Managers

## *Database

The database autoloaders are the primary gameplay access layer.

Responsibilities:

* Initialize database resources
* Load registry data
* Provide lookup functions
* Expose validated resources to gameplay systems

---

# 📚 Registry Layer

## *ResourceDatabase

The registry manages loaded resources after initialization.

Responsibilities:

* Resource registration
* ID validation
* Duplicate detection
* Runtime lookup storage

Resources are stored using dictionary-based lookup for fast access.

Example:

```text
Resource ID
      |
      ↓
Resource Instance
```

---

# 🏭 Generated Index

## *_index.gd

The index file is automatically generated.

It provides:

* Resource paths
* Export-safe references
* Deterministic loading

Creators never manually edit generated index files.

---

# 🔄 Loading Pipeline

```text
Resource Created
        |
        ↓
Generator Scanner
        |
        ↓
Generated Index
        |
        ↓
Registry Database
        |
        ↓
Runtime Lookup
```

---

# 🛠️ Creator Workflow

Creating database content:

1. Create resource
2. Assign unique ID
3. Place resource in database folder
4. Run generator

The framework automatically:

✅ Detects resources
✅ Generates index entries
✅ Loads database entries
✅ Makes resources available at runtime

---

# 🔗 System Integration

The Dialogue Database provides resources for:

* NPC systems
* Quest systems
* Dialogue runtime
* Localization
* Event systems

---

# Summary

The Dialogue Database provides a scalable resource management layer built around generated indexing, registry-based loading, and runtime-safe access.

The same database architecture is shared across the framework, allowing every content system to scale without manual registration or runtime filesystem scanning.

---

# 💬 Database Contents

The Dialogue Database manages all dialogue-related resources used throughout the framework.

Supported dialogue content includes:

- Quest dialogue
- Flavor dialogue
- NPC conversations
- Story interactions
- Optional world dialogue

Resources are organized by unique dialogue identifiers and exposed through the database API.

Example:

```text
Dialogue ID
      |
      ↓
Dialogue Resource
      |
      ↓
Dialogue Runtime
```

That keeps every database README consistent and avoids having 15 copies of the same architecture explanation.

**Documentation:**

- [Dialogue Database](./docs/data/databases/dialogue_database.md)


---

# ⚖️ Difficulty Contents

The Difficulty Database manages all difficulty-related resources used throughout the framework.

Manages:

- Difficulty definitions
- Gameplay difficulty configurations
- Player difficulty modifiers
- Enemy difficulty modifiers
- Custom challenge configurations

Runtime access:

```gdscript
DifficultyDatabase.get_difficulty(difficulty_id)
````

Resources are organized by difficulty identifiers and effect categories.

Example:

```text
Difficulty ID
      |
      ↓
Difficulty Definition
      |
      ↓
Difficulty Effects
      |
      ↓
Gameplay Systems
```

**Documentation:**

- [Difficulty Database](./docs/data/databases/difficulty_database.md)

---

# 🧬 Class Contents

The Class Database manages all class-related resources used throughout the framework.

Manages:

- Player class definitions
- Enemy class definitions
- NPC class definitions
- Class rules
- Level progression data
- Growth curve resources

Runtime access:

```gdscript
ClassDatabase.get_stats(class_id)
````

Resources are organized by entity type and class identifier.

Example:

```text
Class ID
      |
      ↓
Class Definition
      |
      ↓
StatBlock / Rules / Progression
      |
      ↓
Entity Systems
```

**Documentation:**

- [Class Database](./docs/data/databases/class_database.md)

---

# 👤 Database Contents

The NPC Database manages all NPC identity resources used throughout the framework.

Manages:

- NPC definitions
- NPC display information
- Portrait references
- NPC types
- Dialogue references
- Shop references
- Class references

Runtime access:

```gdscript
NPCDatabase.get_npc(npc_id)
````

NPC resources define identity while specialized systems handle NPC behavior.

Example:

```text
NPC ID
   |
   ↓
NPCData Resource
   |
   ├── Dialogue Reference
   ├── Shop Reference
   ├── Class Reference
   └── Identity Data
   |
   ↓
NPC Systems
```

**Documentation:**

* [NPC Database](./docs/data/databases/npc_database.md)

---

# 📦 Item Database Contents

The Item Database manages all item resources used throughout the framework through a centralized runtime lookup system.

Manages:

- Equipment resources
- Weapons
- Armor
- Accessories
- Potions
- Ammunition
- Currency
- Quest items
- Tools
- Gems
- Unique items
- Expandable item categories

Runtime access:

```gdscript
ItemDatabase.get_item_data(item_id)
````

The database provides a unified item pipeline for inventory systems, shops, loot systems, rewards, crafting, and future item-based gameplay features.

Example:

```text
Item ID
   |
   ↓
ItemResource
   |
   ├── Category
   ├── Subcategory
   ├── Stats
   ├── Effects
   ├── Requirements
   └── Metadata
   |
   ↓
Gameplay Systems
```

**Documentation:**

* [Item Database](./docs/data/databases/item_database.md)

---

# 🗺️ Map Database Contents

The Map Database manages all world map resources used throughout the framework through a centralized runtime lookup system.

Manages:

- World maps
- Regions
- Dungeon maps
- Overworld areas
- Injectable maps
- Future expandable world content

Runtime access:

```gdscript
MapDatabase.get_level_data(map_id)
````

The database provides a unified map authority for world navigation, scene resolution, save systems, persistence, and future world streaming features.

Example:

```text
Map ID
   |
   ↓
MapResource
   |
   ├── Scene Reference
   ├── World Group
   ├── Region Group
   ├── Map Metadata
   └── Runtime Information
   |
   ↓
World Systems
```

**Documentation:**

* [Map Database](./docs/data/databases/map_database.md)

---

