# 🪙 Loot Currency Pickup (`LootCurrency`) — System Role

The **Loot Currency Pickup** is the world-facing currency collection controller. It represents currency drops within the game world and acts as the routing layer between physical pickups and centralized currency systems.

Currency pickups do not manage currency storage, wallet rules, economy logic, or inventory capacity directly. They identify the currency type being collected and forward the reward into the appropriate manager.

The pickup behavior itself is inherited from `PickupBase`.

📄 Related Documentation:

- [Pickup Base System](docs/system/loot/items/tools)

---

# 🪙 Currency Pickup Responsibilities

The Loot Currency Pickup is responsible for:

* Identifying the currency type through `item_id`
* Passing the collected amount into the correct currency system
* Triggering pickup feedback
* Removing itself after collection
* Providing a world representation of dropped currency

The pickup does **not** handle:

* Currency storage
* Currency limits
* Economy balancing
* Player inventory ownership
* Currency UI updates
* Transaction logic

Those responsibilities belong to the centralized currency systems.

---

# 🧬 Inheritance Architecture

`LootCurrency` extends:

```gdscript
extends PickupBase
````

The architecture follows the same pattern as other pickup systems:

```
PickupBase
      |
      |
LootCurrency
      |
      |
CurrencyManager
```

`PickupBase` provides the universal pickup pipeline:

```
World Pickup
      ↓
Detection
      ↓
Pickup Validation
      ↓
Pickup Event
      ↓
Child Routing
```

The child pickup only determines **what happens after collection**.

---

# 🔄 Currency Pickup Flow

When a player collects currency:

```
Player
   |
   ↓
Loot Currency Pickup
   |
   ↓
PickupBase validates pickup
   |
   ↓
LootCurrency._on_pickup()
   |
   ↓
Currency Type Routing
   |
   ↓
CurrencyManager
   |
   ↓
Player Currency Updated
```

---

# 🏷️ Currency Identification

Currency pickups use the inherited:

```gdscript
@export var item_id: String
```

to determine the currency type.

Example:

```
gold_coin
silver_coin
event_token
premium_currency
```

Current implementation:

```gdscript
match item_id:
    "gold_coin":
        CurrencyManager.add_coin(item_quantity)
```

The pickup only identifies the currency. The currency manager owns the actual reward operation.

---

# 💰 Current Currency Routing

Current supported currency:

| Currency ID | Destination                  |
| ----------- | ---------------------------- |
| `gold_coin` | `CurrencyManager.add_coin()` |

Example:

```
Gold Pickup
      |
      ↓
item_id = "gold_coin"
      |
      ↓
CurrencyManager.add_coin(amount)
      |
      ↓
Player Gold Updated
```

Unknown currency IDs are rejected:

```gdscript
push_warning("⚠️ Unknown currency ID")
```

This prevents invalid drops from silently entering the economy system.

---

# 📦 Currency Data Flow

Current pickup pipeline:

```
Loot Currency
      |
      |
 item_id
 item_quantity
      |
      ↓
Currency Router
      |
      ↓
CurrencyManager
      |
      ↓
Player Currency Storage
```

The pickup does not store currency values internally.

---

# 🔔 Pickup Feedback

After successful collection:

```
Currency Pickup
       |
       ├── Pickup Notification
       |
       ├── Pickup Event
       |
       └── Remove World Object
```

Visual and notification behavior is inherited from:

```
PickupBase
```

The currency pickup only confirms the reward was routed successfully.

---

# 🧠 Architecture Relationship

Current currency pickup architecture:

```
                 WORLD
                   |
                   |
          Loot Currency Pickup
                   |
          --------------------
          |                  |
     PickupBase        Currency Routing
          |                  |
 Pickup Lifecycle     CurrencyManager
                             |
                             ↓
                    Player Currency Data
                             |
                             ↓
                       EventManager
```

---

# 🔗 System Dependencies

| System          | Responsibility                                      |
| --------------- | --------------------------------------------------- |
| PickupBase      | Universal pickup lifecycle and interaction handling |
| LootCurrency    | Currency-specific routing                           |
| CurrencyManager | Currency ownership and modification                 |
| ItemDatabase    | Pickup metadata and display information             |
| EventManager    | Player feedback and logging                         |
| QuestManager    | Collection objective notifications                  |

---

# 🚧 Planned System Upgrades

The current currency pickup system is designed for expansion.

Future upgrades include:

---

## 🪙 Expanded Currency Types

Current:

```
gold_coin
```

Future:

```
gold_coin
silver_coin
guild_token
arena_token
event_currency
premium_currency
```

Currency types will no longer be limited to hardcoded routing cases.

---

## 📚 Data-Driven Currency Definitions

Current:

```gdscript
match item_id:
    "gold_coin":
        CurrencyManager.add_coin()
```

Future:

```
Currency ID
      ↓
Currency Definition
      ↓
Currency Manager Routing
      ↓
Currency Storage
```

This allows new currencies to be added without modifying pickup scripts.

---

## 🎒 Expanded Currency Storage

Future currency systems will support:

* Additional currency slots
* Larger currency inventories
* Multiple currency categories
* Account/world progression currencies
* Specialized reward currencies

---

# ✅ Current System Responsibilities

| System               | Responsibility                                |
| -------------------- | --------------------------------------------- |
| Loot Currency Pickup | Identifies dropped currency and routes reward |
| PickupBase           | Handles universal pickup behavior             |
| CurrencyManager      | Owns currency modification and storage        |
| ItemDatabase         | Provides pickup metadata                      |
| InventoryManager     | Handles item ownership where applicable       |
| EventManager         | Displays player feedback                      |
| QuestManager         | Processes collection objectives               |

---

# Overall

The **Loot Currency Pickup** is a lightweight world interaction layer that connects dropped currency objects to the centralized economy systems.

Its purpose is not to manage currency, but to identify and route currency rewards.

The architecture follows the same authority separation pattern used throughout the engine:

* PickupBase owns pickup behavior
* Child pickups own routing
* Managers own system state

Future currency expansion will replace hardcoded routing with a data-driven currency definition pipeline while preserving the same pickup architecture.
