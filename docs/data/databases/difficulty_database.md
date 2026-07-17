# ⚖️ Difficulty Database Architecture

## Overview

The Difficulty Database provides a centralized, data-driven framework for managing gameplay difficulty configurations and their associated gameplay modifiers.

Like all major engine databases, the system follows the three-layer architecture:

1. **Autoload Database Manager**

   * Runtime-facing difficulty API
   * Handles initialization
   * Controls active difficulty state
   * Applies difficulty effects

2. **Registry Scanner / Resource Database**

   * Loads and validates difficulty resources
   * Maintains runtime lookup dictionaries
   * Provides difficulty queries

3. **Generated Index File**

   * Automatically created by the generator system
   * Provides deterministic resource paths
   * Eliminates runtime directory scanning

This architecture allows difficulty settings to scale from simple damage modifiers to complete gameplay rule sets without requiring hardcoded logic.

---

# 🏗️ Architecture Overview

```text
Difficulty System
│
├── Difficulty Database (Autoload)
│   │
│   └── DifficultyResourceDatabase
│
├── Registry Scanner
│   │
│   └── Difficulty Resource Registry
│
└── Generated Index
    │
    ├── Main Difficulties
    ├── Player Effects
    └── Enemy Effects
```

---

# 🚀 Difficulty Database Autoload

## al_difficulty_database.gd

The Difficulty Database is the primary runtime access point.

It manages:

* Available difficulties
* Current selected difficulty
* Difficulty initialization
* Applying difficulty effects
* World readiness state

---

# Responsibilities

The database handles:

✅ Loading difficulty definitions
✅ Tracking active difficulty
✅ Applying player modifiers
✅ Applying enemy modifiers
✅ Broadcasting difficulty readiness

---

# Initialization

The database initializes once during startup.

```gdscript
func initialize():
	if is_initialized:
		return
```

During initialization:

```text
DifficultyDatabase
        |
        ↓
DifficultyResourceDatabase
        |
        ↓
Load Difficulty Resources
```

---

# 📦 Resource Loading Pipeline

```text
Generator System
        |
        ↓
difficulty_index.gd
        |
        ↓
Difficulty Database
        |
        ↓
Load .tres Resources
        |
        ↓
Validate Definitions
        |
        ↓
Store Runtime Lookup
```

---

# 📚 Resource Registry Layer

## DifficultyResourceDatabase

The registry layer handles resource discovery and storage.

It maintains:

```gdscript
var difficulty_map: Dictionary = {}
```

Runtime structure:

```text
Difficulty ID
      |
      ↓
DifficultyDefinition
```

Example:

```text
normal
  |
  └── DifficultyDefinition

nightmare
  |
  └── DifficultyDefinition

hell
  |
  └── DifficultyDefinition
```

---

# 🗂️ Difficulty Resource Validation

Every difficulty resource is validated before registration.

The system checks:

---

## Resource Type

Only valid:

```text
DifficultyDefinition
```

resources are accepted.

Invalid resources are rejected.

---

## Required ID

Every difficulty requires:

```gdscript
difficulty_id
```

Example:

```text
easy

normal

nightmare

hell
```

Missing IDs generate errors.

---

## Duplicate Protection

Duplicate difficulty identifiers are blocked.

Example:

```text
normal.tres
normal_copy.tres
```

Both cannot register:

```text
normal
```

---

# 📋 Difficulty Query API

The database provides simple runtime access.

---

## Get Difficulty

```gdscript
get_difficulty(id)
```

Example:

```gdscript
DifficultyDatabase.get_difficulty("nightmare")
```

Returns:

```text
DifficultyDefinition
```

---

## Get All Difficulties

```gdscript
get_all_difficulties()
```

Used for:

* Difficulty menus
* Selection screens
* Debug tools

---

## Get All IDs

```gdscript
get_all_ids()
```

Returns:

```text
[
 easy,
 normal,
 brutal,
 nightmare,
 hell
]
```

---

# 🎯 Difficulty Selection Pipeline

## set_difficulty()

The main gameplay entry point.

```gdscript
set_difficulty(id)
```

Flow:

```text
Player Selects Difficulty
          |
          ↓
Difficulty Database
          |
          ↓
Load Difficulty Definition
          |
          ↓
Set Current Difficulty
          |
          ↓
Apply Effects
```

---

# 🌎 World Ready Signal

After applying the selected difficulty:

```gdscript
world_ready_changed.emit(true)
```

This allows other systems to react when difficulty configuration is active.

Example:

```text
Difficulty Loaded
        |
        ↓
World Initialization Continues
```

---

# ⚙️ Difficulty Effect System

Difficulty does not directly modify gameplay values.

Instead, it uses the existing effect architecture.

```text
DifficultyDefinition

        |
        ├── Player Effects
        |
        └── Enemy Effects
```

---

# 🧍 Player Difficulty Effects

Applied through:

```gdscript
_apply_to_player()
```

Example effects:

```text
Easy
 └── Player Damage +
 └── Player HP +

Hell
 └── Player Damage -
 └── Player Healing Reduced
```

Effects are injected through:

```gdscript
owner.effect_manager.add_effect()
```

---

# 👹 Enemy Difficulty Effects

Applied through:

```gdscript
_apply_to_enemy()
```

All enemies in:

```gdscript
enemy
```

group receive the active difficulty modifiers.

Example:

```text
Nightmare

Enemy HP +
Enemy Damage +
Enemy Healing +
```

---

# 🧩 Effect-Based Design

Difficulty modifiers are separated into individual resources.

Example:

```text
difficulty
│
└── effects
    |
    ├── brutal
    │   |
    │   ├── player
    │   │   ├── damage
    │   │   └── hp
    │   |
    │   └── enemy
    │       ├── damage
    │       └── hp
```

Benefits:

* Modular modifiers
* Easy balancing
* Reusable effects
* Creator-friendly configuration

---

# 🏭 Generated Index System

## difficulty_index.gd

The index file is automatically generated.

Creators never manually edit this file.

---

# Responsibilities

The index provides:

* Deterministic resource paths
* Fast loading
* Export-safe references
* Large database scalability

---

# Generated Categories

The difficulty index is separated into:

---

## Main Difficulties

```gdscript
DIFFICULTY_PATHS_MAIN
```

Contains:

```text
easy
normal
brutal
nightmare
hell
```

---

## Enemy Effects

```gdscript
DIFFICULTY_PATHS_ENEMY
```

Contains:

```text
Enemy HP
Enemy Damage
Enemy Healing
```

---

## Player Effects

```gdscript
DIFFICULTY_PATHS_PLAYER
```

Contains:

```text
Player HP
Player Damage
Player Healing
```

---

# 🔧 Generator Workflow

```text
Creator Adds Difficulty Resource
             |
             ↓
Generator Scanner
             |
             ↓
Detect Resource Category
             |
             ↓
Generate Index File
             |
             ↓
Runtime Database Loads Resources
```

---

# 📁 Data Organization

Recommended structure:

```text
data
└── databases
    └── difficulty
        |
        ├── main
        │   ├── easy.tres
        │   ├── normal.tres
        │   ├── brutal.tres
        │   ├── nightmare.tres
        │   └── hell.tres
        |
        └── effects
            |
            ├── player
            │
            └── enemy
```

---

# 🛠️ Creator Workflow

Creating a new difficulty requires:

1. Create DifficultyDefinition resource.
2. Assign unique difficulty ID.
3. Add gameplay effects.
4. Place resource into the correct database folder.
5. Run generator.

The engine automatically:

✅ Finds the resource
✅ Registers the path
✅ Generates the index
✅ Loads the difficulty
✅ Makes it available at runtime

---

# 🌎 System Integration

The Difficulty Database integrates with:

* Player system
* Enemy system
* Effect Manager
* Combat system
* World initialization

Example:

```text
Difficulty Database

        |
        ↓

Difficulty Definition

        |
        ↓

Effect Manager

        |
        ↓

Player / Enemy Entities
```

---

# 📦 Engine Benefits

## Scalable

Supports:

* Multiple difficulty tiers
* Custom game modes
* Expansion content
* Creator-made challenges

---

## Data Driven

Gameplay tuning happens through resources instead of scripts.

Creators can modify:

* Enemy strength
* Player bonuses
* Healing rules
* Combat modifiers

without changing code.

---

## Reliable

Provides:

✅ Generated indexing
✅ Duplicate protection
✅ Resource validation
✅ Centralized difficulty control
✅ Effect-based scaling

---

# Summary

The Difficulty Database provides a modular difficulty framework built around generated indexing, registry-based loading, and effect-driven gameplay modification.

By separating difficulty definitions from their individual gameplay effects, the system allows creators to build everything from traditional difficulty modes to completely custom challenge configurations while maintaining the same scalable database architecture used throughout the engine.
