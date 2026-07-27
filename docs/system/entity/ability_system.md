# ⚡ Ability Manager

## Overview

The **Ability Manager** is the central authority for all ability execution within the framework.

It is responsible for validating ability usage, managing cooldowns, coordinating runtime execution, and driving the casting lifecycle.

The manager does **not** determine what an ability actually does. Instead, abilities are defined entirely through data and ultimately resolve into one or more `StatEffect` resources processed by the **Effect Manager**.

This includes:

* ⚔️ Offensive abilities
* 🛡 Defensive abilities
* 💚 Support abilities
* 🧰 Utility abilities
* ✨ Passive abilities
* 🔄 Toggle abilities
* ⏳ Cast-time abilities
* 📡 Channeled abilities

---

# 🧠 Core Responsibilities

The Ability Manager provides:

* Ability validation
* Requirement checking
* Cooldown management
* Runtime creation
* Ability lifecycle events
* Cast interruption
* Runtime cleanup

All gameplay abilities flow through the same execution pipeline regardless of their source.

---

# ⚙️ Initialization

```gdscript
func initialize()
```

Registers the Ability Manager as an active gameplay system.

Prevents duplicate initialization.

Output:

```
⚡ AbilityManager initialized
```

---

# 🎯 Ability Request Pipeline

Every ability begins with a cast request.

```
Ability Request
       ↓
Ability Database
       ↓
Requirement Validation
       ↓
Cooldown Validation
       ↓
Create Runtime
       ↓
Execute Ability
       ↓
Effect Manager
```

The manager serves as the gateway between gameplay input and runtime execution.

---

# ✅ Ability Validation

Before an ability can execute, the manager verifies that all requirements have been satisfied.

Supported validation includes:

* Class requirements
* Level requirements
* Stat requirements
* Quest completion
* Item ownership

If any requirement fails, the request is rejected before runtime creation.

---

# 📋 Requirement System

Ability requirements are evaluated individually using dedicated validation logic.

Supported requirement types include:

* None
* Class
* Level
* Stat
* Quest
* Item

This allows abilities to be gated by character progression, equipment, or world progression without embedding game logic inside the abilities themselves.

---

# ⏱️ Cooldown System

Cooldowns are tracked per caster and per ability.

Each cooldown entry is uniquely associated with:

```
Caster
      +
Ability
```

This allows multiple entities to use the same ability independently while maintaining their own cooldown timers.

Abilities without a cooldown bypass this system entirely.

---

# ⚙️ Runtime Management

Rather than executing abilities directly, the manager creates an **Ability Runtime** instance for every successful cast.

The runtime is responsible for:

* Cast timing
* Channel timing
* Effect execution
* Completion
* Interruption

The manager tracks all active runtimes until they complete or are interrupted.

---

# 📡 Ability Lifecycle Events

The manager broadcasts ability state changes throughout execution.

Supported lifecycle events include:

* Ability started
* Casting
* Channeling
* Finished
* Interrupted

These events allow animation, UI, audio, combat logging, and other systems to react without becoming tightly coupled to ability execution.

---

# 🚫 Ability Interruptions

Active abilities may be interrupted before completion.

Interrupting an ability immediately ends its runtime and prevents any remaining execution from occurring.

This supports gameplay such as:

* Movement interruption
* Crowd control
* Stuns
* Silence
* Manual cancellation

---

# 🔗 Ability Execution Pipeline

The Ability Manager does not apply gameplay effects directly.

Instead, execution follows a layered pipeline.

```
Ability Definition
        ↓
Ability Runtime
        ↓
Effect Manager
        ↓
Stat Effects
        ↓
Combat / Stats / Vitals
```

This separation keeps ability execution independent from combat calculations and stat modification.

---

# 🔄 Active Ability Tracking

Currently executing abilities are tracked through:

```gdscript
active_abilities
```

Each runtime maintains:

* Ability definition
* Caster
* Target
* Execution state
* Cast progress
* Channel progress
* Interruption state

This allows multiple abilities from multiple entities to execute simultaneously.

---

# 🔗 System Relationships

```
             Player Input
                  |
                  ↓
          AbilityManager
                  |
        ┌─────────┴─────────┐
        ↓                   ↓
 Requirement Check     Cooldown Check
        |                   |
        └─────────┬─────────┘
                  ↓
           Ability Runtime
                  |
                  ↓
          Ability Definition
                  |
                  ↓
           Effect Manager
                  |
      ┌───────────┼───────────┐
      ↓           ↓           ↓
 Combat      StatManager   VitalComponent
```

---

# ✅ Design Rule

**AbilityManager is the single authority for ability execution.**

No gameplay system should bypass the manager to execute abilities directly.

All abilities—whether active, passive, toggle, instant, cast-time, or channeled—should be represented as `AbilityDefinition` resources, validated by the Ability Manager, executed through an `AbilityRuntime`, and resolved into gameplay effects through the **Effect Manager**.

---

# ⚡ Ability Definition (`resource_ability.gd`)

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
