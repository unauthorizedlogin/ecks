```

## 🛒 Shop Vendor (`ShopVendor / VendorAssignor`) — System Role

The **Shop Vendor** is the world-facing vendor controller. It acts as the bridge between the physical NPC/world object and the centralized shop systems. Vendors do not handle purchasing, selling, currency, or inventory transactions directly — they configure and open the shop interface through `ShopManager`.

### Core Responsibilities

* Defines a shop identity (`shop_id`)
* Defines available merchandise (`item_ids`)
* Provides vendor-specific pricing modifiers
* Provides localized/customizable shop UI labels
* Opens and closes the shop interface
* Builds the vendor inventory payload
* Passes vendor configuration into the shop pipeline

---

# 🏪 Vendor Configuration Layer

Each vendor instance can define:

### Shop Identity

```gdscript
@export var shop_id: String = ""
```

Provides unique vendor ownership for:

* Quest purchase tracking
* Shop-specific logic
* Future reputation/faction systems
* Vendor progression

Example:

```
Blacksmith_01
PotionVendor_Town
GuildMerchant_Main
```

---

# 📦 Vendor Stock Management

Vendors maintain their own item catalog:

```gdscript
@export var item_ids: Array[String] = []
```

The vendor does not contain item data.

Instead:

```
Vendor
  ↓
Item IDs
  ↓
ItemDatabase
  ↓
Shop Inventory Payload
  ↓
Shop Menu
```

This keeps vendor scenes lightweight and database driven.

---

# 💰 Vendor Economy Modifiers

Each vendor controls economic rules:

```gdscript
sell_price_percent
buyback_price_percent
```

Example:

Normal Merchant:

```
Player sells:
50%

Player buys back:
50%
```

Rare Merchant:

```
Player sells:
75%

Buyback:
90%
```

These are passed into:

```gdscript
ShopManager.set_sell_multiplier()
ShopManager.set_buyback_multiplier()
```

The vendor controls the rules, but ShopManager executes the economy.

---

# 🏷️ Dynamic Shop Titles

Vendors support large-scale localization/customization.

Example:

```gdscript
Blacksmith
Armorer
Weapon Vendor
Forge Master
Swordsmith
```

Uses:

```gdscript
ShopTitle enum
```

with optional overrides:

```gdscript
use_custom_shop_title
custom_shop_title
```

Pipeline:

```
Vendor Enum
      ↓
SHOP_TITLE_KEYS
      ↓
Localized Display
      ↓
Shop UI
```

---

# 📋 Dynamic Shop Labels

Vendor controls all shop-facing text:

## Vendor Inventory

Examples:

```
Shop Items:
Merchant Wares:
Featured Goods:
Rare Items:
```

---

## Player Sell Inventory

Examples:

```
Your Inventory:
Sellable Items:
Player Goods:
Equipment To Sell:
```

---

## Buyback Section

Examples:

```
Buyback Items:
Recently Sold:
Repurchase List:
```

---

## Item Information

Examples:

```
Item Info:
Description:
Item Effects:
Item Stats:
Lore:
```

---

All labels are prepared for localization instead of hardcoded UI text.

---

# 🔄 Vendor Interaction Flow

## Player enters range

```gdscript
_on_body_entered()
```

Registers:

```gdscript
player_in_range
```

---

## Player opens shop

```gdscript
open_shop(player)
```

Flow:

```
Player
 ↓
Vendor
 ↓
ShopManager.set_current_shop()
 ↓
Build Vendor Inventory
 ↓
Apply Vendor Economy
 ↓
Open Shop UI
```

---

# 🛒 Inventory Generation

Vendor builds its stock dynamically:

```gdscript
_build_vendor_inventory()
```

Process:

```
item_ids
    ↓
ItemDatabase
    ↓
Duplicate Item Data
    ↓
Attach ID
    ↓
Create Shop Entry
```

Output:

```gdscript
[
 {
   item: item_data,
   price: 100
 }
]
```

This keeps the shop UI completely database-driven.

---

# 🧠 Architecture Relationship

Current shop architecture:

```
                WORLD
                  |
                  |
             Shop Vendor
                  |
        -------------------
        |                 |
 Shop Configuration   Shop Opening
        |                 |
        ↓                 ↓
 ItemDatabase       ShopManager
                         |
        --------------------------------
        |              |               |
 CurrencyManager InventoryManager Buyback
        |
        ↓
      EventManager
```

---

# ✅ Current System Responsibilities

| System           | Responsibility                       |
| ---------------- | ------------------------------------ |
| Shop Vendor      | Physical merchant configuration      |
| ShopManager      | Buying/selling transaction authority |
| ItemDatabase     | Item definitions                     |
| InventoryManager | Player item ownership                |
| CurrencyManager  | Gold transactions                    |
| Shop UI          | Presentation only                    |
| EventManager     | Player feedback/logging              |

---

# Overall

The vendor system is now a **data-driven merchant definition layer**.

Vendors are essentially configurable shop terminals:

* no transaction logic
* no currency logic
* no inventory ownership logic
* no UI ownership

They define **who the merchant is**, **what they sell**, and **how they appear**, while the managers handle the actual game systems. This matches the same authority separation pattern used in the quest/combat/effect systems.
