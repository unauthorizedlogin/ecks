# ⚙️ Effect Resource Generator Architecture

## Overview

The Effect Resource Generator provides the asset creation pipeline for the global Effect System.

It converts CSV-defined effect data into runtime-safe `StatEffect` resources and automatically rebuilds the effect registry index.

Like other engine data pipelines, the system follows the generated resource architecture:

1. **CSV Definition Layer**

   * Creator-facing effect configuration
   * Defines effect behavior and properties
   * Supports all StatEffect settings

2. **Resource Generator**

   * Converts CSV rows into `.tres` resources
   * Applies effect configuration
   * Organizes generated resources by category

3. **Generated Index**

   * Automatically scans generated resources
   * Creates deterministic runtime paths
   * Eliminates runtime folder scanning

This allows effects to be created entirely through data without requiring custom scripts for every new gameplay modifier.

---

# 🏗️ Architecture Overview

```text
Effect System
│
├── Effect CSV Database
│
├── Effect Resource Generator
│   │
│   └── StatEffect Resources
│
├── Effect Index Generator
│
└── Runtime Effect Database
```

---

# 📄 Effect CSV Definition Layer

## effect_strings.csv

The CSV file acts as the authoring database for effects.

Each row represents one effect definition.

Example:

```text
effect_id:
burning

category:
damage

subcategory:
fire

effect_type:
DOT

stat_name:
HP

amount:
5

duration:
10
```

The CSV supports:

* Effect identity
* Stat modification
* Effect behavior
* Timing
* Costs
* Tags
* Revive settings
* Knockback settings

---

# 📦 Effect Resource Generator

## effect_resource_generator.gd

The generator converts CSV definitions into `StatEffect` resources.

Generation flow:

```text
CSV Row
   |
   ↓
Create StatEffect
   |
   ↓
Apply Configuration
   |
   ↓
Save .tres Resource
   |
   ↓
Rebuild Effect Index
```

---

# 🧬 StatEffect Resource Generation

Each generated resource contains:

## Identity

```text
effect_id
display_name
category
subcategory
```

Used for:

* Runtime lookup
* Localization
* Database organization

---

## Core Effect Data

Generated through:

```gdscript
_apply_core()
```

Controls:

* Target stat
* Amount
* Percent scaling
* Effect type

Example:

```text
Strength Buff

Type:
STAT_MOD

Stat:
Strength

Amount:
10
```

---

# ⚔️ Effect Types

The generator supports all global effect behaviors.

```text
STAT_MOD
OVERRIDE
HEAL
DOT
HOT
REVIVE
TEMP_BUFF
TEMP_DEBUFF
ENCHANT
AURA
PASSIVE
KNOCKBACK
```

Examples:

| Effect          | Type     |
| --------------- | -------- |
| Strength Potion | STAT_MOD |
| Burning         | DOT      |
| Regeneration    | HOT      |
| Resurrection    | REVIVE   |
| Weapon Fire     | ENCHANT  |
| Battle Aura     | AURA     |

---

# ⏱️ Timing Configuration

Generated through:

```gdscript
_apply_timing()
```

Supports:

* Trigger type
* Target type
* Duration
* Tick interval

Examples:

```text
Burning

Trigger:
OnHit

Duration:
10 seconds

Tick:
1 second
```

---

# 💸 Effect Cost Generation

Generated through:

```gdscript
_apply_costs()
```

Effects can consume:

* HP
* MP
* Class Resources

Supported payment modes:

```text
NONE

REQUIRE_FULL

SPEND_AVAILABLE
```

Example:

```text
Blood Magic Effect

Cost:
25% HP

Payment:
SPEND_AVAILABLE
```

---

# 🏷️ Effect Tag Generation

Generated through:

```gdscript
_apply_tags()
```

CSV:

```text
fire;damage;magic
```

becomes:

```text
[
 fire,
 damage,
 magic
]
```

Tags allow future systems to filter and identify effects.

---

# 💀 Special Effect Configuration

The generator supports specialized effect behaviors.

---

## Revive Effects

```gdscript
_apply_revive()
```

Supports:

* HP restoration
* MP restoration
* Class resource restoration
* Percentage-based revival

---

## Knockback Effects

```gdscript
_apply_knockback()
```

Supports:

* Knockback force
* Direction configuration

---

# 📁 Generated Resource Structure

Resources are automatically organized by category.

Example:

```text
effects
│
├── damage
│   ├── fire
│   │   └── burning.tres
│   └── poison
│       └── poison.tres
│
├── buff
│   └── temporary
│       └── battle_shout.tres
│
├── heal
│   └── instant
│       └── heal_small.tres
│
└── revive
    └── resurrection
        └── battle_resurrection.tres
```

---

# 🗂️ Effect Index Generator

## effect_index_generator.gd

The index generator creates the runtime effect registry.

Generated file:

```text
effect_index.gd
```

Contains:

```gdscript
const EFFECT_PATHS_ALL
```

with every generated effect resource.

---

# 🔄 Generator Workflow

```text
Creator Adds Effect CSV Entry
              |
              ↓
Effect Resource Generator
              |
              ↓
Create StatEffect Resource
              |
              ↓
Save .tres File
              |
              ↓
Effect Index Generator
              |
              ↓
Runtime Database Loads Effects
```

---

# 🛠️ Creator Workflow

Creating a new effect requires:

1. Add a row to the effect CSV.
2. Configure effect behavior.
3. Assign category and subcategory.
4. Run the generator.

The engine automatically:

* ✅ Creates the resource
* ✅ Places it into the correct folder
* ✅ Updates the index
* ✅ Makes the effect available at runtime

---

# 🌎 System Integration

The Effect Resource Generator integrates with:

* Effect Database
* Effect Manager
* Items
* Abilities
* Potions
* Equipment
* Difficulty System
* Combat System

Example:

```text
Effect CSV

      ↓

Effect Resource

      ↓

Effect Database

      ↓

Effect Manager

      ↓

Gameplay Systems
```

---

# 📦 Engine Benefits

## Scalable

Supports:

* Unlimited effect definitions
* New effect categories
* Expansion content
* Creator-authored effects

---

## Data Driven

Gameplay effects are configured through data instead of hardcoded scripts.

Creators can modify:

* Damage values
* Buff strength
* Duration
* Costs
* Triggers

without changing engine code.

---

## Reliable

Provides:

* ✅ Generated resources
* ✅ Deterministic indexing
* ✅ CSV-driven authoring
* ✅ Centralized effect configuration
* ✅ Runtime-safe loading

---

# Summary

The Effect Resource Generator provides the creation pipeline for the global Effect System.

By converting CSV definitions into indexed runtime resources, the system allows effects to be authored as reusable gameplay components that can power combat, items, abilities, difficulty modifiers, and future content systems.
