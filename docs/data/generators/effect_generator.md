# ✨ Effect Resource Generator Architecture

## 🧠 Overview

The **Effect Resource Generator** converts a centralized CSV definition into individual `StatEffect` resources used throughout the engine.

Rather than creating gameplay effects manually in the Godot editor, designers define effects within a spreadsheet. The generator imports each row, constructs a fully configured `StatEffect` resource, saves it into the appropriate category folder, and rebuilds the runtime index used by the Effect Database.

This pipeline allows gameplay effects to remain entirely data-driven while supporting large libraries of reusable effects.

---

# Responsibilities

The Effect Resource Generator is responsible for:

* Reading the Effect CSV.
* Parsing effect definitions.
* Creating `StatEffect` resources.
* Assigning all gameplay configuration fields.
* Organizing resources into category folders.
* Saving `.tres` resources.
* Regenerating the Effect Index after generation.

---

# Does Not

The generator does **not**:

* Execute gameplay effects.
* Validate runtime behavior.
* Apply stat modifications.
* Handle combat.
* Manage active buffs or debuffs.
* Perform runtime lookups.
* Load effect resources during gameplay.

Its sole responsibility is asset generation.

---

# Generation Pipeline

```text
Effect CSV
      │
      ▼
Effect Resource Generator
      │
      ▼
Parse CSV Row
      │
      ▼
Create StatEffect Resource
      │
      ▼
Assign Effect Properties
      │
      ▼
Save .tres Resource
      │
      ▼
Repeat For Every Row
      │
      ▼
Generate Effect Index
      │
      ▼
Runtime Ready
```

---

# Input

The generator reads a single CSV file.

Each row represents one complete gameplay effect.

Typical data includes:

* Effect ID
* Display Name
* Category
* Subcategory
* Effect Type
* Target Stat
* Amount
* Percentage Mode
* Trigger Type
* Target Type
* Duration
* Tick Interval
* Tags
* Knockback Settings
* Revive Settings
* Cost Configuration

The CSV serves as the authoritative source for all generated effect resources.

---

# Generated Resource

Each CSV row produces one `StatEffect` resource.

Example:

```text
burning.tres
heal_small.tres
battle_resurrection.tres
armor_percent.tres
fire_weapon.tres
```

Every generated resource contains the full gameplay configuration required at runtime.

---

# Property Assignment

The generator separates resource construction into dedicated assignment stages.

## Core Properties

Assigns:

* Effect ID
* Display Name
* Target Stat
* Effect Type
* Amount
* Percentage Scaling

---

## Timing Properties

Assigns:

* Trigger Type
* Target Type
* Duration
* Tick Interval

---

## Cost Properties

Assigns:

* Cost Payment Mode
* HP Cost
* MP Cost
* Resource Cost
* Percentage Cost Flags

---

## Revive Properties

Assigns:

* Revive HP
* Revive MP
* Revive Resource
* Percentage Revive Flags

---

## Knockback Properties

Assigns:

* Knockback Force

---

## Tags

Parses semicolon-delimited tags from the CSV into the resource's tag array.

---

# Output Organization

Generated resources are organized automatically by category and subcategory.

Example:

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

This organization is generated entirely from CSV values and requires no manual folder management.

---

# Runtime Index Generation

After all resources have been written, the generator automatically executes the **Effect Index Generator**.

The index generator scans every generated effect resource and produces:

```text
effect_index.gd
```

containing:

```text
EFFECT_PATHS_ALL
```

This master index enables deterministic runtime loading and guarantees export-safe resource inclusion.

---

# Runtime Relationship

The generator is part of the content creation pipeline.

```text
CSV
   │
   ▼
Effect Resource Generator
   │
   ▼
Generated StatEffect Resources
   │
   ▼
Effect Index Generator
   │
   ▼
effect_index.gd
   │
   ▼
Effect Database
   │
   ▼
Gameplay Systems
```

The generator itself is never used during gameplay.

---

# Design Goals

The Effect Resource Generator was designed around several principles:

* Spreadsheet-driven content creation.
* One resource generated per effect.
* Automatic directory organization.
* Modular property assignment.
* Export-safe runtime loading.
* Minimal manual asset maintenance.
* Scalable support for large effect libraries.

---

# Benefits

This generation pipeline provides:

* Rapid creation of large effect databases.
* Consistent resource formatting.
* Centralized gameplay balancing through CSV editing.
* Automatic organization of generated assets.
* Elimination of repetitive manual resource creation.
* Seamless integration with the Effect Database and runtime index system.

The Effect Resource Generator serves as the authoring bridge between designer-managed spreadsheets and the engine's runtime effect resources, allowing hundreds or thousands of gameplay effects to be generated consistently from structured data.
