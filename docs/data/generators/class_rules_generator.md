# 📜 ClassRuleResource Architecture

## Overview

The **ClassRuleResource** defines the gameplay rules and restrictions associated with a class.

While the **StatBlock Resource** defines *what a class is statistically*, the ClassRuleResource defines *how that class behaves*.

It provides the rule layer for:

* Equipment permissions
* Weapon restrictions
* Armor restrictions
* Starting equipment
* Combat affiliation
* Visual presentation
* Class tags
* Class restrictions

The resource is generated from CSV data and consumed by the Class Database runtime layer.

---

# 🧩 Class Data Architecture

Class data is separated into two independent resources:

```
                 Class CSV
                    |
        ---------------------------
        |                         |
        v                         v

   StatBlock Generator       Rule Generator

        |                         |
        v                         v

   StatBlock.tres        ClassRuleResource.tres

        |                         |
        ---------------------------
                    |
                    v

            Class Database
                    |
                    v

          Runtime Entity Setup
```

---

# 🧠 ClassRuleResource Responsibilities

The ClassRuleResource is responsible for:

* Defining class behavior rules
* Defining equipment compatibility
* Defining class identity restrictions
* Providing starting equipment data
* Providing visual references
* Defining combat relationships

---

# 🚫 ClassRuleResource Does Not

The resource does not:

* Handle combat calculations
* Equip items directly
* Validate inventory state
* Spawn entities
* Manage player progression
* Apply restrictions dynamically

Those systems consume the rule data.

---

# 🏷️ Class Identity

```gdscript
@export var class_id: String
var category: String
var subtype: String
```

The identity fields connect the rule resource to the matching StatBlock.

Example:

```
class_id:
wizard


StatBlock:
wizard.tres


ClassRule:
wizard.tres
```

This allows the runtime database to pair:

```
Wizard Stats
+
Wizard Rules
=
Playable Wizard Entity
```

---

# 🎨 Visual Configuration

## Visual Group

```gdscript
@export var spritesheet: Texture2D
@export var portrait: Texture2D
@export var animation_library: String
```

Controls class presentation.

Used by:

* Entity rendering
* Character creation
* Dialogue systems
* UI displays

---

# ⚔️ Combat Rules

## Damage Team

```gdscript
@export var damage_team: ResourceDamageTeam
```

Defines faction combat behavior.

Controls:

* Team ownership
* Allies
* Friendly fire rules

Example:

```
Player Team

Allies:
- Player
- Summons

Enemy Team

Friendly Fire:
False
```

This keeps combat relationships data-driven.

---

# 🗡️ Weapon Permissions

The ClassRuleResource defines weapon compatibility.

Example:

```gdscript
weapon_sword
weapon_staff
weapon_bow
weapon_dagger
```

A class can independently define:

* Allowed weapons
* Dual wield capability
* Future weapon types

Example:

```
Knight

✓ Sword
✓ Shield
✓ Axe

✗ Staff
✗ Wand
```

---

# 🛡️ Armor Permissions

Armor compatibility is controlled separately.

```gdscript
armor_cloth
armor_light
armor_medium
armor_heavy
```

Example:

```
Mage

✓ Cloth

Rogue

✓ Light

Warrior

✓ Heavy
✓ Medium
```

---

# 🎒 Starting Equipment

## Starting Equipment System

The resource stores the initial equipment loadout.

Example:

```gdscript
start_weapon
start_head
start_body
start_ring
```

The resource supports:

* Standard equipment slots
* Custom future slots

---

# Equipment Slot Mapping

The helper:

```gdscript
get_starting_equipment()
```

converts the resource fields into an equipment dictionary.

Example output:

```
{
 HEAD:
 "wizard_hat",

 BODY:
 "mage_robe",

 WEAPON:
 "wizard_staff"
}
```

This allows equipment systems to consume class data without knowing resource structure.

---

# 🔮 Future Equipment Expansion

Reserved slots:

```gdscript
start_custom1
start_custom2
start_custom3
start_custom4
```

allow additional equipment categories without changing the resource architecture.

Examples:

* Relics
* Artifacts
* Companion items
* Class-specific gear

---

# 🏷️ Class Tags

Tags describe class identity.

Examples:

```gdscript
tag_arcane
tag_healer
tag_tank
tag_ranged
tag_stealth
```

Used for:

* Filtering
* Requirements
* Dialogue checks
* Skill compatibility
* Future systems

Example:

```
Wizard

Tags:
✓ Arcane
✓ Spellcaster

Restrictions:
✗ Heavy Armor
✗ Shield
```

---

# 🚫 Restrictions

Restriction flags define prohibited interactions.

Example:

```gdscript
forbid_arcane
forbid_healer
forbid_tank
```

Used for future validation systems.

Architecture:

```
Item Requirement
        |
        v
Class Tags
        |
        v
Restrictions
        |
        v
Allow / Deny
```

---

# 🏭 Generation Pipeline

Class rules follow the standard generator architecture.

```
CSV
 |
 |
 v
ClassRuleGenerator
 |
 |
 v
ClassRuleResource (.tres)
 |
 |
 v
ClassRuleIndexGenerator
 |
 |
 v
class_rule_index.gd
 |
 |
 v
ClassResourceDatabase
 |
 |
 v
Runtime Rule Lookup
```

---

# 📂 Generated Resource Structure

Output folders are generated automatically:

```
entity/rules/

player/
 ├── mage/
 │    └── wizard.tres
 │
 └── warrior/
      └── knight.tres


enemy/
 └── boss/
      └── dragon.tres
```

Generated from:

```gdscript
category
subtype
```

---

# 🔄 Runtime Loading

The ClassResourceDatabase loads rule resources through:

```gdscript
class_rule_index.gd
```

The index provides deterministic export-safe loading.

Example:

```
class_rule_index.gd

RULE_PATHS
      |
      v

ClassResourceDatabase
      |
      v

rule_map[class_id]
```

---

# 🧾 CSV Driven Architecture

The generator converts CSV columns into resource properties.

Example:

CSV:

```csv
class_id,weapon_sword,armor_heavy,tag_tank
knight,true,true,true
```

Generates:

```gdscript
rule.weapon_sword = true
rule.armor_heavy = true
rule.tag_tank = true
```

---

# 🔗 Integration Points

## Class Database

Provides:

```gdscript
get_class_rule(class_id)
```

Runtime systems request rules through the database.

---

## Equipment System

Uses:

* Weapon permissions
* Armor permissions
* Starting equipment

---

## Combat System

Uses:

* Damage team
* Friendly fire
* Ally relationships

---

## Character Creation

Uses:

* Visual data
* Starting equipment
* Class identity

---

# Architecture Summary

```
                  Class CSV
                      |
                      v
             Class Rule Generator
                      |
                      v
          ClassRuleResource (.tres)
                      |
                      v
        ClassRuleIndexGenerator
                      |
                      v
          class_rule_index.gd
                      |
                      v
          ClassResourceDatabase
                      |
                      v
            Runtime Class Rules
                      |
        --------------------------
        |            |           |
        v            v           v

 Equipment     Combat       Entity Setup
```

The **ClassRuleResource** provides the behavioral definition layer for classes, keeping equipment rules, combat identity, restrictions, and presentation data completely separated from statistical values and runtime systems.
