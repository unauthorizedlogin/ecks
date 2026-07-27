# 🎒 Loot System

## Overview

The Loot System provides the centralized item framework responsible for item definitions, ownership, storage, equipment handling, and world-based loot interactions.

The system is designed around a **data-driven item architecture**, separating:

- Item definitions
- Item ownership
- Inventory storage
- Equipment management
- World containers
- Category-specific item processing

The Loot System does not rely on a single universal inventory pipeline. Instead, item categories are handled through dedicated managers responsible for their own routing, behavior, and serialization.

## Purpose

- Unified item database supporting equipment, consumables, resources, currencies, and progression items
- Modular item category system supporting:
  - Ammunition
  - Consumables
  - Currency
  - Equipment
  - Gems
  - Ingredients
  - Materials
  - Metals
  - Minerals
  - Stones
  - Potions
  - Quest Items
  - Miscellaneous Items
  - Additional expandable categories
- Equipment and inventory management integrated through the same item pipeline

---

# 🧠 System Architecture

The Loot System is organized into dedicated item management systems:

```text
                         Item Database
                              |
                              ↓
                       Loot System Layer
                              |
        ┌─────────────┬──────────────┬─────────────┐
        ↓             ↓              ↓             ↓

      Items       Inventory       Chests       Equipment
        |             |              |             |
        ↓             ↓              ↓             ↓

 Item Managers   Inventory      Chest Manager  Equipment
                 Manager                       Manager


        |
        ↓

 Future Item Type Managers
 (Consumables, Currency, Ammo, etc.)
````

Each system owns its own runtime data and serialization while integrating through shared item resources and database pipelines.

---

# 🧩 Item Category Managers

The Loot System is designed to expand beyond traditional inventory storage.

Item categories are handled through dedicated managers responsible for:

* Routing
* Runtime handling
* Validation
* Serialization

Examples:

```text
Consumable
     |
     ↓
Consumable Manager
```

```text
Currency
     |
     ↓
Currency Manager
```

```text
Ammo
     |
     ↓
Ammo Manager
```

This allows each item category to evolve independently without creating a monolithic Inventory Manager.

---

# 🔗 Loot System Documentation

The following systems make up the Loot layer.

| System | Purpose | Documentation |
|---|---|---|
| 🧰 Chest Loot System  | World containers, storage chests, and interactive loot objects | [Chest Loot System](./loot/chests.md)            |
| 🛡️ Equipment Manager | Equipped item state, slot management, and equipment processing | [Equipment Manager](./loot/equipment_manager.md) |
| 🎒 Inventory Manager  | Item ownership, storage, transfers, and inventory management   | [Inventory Manager](./loot/inventory_manager.md) |
| 📦 Item Resource      | Core item definitions, metadata, and shared item structure     | [Item Resource](./loot/item_resource.md)         |

---

# Summary

The Loot System provides a scalable item framework through shared item resources, centralized databases, and modular runtime managers.

Each subsystem specializes in a specific item responsibility while allowing additional item categories to expand through dedicated management pipelines.
