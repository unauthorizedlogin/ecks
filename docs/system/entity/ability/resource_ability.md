# ⚔️ Ability Definition Architecture

## Overview

The Ability Definition provides the data-driven configuration for individual abilities.

Each ability is represented by an `AbilityDefinition` resource containing its identity, classification, activation rules, targeting configuration, casting behavior, requirements, effects, and presentation settings.

The resource defines **what an ability is and how it is configured**.

Runtime execution is handled separately by the Ability Runtime.

---

### 🧱 Ability Resource Architecture

Each ability is represented by an `AbilityDefinition`.

The resource contains:

* Ability identity
* Classification
* Activation configuration
* Trigger configuration
* Targeting rules
* Requirements
* Cast behavior
* Timing configuration
* Range restrictions
* Applied effects
* Animation data
* Visual effects
* Audio effects

Example:

```text
AbilityDefinition
│
├── Identity
├── Classification
├── Requirements
├── Cast Settings
├── Applied Effects
└── Animation & Audio
```

---

### 🏷️ Ability Identity

Abilities define their basic identity through:

* `ability_id`
* `display_name`
* `icon`
* `description`

The `ability_id` provides the unique identifier used by other systems when referencing the ability.

---

### 🧩 Ability Classification

Abilities can be classified using several independent configuration layers.

#### Ability Type

Defines the general ability behavior:

```text
ACTIVE
PASSIVE
TOGGLE
```

#### Ability Role

Defines the gameplay purpose:

```text
OFFENSE
DEFENSE
SUPPORT
UTILITY
```

#### Ability Category

Defines the broader ability classification:

```text
ATTACK
SPELL
REACTION
AURA
PROC
```

These classifications allow systems to query and organize abilities without requiring hardcoded ability-specific logic.

---

### 🚀 Activation

Abilities define how they enter execution:

```text
MANUAL
AUTO_ATTACK
TRIGGERED
PASSIVE
```

Triggered abilities additionally define a `TriggerType`.

Supported triggers include:

* On Hit
* On Critical Hit
* On Damage Taken
* On Kill
* Health Threshold
* Resource Threshold
* Status Applied
* Status Removed
* Timer

A `trigger_value` can provide an additional numeric threshold or timing value where required.

---

### 🎯 Targeting

Abilities define both the targeting mode and the valid target relationship.

Target modes include:

```text
SELF
TARGET
GROUND
DIRECTION
AREA
```

Target filters include:

```text
ANY
ALLIES
ENEMIES
```

This separates **how a target is selected** from **which entities are valid targets**.

---

### 📋 Requirements

Abilities can contain multiple `AbilityRequirement` resources.

```gdscript
@export var requirements: Array[AbilityRequirement] = []
```

Requirements provide the configuration layer for determining whether an ability can be used.

The ability definition stores the requirements but does not perform the runtime validation itself.

---

### ⏱️ Cast Behavior

Abilities support multiple execution timing models:

```text
INSTANT
CAST_TIME
CHANNEL
TOGGLE
```

The selected cast behavior determines how the Ability Runtime executes the ability.

```text
AbilityDefinition
       │
       ↓
Cast Behavior
       │
 ┌─────┼─────────────┐
 ↓     ↓             ↓
Instant Cast      Channel
       │
       └── Toggle
```

---

### ⏲️ Ability Timing

Abilities can define:

* Attack time
* Cooldown
* Cast time
* Channel time

These values provide the timing configuration used by the runtime execution layer.

---

### 🛑 Restrictions

Abilities can define execution restrictions including:

* Interruptibility
* Line-of-sight requirements

These settings allow abilities to describe basic execution constraints without embedding those rules directly into individual ability scripts.

---

### 📏 Targeting Range

Abilities support:

* Minimum range
* Maximum range
* Area radius

These values provide the spatial configuration required by targeting and runtime systems.

---

### ✨ Applied Effects

Abilities reference effects through effect IDs:

```gdscript
@export var effects: Array[String] = []
```

The Ability Definition does not contain the effect implementation.

Instead:

```text
AbilityDefinition
      │
      ↓
Effect IDs
      │
      ↓
Effect Database
      │
      ↓
StatEffect
```

This allows multiple abilities to reuse the same effects.

For runtime execution, see:

* [Ability Runtime Architecture](./ability_runtime.md)
* [Effect System Architecture → StatEffect Pipeline](../effect_system.md)

---

### 🎬 Animation & Presentation

Abilities can define presentation data including:

* Animation
* Animation speed
* Cast VFX
* Impact VFX
* Cast SFX
* Impact SFX

These values allow the ability definition to describe its presentation without owning the systems responsible for playing animations, visual effects, or audio.

---

### 🔗 System Integration

The Ability Definition acts as the configuration layer connecting several systems.

```text
Ability Definition
        │
        ├── Requirements
        │
        ├── Targeting
        │
        ├── Effects
        │
        ├── Animation
        │
        └── Audio
                │
                ↓
         Ability Runtime
                │
                ↓
          Gameplay Systems
```

Primary integrations include:

* Ability Runtime
* Ability Database
* Effect System
* Requirement System
* Targeting System
* Combat System
* Animation System
* VFX System
* Audio System

---

### 🏭 Data-Driven Design

Abilities are configured as resources rather than implemented as individual scripts.

This allows creators to modify:

* Ability classification
* Activation behavior
* Trigger conditions
* Targeting
* Timing
* Requirements
* Effects
* Presentation

without creating new runtime logic for every ability.

---

### 📦 Responsibilities

The Ability Definition:

✅ Stores ability identity
✅ Defines ability classification
✅ Defines activation and trigger configuration
✅ Defines targeting rules
✅ Stores ability requirements
✅ Defines cast behavior
✅ Stores timing and range configuration
✅ References applied effects
✅ Stores presentation configuration

The Ability Definition does **not**:

❌ Execute abilities
❌ Apply effects
❌ Resolve combat
❌ Validate runtime targets
❌ Manage cooldown state
❌ Play animations
❌ Play audio

Those responsibilities belong to the appropriate runtime and gameplay systems.

---

### Summary

The Ability Definition provides the **data layer for the ability system**.

It describes what an ability is, how it is classified, how it activates, what it can target, how it is timed, what effects it references, and how it is presented.

The configuration remains separate from execution, allowing the same Ability Runtime and supporting systems to execute a wide range of abilities without requiring ability-specific scripts.
