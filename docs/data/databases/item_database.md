# 📦 Item Database Architecture

## Overview

The Item Database provides a centralized runtime access layer for all item resources within the engine.

It acts as the single entry point for gameplay systems requiring item information, while separating resource loading, indexing, and runtime queries into independent layers.

The system supports all item categories through a unified database pipeline:

* Equipment
* Weapons
* Armor
* Accessories
* Potions
* Ammo
* Currency
* Quest Items
* Tools
* Gems
* Unique Items
* Future expandable item categories

The architecture removes direct filesystem dependency from gameplay systems by using generated resource indexes.

---

# 🏗️ Architecture Structure

```
ItemDatabase (Autoloader)
        |
        |
        v
ItemResourceDatabase
        |
        |
        v
Generated Item Index
        |
        |
        v
ItemResource (.tres)
```

---

# 🧠 ItemDatabase

## Responsibilities

`al_item_database.gd` is the gameplay-facing interface.

It is responsible for:

* Initializing the item resource database
* Providing item lookup access
* Exposing filtered item collections
* Returning standardized item data dictionaries

It does **not**:

* Scan directories
* Handle filesystem operations
* Load individual resources
* Manage inventory logic
* Manage item spawning
* Handle equipment calculations

---

# 🚀 Initialization Pipeline

When initialized:

1. ItemDatabase starts the loading process.
2. ItemResourceDatabase loads the generated item index.
3. Every indexed `.tres` item resource is loaded.
4. Resources are registered into runtime caches.
5. Gameplay systems gain access through ItemDatabase.

Example initialization:

```
ItemDatabase.initialize()

        ↓

ItemResourceDatabase.load_directory()

        ↓

item_index.gd

        ↓

ItemResource registration

        ↓

Runtime lookup available
```

---

# 📥 Item Lookup API

## Single Item Access

Gameplay systems request item data through:

```
get_item_data(item_id)
```

This provides a standardized dictionary containing:

* Display information
* Item category
* Item subtype
* Equipment requirements
* Level requirements
* Scaling information
* Rarity
* Price
* Stack information
* Icon reference
* Description
* Modifiers
* Effects
* Raw resource reference

This prevents gameplay systems from depending directly on resource implementation.

---

# 🗂️ Resource Database

## ItemResourceDatabase Responsibilities

The resource database handles:

* Loading indexed item resources
* Registering items
* Maintaining lookup maps
* Building category hierarchies

It maintains two primary caches:

---

## Item Map

Direct lookup by item ID.

Example:

```
item_map["sword_rusty"]
        |
        v
ItemResource
```

Used for:

* Inventory lookup
* Loot generation
* Equipment loading
* Quest rewards

---

## Category Map

Organized item browsing structure.

Example:

```
category_map

equipment
 ├── mage
 │     ├── witch_hat
 │     └── witch_robe
 │
 ├── warrior
 │     ├── knights_armor
 │     └── knights_helm


potions
 ├── health
 ├── mana
 └── stamina
```

Used for:

* Shop systems
* Filtering
* Item browsers
* Loot tables

---

# 📦 Resource Registration

Every item resource is validated before registration.

Validation includes:

## Missing ID Protection

Resources without:

```
item_id
```

are rejected.

---

## Duplicate Protection

Duplicate IDs are prevented:

```
Duplicate item_id: sword_rusty
```

This guarantees every item has a unique runtime identifier.

---

# 🗂️ Generated Item Index

## item_index.gd

The generated index contains every item resource path.

Example:

```
const ITEM_PATHS := [

"res://data/databases/items/weapons/sword_rusty.tres",

"res://data/databases/items/potions/health/potion_health_small.tres"

]
```

---

## Purpose

The generated index provides:

* Deterministic loading
* Faster startup
* Export-safe resource discovery
* No runtime directory scanning
* Platform-independent resource access

---

## Generated Workflow

```
Item Resource Created

        ↓

Item Index Generator

        ↓

item_index.gd

        ↓

ItemDatabase Startup

        ↓

Runtime Cache
```

---

# 🔎 Query Examples

## Check Item Exists

```
ItemDatabase.has_item("sword_rusty")
```

---

## Get Item

```
ItemDatabase.get_item_data("potion_health_small")
```

---

## Get Category

```
ItemDatabase.get_category("weapons")
```

---

## Get Subcategory

```
ItemDatabase.get_subcategory(
    "equipment",
    "mage"
)
```

---

# 🔧 Expansion Support

The database is designed for future item growth.

Adding new item categories requires:

1. Creating new ItemResource files
2. Placing them in the appropriate database folder
3. Running the index generator

No database code changes are required.

---

# ✅ Design Benefits

The Item Database provides:

* Centralized item authority
* Fast runtime lookup
* Export-safe resource loading
* Category filtering
* Duplicate protection
* Generator-driven scalability
* Separation between content creation and runtime systems

The result is a scalable item architecture capable of supporting large ARPG inventories, loot systems, shops, crafting, and progression systems without increasing runtime complexity.
