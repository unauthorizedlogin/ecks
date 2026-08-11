## ⚔️ Difficulty Definition

Difficulty definitions provide `StatEffect` resources which are applied to players and enemies through the normal effect and stat pipelines.

---

### ⚠️ Current Development Status

The Difficulty System currently supports runtime stat modification through the Effect System.

Current functionality includes:

* ✅ Difficulty resource structure
* ✅ Player and enemy effect definitions
* ✅ Effect System integration
* ✅ Runtime StatBlock modification
* ✅ Difficulty-based stat modifiers

Additional modifier fields exist in the resource as reserved configuration for future systems.

---

## 🎯 Core Design

Difficulty is not an isolated stat or modifier system.

Instead, it acts as a **configuration layer** that supplies effects to existing gameplay systems.

Difficulty Definitions provide:

* Player stat effects
* Enemy stat effects
* Reserved economy modifiers
* Reserved progression modifiers
* Reserved spawning modifiers
* Reserved death penalties

These values are stored as data resources, allowing new difficulties to be created without modifying gameplay code.

---

# 🧬 Effect System Integration

Difficulty uses the **Effect System** as its primary stat modification pipeline.

Difficulty does not directly modify runtime stats.

Instead, it provides `StatEffect` resources that are processed through the existing effect architecture and applied to the character's `StatBlock`.

This allows difficulty modifiers to work alongside:

* Equipment modifiers
* Temporary buffs
* Debuffs
* Abilities
* Other gameplay effects

Example:

```text
Difficulty: Nightmare

Enemy Effects:
    +50% HP
    +35% Damage
    +25% Resistance

↓

Effect System

↓

Enemy StatBlock

↓

Combat / Gameplay Systems
```

The `StatBlock` contains a broad range of modifiable runtime statistics, including primary attributes, resources, regeneration, combat statistics, defenses, resistances, attack speed, movement, and reward-related modifiers such as `GoldFind`, `MagicFind`, and `XPGain`.

---

# 🎯 Player Effects

Difficulty can modify player statistics through:

```gdscript
@export var player_effects: Array[StatEffect] = []
```

These effects can modify any supported `StatBlock` value through the existing Effect System.

---

# 👾 Enemy Effects

Enemy scaling is the primary current use of the Difficulty System.

Configured through:

```gdscript
@export var enemy_effects: Array[StatEffect] = []
```

Enemy effects can modify supported runtime statistics such as:

* Health
* Damage
* Armor
* Resistances
* Attack speed
* Movement
* Regeneration

---

# 🧩 Reserved Modifiers

`DifficultyDefinition` also contains configuration fields reserved for future systems.

### Player Modifiers

* Vendor price multiplier
* Repair cost multiplier
* Reputation multiplier

### Enemy Modifiers

* Enemy density multiplier
* Elite spawn multiplier
* Champion spawn multiplier
* Death XP loss
* Death gold loss

These fields are currently **unused** and do not participate in the active difficulty runtime pipeline.

---

# Current Implementation Summary

* ✅ Data-driven `DifficultyDefinition` resource
* ✅ Player / Enemy `StatEffect` containers
* ✅ Effect System integration
* ✅ Runtime StatBlock modification
* ✅ Broad character stat modifier support
* 📦 Reserved economy, progression, spawning, and death modifier fields

The Difficulty System currently provides an **effect-driven stat scaling layer**, with the existing Effect and Stat systems handling the actual runtime modification work.
