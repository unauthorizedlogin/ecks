# 📜 StatBlock Resource Architecture

## Overview

The **StatBlock Resource** is the foundational data container for all entity class-based stat definitions within the engine.

It provides a unified schema for:

* Player classes
* Enemy classes
* NPC classes
* Future entity archetypes

The StatBlock represents the **static class data layer**. It defines the base identity, categorization, and stat values that are loaded by the Class Database and converted into runtime-safe copies when used by gameplay systems.

The architecture separates:

1. **StatBlock Resource**
2. **Class Resource Generator**
3. **Class Database Runtime Access**
4. **Runtime Stat Processing Systems**

The StatBlock itself does **not** perform calculations, combat logic, balancing, or runtime modification handling.

---

# 🧬 StatBlock Resource Responsibilities

The StatBlock Resource is responsible for:

* Defining entity class identity
* Storing base stat values
* Providing exported editor fields
* Grouping stats by gameplay category
* Acting as the source resource for generated class data

The resource provides:

* Class identifiers
* Display information
* Category classification
* Base stat values
* Derived stat storage

---

# 🚫 StatBlock Does Not

The StatBlock does not:

* Calculate derived statistics
* Handle level scaling
* Handle equipment modifiers
* Apply buffs/debuffs
* Manage combat calculations
* Modify runtime player state
* Balance classes
* Control progression systems

Runtime manipulation is handled by external systems.

---

# 🏷️ Class Identity Data

Every StatBlock contains metadata used for database indexing and gameplay lookup.

```gdscript
@export var class_id: String
@export var display_name: String
@export var portrait: String
@export var category: String
@export var subtype: String
```

## class_id

The unique identifier used by:

* Class Database
* Entity creation
* Runtime lookup
* Save systems
* Dialogue references

Example:

```
wizard
knight
enemy_dragon
vendor_blacksmith
```

---

## display_name

Localized display identifier.

The value is passed through localization systems rather than storing translated text directly.

Example:

```
class_wizard_name
```

---

## portrait

Optional visual reference used by:

* Character creation
* Dialogue systems
* NPC displays
* Entity panels

---

## category / subtype

Used for database organization and filtering.

Example:

```
category:
player

subtype:
mage
```

Creates:

```
Player
 └── Mage
      ├── Wizard
      ├── Necromancer
      └── Battle Mage
```

---

# 📊 Stat Organization

Stats are grouped into gameplay categories through Godot export groups.

This provides:

* Inspector organization
* Easier balancing
* Clear stat ownership
* Future editor tooling support

---

# ❤️ Base Vital Stats

## Base Vitals

Primary survival attributes.

```gdscript
Stamina
Vitality
Wisdom
```

Used as foundations for:

* Health pools
* Resource pools
* Defensive calculations

---

# 🔮 Derived Vital Stats

Calculated or generated values.

```gdscript
HP
MP
ClassResource
```

Examples:

```
Vitality → HP
Wisdom → MP
Class → Resource Type
```

These exist inside the resource because generated classes may require predefined values.

---

# ♻️ Regeneration Stats

Controls recovery behavior.

```gdscript
HPRegen
MPRegen
ResourceRegen
```

Used by:

* Resource managers
* Combat recovery systems
* Passive regeneration

---

# ⚔️ Primary Stats

Core offensive identity stats.

```gdscript
Dexterity
Intelligence
Strength
```

Examples:

```
Strength
 → Warrior scaling

Dexterity
 → Rogue scaling

Intelligence
 → Mage scaling
```

---

# 🛡 Secondary Stats

Supporting attributes.

```gdscript
Agility
Willpower
Fortitude
```

Used for:

* Defensive calculations
* Status resistance
* Secondary scaling

---

# 💥 Damage Stats

Offensive combat values.

```gdscript
Damage
CritHit
CritDmg
```

Used by:

* Combat manager
* Damage calculations
* Class balancing

---

# 🧠 Tactical Stats

Partially derived combat behavior.

```gdscript
Armor
Block
Dodge
Sneak
Barrier
```

Examples:

```
Armor
→ Physical mitigation

Block
→ Attack prevention

Dodge
→ Avoidance chance
```

---

# 🔥 Resistance Stats

Elemental and damage mitigation.

```gdscript
IceResist
FireResist
LightResist
PhysResist
AllResist
```

Used by:

* Effect systems
* Damage mitigation
* Difficulty modifiers

---

# ⚡ Speed Stats

Movement and action timing.

```gdscript
Movement
AtkSpd
CastSpd
Speed
```

Controls:

* Movement rate
* Attack timing
* Casting speed
* General action speed

---

# 🎭 Influential Stats

Social and utility attributes.

```gdscript
Charm
Concentration
Luck
```

Used for future systems such as:

* Dialogue checks
* Loot calculations
* Skill interactions
* World events

---

# 📈 Fully Derived Ratings

High-level calculated values.

```gdscript
Attack
Defense
```

These provide simplified gameplay-facing ratings derived from multiple underlying stats.

Example:

```
Attack =
Damage +
Strength +
Weapon Scaling

Defense =
Armor +
Fortitude +
Resistance
```

---

# 🔄 Runtime Duplication

StatBlocks stored inside the database remain immutable templates.

When requested:

```gdscript
get_stats(class_id)
```

the database returns:

```gdscript
duplicate_block()
```

This creates a runtime-safe instance.

Architecture:

```
Class Resource
        |
        v
StatBlock Template
        |
        v
Class Database
        |
        v
Runtime StatBlock Copy
        |
        v
Entity
```

Benefits:

* Prevents modifying database templates
* Supports multiple entities sharing one class
* Allows runtime stat changes

---

# 🏭 Generation Pipeline

StatBlocks are generated from CSV definitions.

Pipeline:

```
CSV
 |
 |
 v
Class Resource Generator
 |
 |
 v
StatBlock Resource (.tres)
 |
 |
 v
Class Index Generator
 |
 |
 v
class_index.gd
 |
 |
 v
Class Database
```

---

# 📂 Resource Storage

Generated resources are organized automatically.

Example:

```
entity/class/

player/
 ├── mage/
 │    ├── wizard.tres
 │    └── necromancer.tres
 │
 └── warrior/
      └── knight.tres


enemy/
 └── beasts/
      └── wolf.tres
```

Folder structure is generated from:

```
category
subtype
```

---

# 🧩 Integration Points

## Class Database

Responsible for:

* Loading StatBlocks
* Indexing class IDs
* Providing runtime copies

---

## Entity Systems

Consume StatBlocks for:

* Entity creation
* Starting attributes
* Archetype selection

---

## Combat Systems

Consume runtime stats for:

* Damage
* Defense
* Resistances
* Speed

---

## Progression Systems

Consume stats for:

* Scaling
* Growth
* Level progression

---

# Architecture Summary

```
                 CSV
                  |
                  v
        Class Resource Generator
                  |
                  v
            StatBlock.tres
                  |
                  v
          Class Index Generator
                  |
                  v
          Class Resource Database
                  |
                  v
            Class Database API
                  |
                  v
        Runtime Entity StatBlock
                  |
                  v
       Combat / Progression / World
```

The StatBlock Resource provides the **static identity and statistical foundation** for every class-driven entity in the engine while remaining completely separated from runtime gameplay logic.
