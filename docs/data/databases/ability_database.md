# ✨ Ability Database Architecture

## 🧠 Overview

The **Ability Database** is the central runtime repository for all reusable `AbilityDefinition` resources within the engine.

Abilities are fully data-driven assets generated into individual `.tres` resources and registered through an export-safe runtime index. This allows gameplay abilities to be created, organized, and expanded without requiring modifications to ability execution logic.

The Ability Database separates **ability data ownership** from **ability behavior execution**. The database stores complete ability definitions while runtime systems such as the Ability Manager are responsible for validation, casting, cooldowns, targeting, and effect execution.

The database supports scalable ability content through generated resource paths, deterministic loading, and centralized lookup by Ability ID.

---

# Responsibilities

The Ability Database is responsible for:

* Loading all generated `AbilityDefinition` resources.
* Registering abilities by unique Ability ID.
* Providing runtime lookup access for ability definitions.
* Exposing all available abilities to gameplay systems.
* Validating loaded resources during initialization.
* Supporting export-safe loading through generated index files.
* Maintaining a centralized repository for reusable ability definitions.

---

# Does Not

The Ability Database does **not**:

* Execute abilities.
* Handle casting behavior.
* Validate caster requirements.
* Manage cooldowns.
* Track active abilities.
* Apply ability effects.
* Process targeting logic.
* Handle animation playback.
* Manage resource costs.
* Track runtime ability state.
* Control AI ability decisions.

Those responsibilities belong to runtime gameplay systems such as the Ability Manager, Combat System, Entity System, Effect System, or other ability consumers.

---

# Resource Structure

Each generated resource is an `AbilityDefinition`.

An AbilityDefinition represents a complete ability configuration including:

* Ability identifier
* Display name
* Icon reference
* Description
* Category
* Subcategory
* Ability classification
* Effect references
* Targeting configuration
* Cost configuration
* Cooldown configuration
* Runtime behavior metadata

These resources are intended to be shared across entities, classes, NPCs, equipment, and other gameplay systems requiring ability access.

---

# Initialization Flow

```
Engine Startup
        │
        ▼
AbilityDatabase.initialize()
        │
        ▼
AbilityResourceDatabase.load_directory()
        │
        ▼
ability_index.gd
        │
        ▼
Load registered AbilityDefinition resources
        │
        ▼
Validate resources
        │
        ▼
Register by Ability ID
        │
        ▼
Ability Database Ready
```

Initialization occurs lazily when the database is first accessed.

The database ensures duplicate IDs, invalid resources, and missing identifiers are detected during loading.

---

# Runtime Access

Primary runtime lookups include:

* `get_ability(id)`
* `has_ability(id)`
* `get_all_abilities()`
* `get_all_ids()`

Gameplay systems retrieve immutable ability definitions from the database and execute ability behavior independently.

Abilities are referenced through their unique identifier rather than direct resource paths, allowing systems to remain decoupled from content organization.

---

# Generated Resource Organization

Abilities are organized into category and subcategory folders.

Example layout:

```text
abilities/

├── Holy/
│   └── Single/
│       └── heal.tres
│
├── Magic/
│   └── Fire/
│       └── fireball.tres
│
└── Warrior/
    └── Core/
        └── whirlwind.tres
```

Folder structure provides content organization and designer readability.

Runtime behavior is determined by the resource data itself and not by the folder hierarchy.

---

# Runtime Index

The generator produces an export-safe runtime index.

```
ability_index.gd
```

The index contains:

```gdscript
ABILITY_PATHS_ALL
```

This master resource list provides deterministic loading of all generated ability resources.

The index ensures generated `.tres` files are discoverable during exported builds without relying on runtime directory scanning.

---

# Resource Loading Validation

During initialization, each ability resource is validated before registration.

Validation includes:

* Resource successfully loaded.
* Resource inherits from `AbilityDefinition`.
* Ability contains a valid Ability ID.
* Ability ID does not already exist.

Invalid resources are rejected and reported during database loading.

---

# Relationship to Other Systems

The Ability Database serves as the primary data source for systems requiring access to ability definitions.

Typical consumers include:

* Ability Manager
* Entity System
* Class System
* NPC Skill System
* Combat System
* AI System
* Equipment System
* Quest Reward System
* Progression Systems

Each system references abilities through their unique ID rather than duplicating ability configuration data.

---

# Design Goals

The Ability Database follows several architectural principles:

* Data-driven ability creation.
* Centralized ability storage.
* Separation of ability data and execution logic.
* Reusable ability definitions across systems.
* Export-safe deterministic loading.
* Generator-driven content pipelines.
* Scalable organization for large ability libraries.
* Runtime independence from file structure.

---

# Benefits

This architecture provides:

* Single source of truth for ability definitions.
* Simplified ability creation through generated resources.
* Consistent ability references across gameplay systems.
* Reduced coupling between content and runtime logic.
* Scalable support for hundreds or thousands of abilities.
* Reliable loading behavior in exported projects.
* Easy expansion through CSV-driven content pipelines.

The Ability Database functions as the engine's canonical repository for ability definitions, allowing multiple gameplay systems to share the same ability data while keeping execution behavior isolated within runtime systems.
