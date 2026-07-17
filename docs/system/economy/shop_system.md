# 🛒 al_shop_manager.gd

## Overview

The **ShopManager** is the central authority for all merchant transactions within the framework.

It manages:

- Purchasing items
- Selling items
- Buyback inventory
- Currency validation
- Item routing
- Inventory integration
- Quest purchase objectives
- Transaction logging
- Save synchronization

All shop transactions flow through this manager.

---

# 🧠 Core Responsibilities

ShopManager controls:

- Current active shop context
- Purchase validation
- Item distribution
- Sell pricing
- Buyback history
- Merchant transaction events

The system provides a unified merchant pipeline independent of individual item categories.

---

# 🏪 Shop Context

## Active Shop Tracking

Each interaction stores the current merchant ID.

```gdscript
set_current_shop(shop_id)
````

Used for:

* Purchase tracking
* Quest objectives
* Merchant-specific logic
* Future reputation systems

Example:

```text
Player
 |
 ↓
Merchant NPC
 |
 ↓
ShopManager
 |
 ↓
Current Shop ID
```

---

# 🛒 Purchase System

## Purchase Pipeline

All purchases follow:

```text
Purchase Request
        |
        ↓
ShopManager
        |
        ↓
ItemDatabase Validation
        |
        ↓
Currency Check
        |
        ↓
Item Grant
        |
        ↓
Currency Removal
        |
        ↓
Save
        |
        ↓
Quest Notification
```

---

# 🔍 Purchase Validation

Before completing a purchase:

## Item Validation

Checks:

* Item exists
* Valid item data
* Price available

---

## Currency Validation

Checks:

* Player coin amount
* Transaction affordability

Failed purchases emit Event Viewer messages.

---

# 📦 Item Distribution Routing

ShopManager does not directly store items.

Instead, purchased items are routed to their owning managers.

---

# ⚔️ Equipment

Equipment purchases route through:

```gdscript
InventoryManager.add_item()
```

Supports:

* Weapons
* Armor
* Equipment items

---

# 🏹 Ammo

Ammo is routed through:

```text
AmmoManager
```

Supported:

* Arrows
* Bombs

---

# 🧪 Potions

Potion purchases route through:

```text
PotionManager
```

Supported:

* Health potions
* Mana potions
* Stamina potions

---

# 🍖 Consumables

Consumable purchases route through:

```text
ConsumableManager
```

Supported:

* Food
* Drinks
* Snacks

---

# 💎 Material Resources

ShopManager supports direct material acquisition.

Supported managers:

```text
MetalManager
GemManager
StoneManager
MineralManager
IngredientManager
```

This allows merchants to sell:

* Crafting materials
* Upgrade resources
* Gathering components

---

# 💰 Purchase Finalization

After successful item delivery:

ShopManager:

* Removes currency
* Updates inventory state
* Saves the game
* Logs purchase events
* Sends quest notifications

---

# 📜 Quest Integration

Purchases automatically notify the QuestManager.

Event:

```gdscript
ObjectiveDefinition.ObjectiveType.PURCHASE
```

Data provided:

```gdscript
{
    item_id,
    shop_id,
    amount
}
```

Supports:

* Purchase objectives
* Merchant quests
* Collection quests
* Trade objectives

---

# 📤 Sell System

## Sell Pipeline

```text
Sell Request
      |
      ↓
ShopManager
      |
      ↓
Item Validation
      |
      ↓
Inventory Removal
      |
      ↓
Currency Reward
      |
      ↓
Buyback Storage
      |
      ↓
Save
```

---

# 💸 Sell Pricing

Selling uses a configurable multiplier.

Default:

```gdscript
sell_multiplier = 0.5
```

Example:

```text
Item Value: 100 gold

Sell Price:
100 × 0.5

= 50 gold
```

Can be modified dynamically:

```gdscript
set_sell_multiplier()
```

---

# 🔁 Buyback System

ShopManager maintains a temporary merchant buyback inventory.

Features:

* Stores recently sold items
* Tracks resale value
* Allows item recovery
* Limited inventory size

Default:

```gdscript
BUYBACK_LIMIT = 10
```

---

# Buyback Pipeline

```text
Sold Item
    |
    ↓
Buyback List
    |
    ↓
Player Purchase
    |
    ↓
Inventory Restore
    |
    ↓
Currency Removal
```

---

# 📋 Buyback Storage

Each entry stores:

* Original item data
* Item ID
* Buyback price

Example:

```gdscript
{
    "id": "iron_sword",
    "price": 50,
    ...
}
```

---

# 🔄 Transaction Logging

ShopManager integrates with:

```text
EventManager
```

Events include:

## Purchase

```text
Purchased: Item Name
```

## Sale

```text
Sold item | Item Name
```

## Buyback

```text
Buyback completed | Item Name
```

Uses the LOOT event channel.

---

# 💾 Save Integration

Merchant transactions automatically synchronize with SaveManager.

Saved after:

* Purchases
* Sales
* Buybacks

Uses:

```gdscript
SaveManager.save_game()
```

---

# 🔔 UI Notification System

ShopManager exposes:

```gdscript
signal notify(message)
```

Used for:

* Invalid actions
* Failed transactions
* UI feedback

---

# 🧩 Architecture

```text
                 ShopManager
                      |
        ┌─────────────┼─────────────┐
        ↓             ↓             ↓
 ItemDatabase   CurrencyManager  InventoryManager
                      |
                      ↓
               Transaction Result
                      |
        ┌─────────────┼─────────────┐
        ↓             ↓             ↓
 QuestManager   EventManager    SaveManager


Purchase Routing:

ShopManager
     |
     ├── Equipment → InventoryManager
     ├── Ammo → AmmoManager
     ├── Potion → PotionManager
     ├── Consumable → ConsumableManager
     └── Materials → Resource Managers
```

---

# ✅ System Summary

The ShopManager provides a centralized merchant framework where all buying, selling, and recovery transactions are handled through a single authority.

The system is fully data-driven and supports expansion through additional item types, merchant rules, pricing modifiers, and future economy systems without changing the core transaction pipeline.
