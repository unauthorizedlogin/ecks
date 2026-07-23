# ✨ Effect Database Architecture

## 🧠 Overview

The **Effect Database** is the central runtime repository for all reusable `StatEffect` resources within the engine.

Effects are fully data-driven assets generated from CSV into individual `.tres` resources, allowing designers to create, organize, and reuse gameplay effects without modifying gameplay code.

Unlike specialized effect systems (such as Difficulty Effects), the Effect Database exposes the complete `StatEffect` feature set, including stat modification, healing, damage-over-time, buffs, debuffs, revives, knockback, costs, triggers, targeting, durations, and additional metadata.

The database is responsible only for resource storage and retrieval. It does not execute effects or manage runtime state.

---

# Responsibilities

The Effect Database is responsible for:

* Loading all generated `StatEffect` resources.
* Providing fast lookup by Effect ID.
* Exposing all registered effects to gameplay systems.
* Acting as the central repository for reusable gameplay effects.
* Supporting export-safe loading through generated index files.

---

# Does Not

The Effect Database does **not**:

* Execute gameplay effects.
* Apply stat modifications.
* Manage durations or ticking effects.
* Process triggers.
* Track active buffs or debuffs.
* Validate gameplay rules.
* Manage stacking behavior.
* Handle cooldowns.
* Own runtime state.

Those responsibilities belong to runtime gameplay systems such as the Effect Manager, Ability System, Equipment System, Combat System, or other effect consumers.

---

# Resource Structure

Each generated resource is a `StatEffect`.

A StatEffect defines a complete gameplay effect including:

* Effect identifier
* Display name
* Category
* Subcategory
* Effect type
* Target stat
* Amount
* Percentage scaling
* Trigger type
* Target type
* Tags
* Duration
* Tick interval
* Knockback configuration
* Revive configuration
* Resource costs
* Cost payment behavior

These resources are intended to be shared across the engine and referenced by other data-driven systems.

---

# Initialization Flow

```
Engine Startup
        │
        ▼
EffectDatabase.initialize()
        │
        ▼
EffectResourceDatabase.load_directory()
        │
        ▼
effect_index.gd
        │
        ▼
Load every StatEffect resource
        │
        ▼
Register by Effect ID
        │
        ▼
Database Ready
```

Initialization occurs lazily on first access.

---

# Runtime Access

Primary runtime lookups include:

* `get_effect(effect_id)`
* `has_effect(effect_id)`
* `get_all_effects()`
* `get_all_ids()`

Gameplay systems retrieve immutable effect definitions from the database and execute them independently.

---

# Generated Resource Organization

Effects are organized into category and subcategory folders.

Example layout:

```text
effects/

├── aura/
│   └── support/
│       └── mana_aura.tres
│
├── buff/
│   ├── stats/
│   └── temporary/
│
├── damage/
│   ├── fire/
│   └── poison/
│
├── debuff/
│   ├── override/
│   └── temporary/
│
├── enchant/
│
├── heal/
│   ├── instant/
│   └── regen/
│
├── passive/
│
├── revive/
│
└── combat/
    └── knockback/
```

Folder structure is primarily organizational and has no impact on runtime behavior.

---

# Runtime Index

The generator produces an export-safe runtime index.

```
effect_index.gd
```

The index contains:

* `EFFECT_PATHS_ALL`

This master list guarantees deterministic loading and ensures all resources are included during export.

---

# Relationship to Other Systems

The Effect Database serves as a shared data source for numerous gameplay systems.

Typical consumers include:

* Ability System
* Equipment System
* Item System
* Potion System
* Enchantment System
* Combat System
* NPC Skills
* Quest Rewards
* Difficulty Modifiers
* Status Effect Manager

Each system references effects by ID rather than duplicating gameplay logic.

---

# Design Goals

The Effect Database follows several architectural principles:

* Data-driven gameplay.
* Reusable effect definitions.
* Centralized effect storage.
* Export-safe resource loading.
* Designer-friendly content creation.
* Separation of data and runtime logic.
* Shared effect assets across multiple gameplay systems.

---

# Benefits

This architecture provides:

* Single source of truth for gameplay effects.
* Reusable effect assets across the engine.
* Consistent behavior regardless of the originating system.
* Simplified content authoring through generators.
* Scalable organization for hundreds or thousands of effects.
* Deterministic loading suitable for exported projects.

The Effect Database functions as the engine's canonical repository for gameplay effect definitions, allowing multiple independent systems to reference the same effect resources while keeping execution logic separate from data.
