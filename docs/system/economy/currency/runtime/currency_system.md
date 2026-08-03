# 💰 al_currency_manager.gd

## Overview

The **CurrencyManager** is the centralized authority for all player currency within the framework.

It manages:

* Currency balances
* Currency spending
* Currency rewards
* Runtime currency state
* Save/load serialization
* UI update notifications

The manager provides a unified interface for every gameplay system that awards or consumes currency.

---

# 🧠 Core Responsibilities

CurrencyManager controls:

* Coin balances
* Gem balances
* Token balances
* Currency validation
* Currency transactions
* Runtime synchronization

The manager is intentionally lightweight and does not determine *why* currency changes occur—only *how* they are stored and updated.

---

# 💰 Supported Currency Types

The framework currently includes three built-in currencies.

```text
Coins
Gems
Tokens
```

Additional currencies can be added by extending the runtime balance dictionary and corresponding helper functions.

---

# 📦 Runtime Storage

All balances are maintained within a centralized runtime dictionary.

```gdscript
equipped = {
    coin,
    gem,
    token
}
```

This provides a single source of truth for every currency lookup.

---

# ➕ Currency Acquisition

Gameplay systems award currency through dedicated manager functions.

Examples:

```gdscript
add_coin()

add_gem()

add_token()
```

Sources may include:

* Quest rewards
* Enemy drops
* Shops
* Loot containers
* Events
* Future economy systems

CurrencyManager does not care where the reward originated.

---

# 💸 Spending Pipeline

Every purchase follows the same validation process.

```text
Spend Request
       |
       ↓
CurrencyManager
       |
       ↓
Balance Check
       |
       ↓
Enough Currency?
      / \
     /   \
   Yes    No
    |      |
    ↓      ↓
Remove   Reject
Currency Transaction
```

Transactions only succeed when sufficient currency exists.

---

# ✅ Transaction Validation

Before removing currency the manager verifies:

* Currency exists
* Balance is sufficient
* Requested amount is valid

Failed transactions return:

```gdscript
false
```

Successful transactions return:

```gdscript
true
```

This allows gameplay systems such as shops to safely validate purchases before continuing.

---

# 🔍 Runtime Queries

Other systems retrieve balances through dedicated getters.

```gdscript
get_coin_count()

get_gem_count()

get_token_count()
```

This prevents external systems from modifying runtime values directly.

---

# 🔔 UI Synchronization

CurrencyManager emits:

```gdscript
inventory_changed
```

whenever balances change.

This allows UI systems to refresh automatically.

Examples include:

* Currency HUD
* Shop menus
* Merchant interfaces
* Future banking or wallet systems

No polling is required.

---

# 💾 Save Integration

CurrencyManager serializes all runtime balances.

```gdscript
serialize_currency()
```

Produces:

```text
{
    coin,
    gem,
    token
}
```

During loading:

```gdscript
deserialize_currency()
```

restores every balance before notifying dependent UI systems.

---

# 🔗 Framework Integration

CurrencyManager is used throughout the economy systems.

Examples include:

* ShopManager
* QuestManager
* RewardManager
* SaveManager
* UI panels

Any gameplay system that awards or consumes currency communicates through this manager.

---

# 🧩 Architecture

```text
              CurrencyManager
                     |
      ┌──────────────┼──────────────┐
      ↓              ↓              ↓
  ShopManager   RewardManager   QuestManager
      |              |              |
      └─────── Currency Updates ────┘
                     |
                     ↓
              Runtime Balances
                     |
                     ↓
             inventory_changed
                     |
                     ↓
                Currency UI
                     |
                     ↓
               SaveManager
```

---

# ✅ System Summary

The **CurrencyManager** serves as the centralized authority for player currency within the framework.

It maintains runtime balances, validates transactions, synchronizes UI updates, and integrates with save data while remaining independent of the gameplay systems that award or consume currency. This separation keeps the economy modular and allows new currencies or reward systems to be introduced without changing the core transaction pipeline.
