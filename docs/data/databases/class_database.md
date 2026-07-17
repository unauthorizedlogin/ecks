# 🧬 Class Database Architecture

## Overview

The Class Database provides a centralized, data-driven framework for managing all entity class definitions across the engine.

Classes are used as the foundation for:

* Player archetypes
* Enemy archetypes
* NPC/vendor classifications
* Runtime stat initialization
* Class-specific gameplay rules
* Level progression data

The system separates **resource discovery**, **runtime access**, and **generated indexing** into three distinct layers.

This follows the standard engine database architecture:

1. **Autoload Database Manager**

   * Runtime-facing class API
   * Handles initialization
   * Provides gameplay queries
   * Coordinates class, rule, and progression data

2. **Registry Scanner / Resource Database**

   * Handles resource loading
   * Maintains runtime caches
   * Organizes classes by category/subcategory
   * Provides filtered lookup access

3. **Generated Index Files**

   * Automatically created by generator tools
   * Provide deterministic resource paths
   * Remove runtime filesystem scanning
   * Ensure export-safe database loading

---

# 🧬 Layer 1 — Class Database Manager

## `al_class_database.gd`

The Class Database is the primary runtime interface for all class-related requests.

It acts as the public API between gameplay systems and class data.

### Responsibilities

The manager handles:

* Database initialization
* Loading class resources
* Loading class rules
* Loading XP progression data
* Exposing gameplay-safe queries

The manager does **not**:

* Scan directories
* Calculate stats
* Modify combat values
* Handle entity creation
* Apply runtime modifiers

---

# 🚀 Initialization Pipeline

During startup:

```
ClassDatabase.initialize()
        |
        |
        v
ClassResourceDatabase
        |
        |
        +--> Load Class Index
        |
        +--> Load Rule Index
        |
        +--> Load XP Index
```

Initialization loads:

### Class Definitions

Contains:

* Player classes
* Enemy classes
* NPC classes
* Vendor classes

Examples:

```
player/
 ├── rogue
 ├── mage
 ├── warrior

enemy/
 ├── beasts
 ├── undead
 ├── elementals

npc/
 └── vendors
```

---

### Class Rules

Separate from stat definitions.

Rules define behavioral information such as:

* Role
* Archetype
* Gameplay restrictions
* Class-specific metadata

Examples:

```
rogue/
 └── assassin.tres

mage/
 └── wizard.tres

warrior/
 └── knight.tres
```

---

### XP Progression Data

Class progression is also database-driven.

Loaded resources:

* Level curves
* Growth tables

Provides:

* XP requirements
* Stat growth
* Class scaling

---

# 📥 Class Queries

The database exposes multiple access patterns.

## Single Class Lookup

Example:

```gdscript
ClassDatabase.get_stat_block("assassin")
```

Returns:

```
Runtime-safe duplicated StatBlock
```

The database does not return shared resources.

Each entity receives its own runtime copy.

---

## Class Categories

Classes are organized into hierarchical groups.

Example:

```gdscript
get_player_classes()
```

Returns:

```
{
    rogue:
    [
        assassin,
        ranger,
        thief
    ],

    mage:
    [
        wizard,
        necromancer
    ]
}
```

This allows systems such as:

* Character creation
* Class selection menus
* NPC filtering
* Enemy spawning

to query classes dynamically.

---

# 📦 Layer 2 — Class Resource Database

## `class_resource_database.gd`

The registry layer handles all resource loading and caching.

It converts static `.tres` resources into fast runtime dictionaries.

---

# Database Caches

The registry maintains multiple lookup maps:

## Primary Class Cache

```gdscript
class_map
```

Stores:

```
class_id
    |
    v
StatBlock Resource
```

Example:

```
assassin
    |
    +--> Assassin StatBlock
```

Used for direct lookup.

---

## Rule Cache

```gdscript
rule_map
```

Stores:

```
class_id
    |
    v
ClassRuleResource
```

Separates gameplay rules from base stats.

---

## XP Cache

```gdscript
level_curve_map
```

Stores:

```
class_id
    |
    v
LevelCurveResource
```

---

```gdscript
growth_map
```

Stores:

```
class_id
    |
    v
LevelGrowthData
```

---

# Category Registry

Classes are automatically organized during registration.

Structure:

```
category_map

player
 |
 +-- rogue
 |     |
 |     +-- assassin
 |     +-- ranger
 |
 +-- mage
       |
       +-- wizard


enemy
 |
 +-- beasts
       |
       +-- wolf
       +-- bear
```

This allows dynamic filtering without additional database work.

---

# Runtime Safety

Class resources are immutable definitions.

When gameplay requests a class:

```gdscript
get_stats(class_id)
```

the database returns:

```
StatBlock.duplicate_block()
```

instead of the original resource.

Benefits:

* Prevents shared state corruption
* Allows runtime stat modification
* Supports multiple entities using the same class
* Keeps database definitions clean

---

# 📂 Layer 3 — Generated Index System

## `class_index.gd`

The class index is automatically generated.

It contains:

```gdscript
const CLASS_PATHS := []
```

with every registered class resource.

Example:

```
res://data/databases/entity/class/player/rogue/assassin.tres
res://data/databases/entity/class/enemy/beasts/enemy_wolf.tres
res://data/databases/entity/class/npc/vendor/vendor_armorer.tres
```

---

## Why Generated Indexes?

Traditional runtime scanning:

```
Game Start
 |
 v
Scan folders
 |
 v
Find resources
 |
 v
Load files
```

Problems:

* Slower startup
* Export complications
* Platform filesystem differences
* Hidden resource failures

The generator pipeline converts this into:

```
Generator
 |
 v
class_index.gd
 |
 v
Runtime Load
 |
 v
Instant Database Initialization
```

Benefits:

✅ Faster startup
✅ Deterministic loading
✅ Export-safe
✅ No filesystem dependency
✅ Easy validation

---

# ⚖️ Rule Index

## `class_rule_index.gd`

Rules use their own generated index.

Example:

```
RULE_PATHS

rogue/dps/assassin.tres

mage/dps/wizard.tres

warrior/tank/knight.tres
```

Keeping rules separate allows:

* Independent expansion
* Cleaner resource organization
* Optional class behavior layers

---

# 🧠 Database Flow

Complete class loading pipeline:

```
                 Generator Tools
                       |
                       |
        +--------------+--------------+
        |                             |
        v                             v

 class_index.gd              class_rule_index.gd

        |                             |

        +-------------+---------------+

                      |

                      v

          ClassResourceDatabase

                      |

        +-------------+-------------+
        |             |             |

        v             v             v

   class_map     rule_map      xp_maps

        |

        v

    ClassDatabase

        |

        v

 Gameplay Systems
```

---

# 🎮 Systems Using Class Database

The Class Database provides foundation data for:

### Player Creation

* Starting class selection
* Base stats
* Progression setup

### Enemy Spawning

* Enemy archetypes
* Stat initialization
* Difficulty scaling

### NPC Systems

* Vendor classifications
* Dialogue helpers
* Entity metadata

### Combat

* Base attributes
* Growth values
* Class rules

### UI

* Class names
* Class categories
* Selection menus

---

# 🛠️ Creator Workflow

Creators do not manually register classes.

Workflow:

```
Create StatBlock Resource
        |
        v
Place in class database folder
        |
        v
Run Generator
        |
        v
class_index updated
        |
        v
Class available everywhere
```

No code changes required.

No manual registration required.

---

# ⭐ Design Philosophy

The Class Database follows the engine's core data-driven architecture:

**Resources define content.**

**Generators define discovery.**

**Databases define runtime access.**

This allows creators to expand:

* Classes
* Enemies
* NPC archetypes
* Vendors
* Progression systems

without modifying engine code.

The database becomes a scalable content registry rather than a hardcoded gameplay system.
