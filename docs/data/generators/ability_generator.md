# 🏷️ Ability Resource Generator System

The Ability Resource Generator System provides a data-driven pipeline for creating and managing all ability definitions.

Abilities are authored through CSV data and automatically converted into optimized `AbilityDefinition` `.tres` resources, allowing large-scale ability databases without manual resource creation.

The system separates **ability authoring data** from **runtime execution**, allowing abilities to be expanded through data changes rather than code modifications.

The system is responsible for:

* ability resource generation
* ability identity and metadata
* ability classification
* ability role assignment
* targeting configuration
* cast behavior configuration
* cooldown and timing definitions
* ability requirement generation
* effect reference assignment
* animation and audio metadata
* category and subcategory routing
* generated ability indexing

---

# 🧱 Ability Resource Architecture

Each generated ability is represented by an `AbilityDefinition` resource.

Ability resources contain:

* Ability identity
* Display information
* Classification data
* Ability role
* Targeting behavior
* Unlock requirements
* Cast configuration
* Cooldown and timing values
* Applied effect references
* Animation metadata
* Visual effect references
* Audio references

Generated abilities are stored as `.tres` resources and are loaded through the generated ability index.

Example:

```
Ability CSV
    |
    ↓
Ability Resource Generator
    |
    ↓
AbilityDefinition.tres
    |
    ↓
Ability Index
    |
    ↓
Runtime Ability Database
```

---

# ⚔️ Ability Classification Support

The Ability Resource Generator supports multiple ability classifications through the shared `AbilityDefinition` resource.

## Ability Types

Supported ability types:

* Active abilities
* Passive abilities
* Toggle abilities

Example:

```
Frost Nova

Type:
ACTIVE
```

```
Thick Skin

Type:
PASSIVE
```

```
Battle Stance

Type:
TOGGLE
```

---

## Ability Roles

Abilities are assigned gameplay roles for future filtering and organization.

Supported roles:

* Offense
* Defense
* Support
* Utility

Example:

```
Fireball

Role:
OFFENSE
```

```
Healing Aura

Role:
SUPPORT
```

---

## Target Modes

Abilities define how their targets are acquired.

Supported targeting modes:

* Self
* Target
* Ground
* Direction
* Area

Example:

```
Blink

Target Mode:
GROUND
```

```
Shield

Target Mode:
SELF
```

---

# 🗂️ Ability Database Routing

Generated abilities are automatically organized using category and subcategory metadata.

Example:

```
data/databases/entity/abilities/

├── combat/
│   ├── offensive/
│   │    ├── fireball.tres
│   │    ├── slash.tres
│
├── defense/
│   ├── shields/
│
├── utility/
│   ├── movement/
│
├── support/
│   ├── healing/
```

The routing system allows large ability databases to remain organized while supporting unlimited expansion.

Folder placement is determined directly from CSV metadata.

Example:

CSV:

```
category:
combat

subcategory:
offensive
```

Generated:

```
abilities/combat/offensive/ability_id.tres
```

---

# ⏱️ Cast Behavior Configuration

Abilities support configurable casting behavior.

Supported cast behaviors:

* Instant
* Cast Time
* Channel
* Toggle

Generated abilities can define:

* cooldown duration
* cast duration
* channel duration
* interruption rules
* line-of-sight requirements
* maximum range
* targeting radius

Example:

```
Fireball

Cast Behavior:
CAST_TIME

Cast Time:
2 seconds

Cooldown:
5 seconds

Range:
12
```

---

# 🧩 Ability Requirement Generation

The generator converts requirement definitions from CSV data into structured `AbilityRequirement` resources.

Supported requirements:

* Class requirements
* Level requirements
* Stat requirements
* Quest requirements
* Item requirements
* Faction requirements

Example CSV:

```
CLASS:MAGE;
LEVEL:10;
STAT:INTELLIGENCE:50
```

Generated:

```
AbilityRequirement[]
|
├── Class: Mage
├── Level: 10
└── Intelligence: 50
```

Requirements are stored with the ability definition and evaluated by runtime systems.

The generator defines requirements.

The Ability System handles requirement validation.

---

# ⚔️ Ability Effect References

Abilities support multiple linked effects.

Effects are stored as references rather than embedded behavior.

Example:

CSV:

```
effects:
fire_damage;burning;knockback
```

Generated:

```
AbilityDefinition

effects:
[
 fire_damage,
 burning,
 knockback
]
```

This allows abilities to combine existing effect definitions without duplicating effect logic.

Example:

```
Ability
 |
 ↓
Effect References
 |
 ↓
Effect Database
 |
 ↓
Effect Manager
```

---

# 🎬 Animation, Visual, and Audio Configuration

Abilities support presentation metadata.

Generated configuration includes:

## Animation

Supports:

* animation resource reference
* animation playback speed

---

## Visual Effects

Supports:

* cast VFX
* impact VFX

---

## Audio Effects

Supports:

* cast sound
* impact sound

Example:

```
Lightning Bolt

Animation:
cast_lightning

Cast VFX:
lightning_charge

Impact VFX:
lightning_hit

Cast SFX:
spell_cast_03
```

---

# ⚙️ Generation Pipeline

The generation process:

1. Reads ability CSV definitions
2. Parses CSV headers and values
3. Creates `AbilityDefinition` resources
4. Applies identity metadata
5. Applies classification data
6. Applies casting configuration
7. Generates requirements
8. Assigns effect references
9. Applies animation and audio metadata
10. Routes resources into category folders
11. Saves `.tres` resources
12. Rebuilds the ability index

---

# 🔎 Generated Ability Index

The Ability Index Generator scans generated ability resources and creates a runtime lookup file.

Generated output:

```
system/entity/ability/runtime/ability_index.gd
```

The index provides:

* deterministic ability discovery
* filesystem-independent loading
* fast runtime lookup
* scalable database expansion

Generated structure:

```gdscript
const ABILITY_PATHS_ALL := [
    "res://data/databases/entity/abilities/combat/fire/fireball.tres",
    "res://data/databases/entity/abilities/support/heal/healing_wave.tres"
]
```

The index is automatically rebuilt whenever ability generation runs.

---

# 🔗 Ability System Integration

The Ability Resource Generator provides ability definitions.

It does not execute abilities.

Responsibilities are separated:

```
CSV Data
 |
 ↓
Ability Resource Generator
 |
 ↓
AbilityDefinition
 |
 ↓
Ability Database
 |
 ↓
Ability Manager
 |
 ↓
Entity
 |
 ↓
Effect System
```

The generator handles:

* ability creation
* metadata assignment
* resource storage
* index generation

Runtime systems handle:

* ability activation
* cooldown tracking
* casting execution
* targeting
* effect application
* gameplay behavior

---

# ✅ System Responsibilities

The Ability Resource Generator System:

✅ Creates ability resources
✅ Converts CSV data into `.tres` definitions
✅ Generates ability metadata
✅ Configures casting behavior
✅ Builds ability requirements
✅ Assigns effect references
✅ Routes abilities into database categories
✅ Generates runtime ability indexes

The Ability Resource Generator System does **not**:

❌ Execute abilities
❌ Handle casting logic
❌ Manage cooldown timers
❌ Validate gameplay requirements
❌ Apply effects to entities
❌ Control combat behavior

Those responsibilities belong to:

* Ability Database System
* Ability Manager System
* Requirement Validation System
* Effect System
* Combat System
* Entity System
