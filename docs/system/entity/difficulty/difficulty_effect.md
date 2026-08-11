# 🧬 Difficulty Effect Resource

The Difficulty Effect provides the simplified effect layer used by the Difficulty System to modify runtime statistics.

Difficulty effects use the standard `StatEffect` resource but are authored and generated specifically for difficulty configuration.

This provides a simpler authoring workflow while retaining compatibility with the existing Effect System.

---

## 🎯 Purpose

Difficulty effects are used to define stat modifiers such as:

* Increased HP
* Increased Damage
* Increased Armor
* Increased Resistances
* Reduced player statistics
* Other supported `StatBlock` modifiers

The Difficulty System does not implement separate stat modification logic.

Instead, generated effects are passed through the existing Effect System.

---

## ⚙️ Generation

Difficulty effects are stored in the difficulty effect database:

```text
data/databases/difficulty/effects/
```

The Difficulty Resource Generator scans this directory and builds an internal effect database using each effect's `effect_id`.

Difficulty CSV definitions can then reference effects by ID:

```text
difficulty_id,player_effects,enemy_effects
normal,,
hard,player_damage_penalty
nightmare,player_damage_penalty;player_resistance_penalty
```

Referenced effects are resolved during generation and assigned to the `DifficultyDefinition` resource.

---

## 🔄 Generation Pipeline

```text
Difficulty CSV
      │
      ▼
Difficulty Resource Generator
      │
      ├── Load Difficulty Effects
      │
      ├── Resolve Effect IDs
      │
      ▼
DifficultyDefinition
      │
      ▼
Generated .tres
      │
      ▼
Difficulty Database
```

The generator validates referenced effect IDs and warns when an effect cannot be resolved.

---

## 🧱 Effect Architecture

Difficulty effects remain standard `StatEffect` resources.

This means the Difficulty System can use the same underlying effect architecture as other gameplay systems while exposing only the configuration required for difficulty scaling.

```text
Difficulty Effect
      │
      ▼
StatEffect
      │
      ▼
EffectManager
      │
      ▼
StatManager
      │
      ▼
StatBlock
```

The simplified difficulty effect set is therefore an **authoring constraint**, not a separate effect implementation.

The underlying Effect System remains capable of supporting broader effect behavior if future difficulty requirements expand.

---

## 📋 Current Scope

Difficulty effects are currently used for **player stat modification**.

Enemy effects are supported by the resource and generator but are not currently active at runtime.
