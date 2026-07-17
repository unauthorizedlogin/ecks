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
