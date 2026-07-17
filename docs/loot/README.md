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

# 📦 Item System

The Item System provides the foundation for all loot objects.

Responsibilities:

* Item resource definitions
* Item metadata
* Item categorization
* Stack rules
* Database references
* Runtime validation

All loot objects originate from Item Database definitions.

---

# 🎒 Inventory System

The Inventory System manages acquired item ownership before items enter specialized systems.

Responsibilities:

* Player item storage
* Item quantities
* Item movement
* Inventory persistence
* Equipment item storage before use

Equipment items are stored within inventory until the player chooses to equip them.

Example:

```text
Dropped Equipment
        |
        ↓
Inventory
        |
        ↓
Equip Action
```

---

# ⚔️ Equipment System

The Equipment System manages items after they have been equipped.

Responsibilities:

* Equipment slots
* Equip validation
* Unequip handling
* Equipped item persistence
* Stat integration

Equipment flow:

```text
Inventory
    |
    ↓
Equipment Manager
    |
    ↓
StatManager
    |
    ↓
Runtime Stats
```

Equipment bonuses are applied through the stat pipeline rather than directly modifying character values.

---

# 🗄️ Chest System

The Chest System manages world-based item containers.

Responsibilities:

* Chest identity
* Stored item data
* Loot persistence
* Player interaction
* Container state

Each chest maintains an independent storage state.

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

```
```
