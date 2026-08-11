# ⚡ Ability Definition 

## Overview

`AbilityDefinition` is the universal resource describing every ability available within the framework.

Abilities are entirely data-driven and contain the information required to determine how an ability behaves, who may use it, how it is cast, and which gameplay effects it applies.

The resource does not contain execution logic. Instead, it provides configuration consumed by the **Ability Manager** and **Ability Runtime**.

This resource supports:

* ⚔️ Active abilities
* ✨ Passive abilities
* 🔄 Toggle abilities
* ⏳ Cast-time abilities
* 📡 Channeled abilities
* 🎯 Multiple targeting modes
* 📋 Progression requirements
* ✨ Multi-effect execution
* 🎞 Animation and audiovisual metadata

---

# 🧱 Resource Identity

```gdscript
ability_id
```

Unique identifier for the ability.

Used for:

* Database indexing
* Runtime lookup
* Save references
* Cooldown tracking
* Debugging

---

# 🗂️ Generator Metadata

```gdscript
category
subcategory
```

Used by the data generation pipeline for organization and automatic database construction.

Example:

```
Abilities
├── Warrior
├── Mage
├── Rogue
└── Utility
```

---

# ⚔️ Ability Classification

Every ability belongs to a primary gameplay category.

## Ability Type

Defines the overall behavior.

Supported types:

* Active
* Passive
* Toggle

---

## Ability Role

Describes the intended gameplay purpose.

Supported roles:

* Offense
* Defense
* Support
* Utility

These roles assist with organization and future filtering without affecting runtime behavior.

---

# 🎯 Targeting System

Abilities define how targets are selected.

Supported targeting modes include:

* Self
* Target
* Ground
* Direction
* Area

Additional targeting properties define:

* Maximum range
* Area radius
* Line-of-sight requirements

This separates targeting rules from execution logic.

---

# 📋 Requirement System

Abilities may define one or more requirements before they can be used.

Supported requirement categories include:

* Class
* Level
* Stat
* Quest
* Item

Requirements are evaluated by the **Ability Manager** prior to runtime creation.

---

# ⏳ Casting System

Abilities support multiple casting behaviors.

## Instant

Executes immediately.

Examples:

* Basic attacks
* Instant heals

---

## Cast Time

Requires a casting period before execution.

Examples:

* Fireball
* Resurrection

---

## Channel

Executes repeatedly while channeling.

Examples:

* Beam attacks
* Healing streams
* Sustained effects

---

## Toggle

Remains active until manually disabled or interrupted.

Examples:

* Auras
* Persistent stances

---

# ⏱️ Timing Configuration

Abilities define several timing properties.

Supported timers include:

* Cooldown
* Cast time
* Channel duration

These values determine runtime behavior but are enforced by the Ability Manager and Ability Runtime rather than the resource itself.

---

# ✨ Applied Effects

Abilities reference one or more gameplay effects.

```gdscript
effects
```

Each entry references a `StatEffect` resource.

During execution:

```
Ability
      ↓
Effect Manager
      ↓
Stat Effects
```

This allows a single ability to apply any combination of damage, healing, buffs, debuffs, crowd control, or other gameplay modifications without requiring unique ability logic.

---

# 🎞 Presentation Data

Abilities also contain presentation metadata used by rendering systems.

Supported data includes:

* Animation
* Animation speed
* Cast visual effects
* Impact visual effects
* Cast audio
* Impact audio

These values describe how an ability should be presented without affecting gameplay calculations.

---

# 🔗 Ability Pipeline

```
Ability Definition
         |
         ↓
 Ability Manager
         |
         ↓
 Ability Runtime
         |
         ↓
  Effect Manager
         |
         ↓
   StatEffect Resources
         |
         ↓
Combat / Stats / Vitals
```

---

# ✅ Design Rule

`AbilityDefinition` is the universal data container for gameplay abilities.

Abilities should contain only configuration describing casting behavior, targeting, requirements, presentation, and applied effects.

Gameplay execution should always be delegated to the **Ability Manager**, while all gameplay modifications should ultimately resolve through the **Effect Manager** using `StatEffect` resources.
