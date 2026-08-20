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

- ✅ Detects resources
- ✅ Generates index entries
- ✅ Loads database entries
- ✅ Makes resources available at runtime

---

# 🔗 Database Implementations

The framework uses the shared Resource Database Architecture across all content systems.

Each database provides centralized runtime access to its resource type through generated indexes, registry loading, and database autoloaders.

Implemented databases:

| Database | Purpose | Documentation |
|---|---|---|
| ⚡ Ability Database | Ability definitions | [Ability Database](./ability_database.md) |
| 📷 Camera Database | Camera profiles and camera configurations | [Camera Database](./camera_database.md) |
| 🧬 Class Database | Entity classes and progression data | [Class Database](./class_database.md) |
| 💬 Dialogue Database | Dialogue resources and conversations | [Dialogue Database](./dialogue_database.md) |
| ⚖️ Difficulty Database | Gameplay difficulty configurations | [Difficulty Database](./difficulty_database.md) |
| ✨ Effect Database | Gameplay effects and effect definitions | [Effect Database](./effect_database.md) |
| 📦 Item Database | Items, equipment, and gameplay objects | [Item Database](./item_database.md) |
| 🗺️ Map Database | World maps and level resources | [Map Database](./map_database.md) |
| 👤 NPC Database | NPC identity and references | [NPC Database](./npc_database.md) |
| 📜 Quest Database | Quest definitions and objectives | [Quest Database](./quest_database.md) |
| 🧮 Stat Formula Database | Stat calculations and conversions | [Stat Formula Database](./stat_formula_database.md) |

# Summary

The Resource Database Architecture provides a unified system for managing framework data through generated indexing, registry-based loading, and runtime-safe access.

All databases follow the same pipeline, allowing content systems to scale without manual registration or runtime filesystem scanning.

---
