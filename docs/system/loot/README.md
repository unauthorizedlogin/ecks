# 🎒 Loot System README

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

## **Purpose:**

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
  - Quest items
  - Miscellaneous items
  - Additional expandable categories
- Equipment and inventory management integrated into the same item pipeline

---

# 🧠 System Architecture

The Loot System is organized into four primary systems:

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

Each system owns its own runtime data and serialization.

---

# 📦 Chest Loot System

The Chest Loot System provides interactive world containers for item rewards, exploration rewards, and persistent player storage.

Chests are designed as world-based loot objects that connect gameplay interaction with the Loot System architecture.

The system currently supports two primary chest types:

- Dynamic Loot Chests
- Storage Chests

Both chest types share common interaction behavior while serving different gameplay purposes.

---

Documentation:

- [Chest Loot System](loot/chests.md)

---

## 🛡️ Equipment Manager

The Equipment Manager controls equipped item state after equipment has been moved from inventory into active equipment slots.

Responsibilities include:

- Equipment slot management
- Equip and unequip handling
- Equipment validation
- Equipment modifier processing
- Equipment effect synchronization
- Equipment serialization

Equipment integrates with the Stat and Effect systems to apply active bonuses from equipped items.

---

Documentation:

➡️ [Equipment Manager Documentation](loot/equipment_manager.md)

---

## 🎒 Inventory Manager

The Inventory Manager manages player item ownership and storage systems.

Responsibilities include:

- Player inventory slots
- Storage chest inventories
- Item transfers
- Stack management
- Inventory serialization

Equipment items are stored here when acquired and moved to the Equipment Manager after being equipped.

The Inventory Manager focuses on item ownership and storage. Other item categories will use dedicated managers as their systems expand.

---

Documentation:

📄 [Inventory Manager Documentation](loot/inventory_manager.md)

---

## 📦 Item Resource

The Item Resource is the foundation data definition for all item objects within the Loot System.

It provides the shared item structure used across:

- Equipment
- Inventory items
- Materials
- Consumables
- Quest items
- Future item categories

Item resources define:

- Item identity and classification
- Visual data
- Stack rules
- Economy values
- Equipment requirements
- Stat modifiers
- Runtime effects

All item systems reference Item Resources through the Item Database, allowing item behavior to remain data-driven and expandable.

---

Documentation:

📄 [Item Resource Documentation](loot/item_resource.md)

---

# 🧩 Item Category Managers

The Loot System is designed to expand beyond traditional inventory storage.

Future item categories will have dedicated managers responsible for:

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

This allows each item category to evolve independently without bloating the Inventory Manager.

