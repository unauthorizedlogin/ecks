# 🎒 Inventory Manager

## Overview

The Inventory Manager is the central runtime system responsible for item ownership, storage, transfers, and inventory state management.

It manages:

- Player inventory storage
- Storage chest inventories
- Item addition and removal
- Item consumption
- Item transfers
- Inventory serialization
- Inventory state updates

The system maintains item quantities through item identifiers and uses item definitions from the Item Database for validation and metadata.

---

# Inventory Structure

```

InventoryManager
│
├── Player Inventory
│   └── item_id
│       ├── name
│       ├── amount
│       ├── stackable
│       └── item_type
│
├── Storage Inventories
│   └── chest_id
│       └── item_id
│           ├── name
│           ├── amount
│           ├── stackable
│           └── item_type
│
└── Focus State
├── focused_inventory
└── focused_chest

```

---

# Inventory Limits

## Player Inventory

The player inventory uses a maximum slot limit.

```

MAX_SLOTS = 64

```

Slots represent unique item entries, not total item quantity.

Example:

```

Health Potion x50

```

uses:

```

1 slot

```

not:

```

50 slots

```

---

# Item Storage

Items are stored using their item ID as the dictionary key.

Example:

```

inventory = {
"iron_sword": {
"name": "Iron Sword",
"amount": 1,
"stackable": false,
"item_type": "weapon"
}
}

```

---

# Item Operations

## Add Item

Adds an item entry to inventory.

Behavior:

- Validates item through Item Database
- Checks item type restrictions
- Merges stackable items
- Creates new entries when needed
- Emits inventory update signals

---

## Remove Item

Removes a specified quantity of an item.

Behavior:

- Validates item exists
- Validates available quantity
- Reduces stack amount
- Removes empty entries

---

## Consume Item

Uses an item against a target.

Behavior:

- Retrieves item effects
- Passes effects to the effect system
- Applies item behavior to the target

---

# Stack System

Stackable items merge automatically.

Example:

Before:

```

Potion x10

```

Adding:

```

Potion x5

```

Result:

```

Potion x15

```

Non-stackable items create separate entries.

---

# Storage System

Storage chests maintain independent inventories.

Each chest is identified by a unique chest ID.

Structure:

```

storage_chests
│
└── chest_id
│
└── item_id
└── item data

```

---

# Inventory Transfers

The manager supports transfers between:

```

Player Inventory
↕
Storage Inventory

```

Transfer behavior:

- Removes item from source
- Adds item to destination
- Restores original state if transfer fails

---

# Inventory Focus

The manager tracks the currently active inventory context.

Supported inventory types:

| Type | Description |
|---|---|
| PLAYER | Player inventory |
| STORAGE | Storage chest inventory |

Additional state:

| Property | Description |
|---|---|
| focused_chest | Currently opened storage chest |

---

# Signals

## inventory_changed

Emitted when inventory state changes.

Used for:

- Inventory refresh
- State synchronization

---

## item_acquired

Emitted when an item is added.

Parameters:

```

item_id
amount

```

---

## item_removed

Emitted when an item is removed.

Parameters:

```

item_id
amount

```

---

# Serialization

The Inventory Manager supports saving and loading inventory state.

Serialized data contains:

```

{
inventory,
storage_chests
}

```

Deserialization restores:

- Player inventory
- Storage chest inventories

After loading:

```

inventory_changed

```

is emitted to refresh connected systems.

---

# Design Rules

- Inventory stores item ownership, not item definitions.
- Item data is retrieved through Item Database.
- Stack rules are controlled by item definitions.
- Storage inventories are independent from player inventory.
- Transfers preserve state consistency.
- Equipment items are filtered through inventory restrictions.

# 🎒 Inventory & Equipment System

## Overview

The Inventory & Equipment System manages item ownership, storage, equipment slots, item transfers, and equipment-driven stat changes.

It provides the framework layer responsible for:

- Player item storage
- Storage chest inventories
- Item stacking and removal
- Equipment slot management
- Equip / unequip workflows
- Equipment validation
- Equipment stat application
- Equipment effect synchronization
- Inventory and equipment serialization

The system separates item ownership from equipment state while keeping both synchronized through centralized managers.

---

# 📦 Item Resource

`ItemResource` defines the shared data structure for all items within the framework.

Supported item categories include:

- Equipment
- Consumables
- Potions
- Ammo
- Currency
- Gems
- Ingredients
- Metals
- Minerals
- Stones
- Quest items
- Miscellaneous items

## Item Identity

Items contain:

- Item ID
- Display name
- Description
- Item category
- Item type
- Category metadata

This allows systems to reference items through stable identifiers instead of direct object references.

---

# ⚔️ Equipment Data

Equipment items support additional classification data:

## Equipment Slots

Supported equipment locations include:

- Head
- Shoulders
- Arms
- Hands
- Body
- Waist
- Legs
- Feet
- Weapon
- Offhand
- Wrist
- Neck
- Ring
- Back
- Unique
- Custom Slots

## Weapon Types

Supported weapon categories include:

- Axe
- Bow
- Crossbow
- Dagger
- Gun
- Hammer
- Instrument
- Mace
- Scythe
- Shield
- Spear
- Staff
- Sword
- Totem
- Wand
- Unarmed

## Armor Types

Supported armor categories include:

- Cloth
- Light
- Medium
- Heavy

---

# 🎒 Inventory Manager

`InventoryManager` controls item ownership and storage.

## Responsibilities

- Add items
- Remove items
- Track item quantities
- Manage player inventory
- Manage storage chest inventories
- Transfer items between inventories
- Track inventory changes
- Serialize inventory state

---

# 📦 Inventory Structure

Inventory uses a dictionary-based structure:

```

item_id → item data + quantity

```

This allows:

- Fast item lookup
- Stack management
- Save/load support
- Quest and gameplay event integration

---

# 🔢 Stack Management

Stackable items automatically combine when possible.

Inventory limits are based on:

- Number of unique item entries
- Not total item quantity

Example:

```

Iron Ore x500

```

uses one inventory slot.

---

# 🧰 Storage System

Storage inventories are independently tracked.

Features:

- Multiple chest inventories
- Player ↔ storage transfers
- Separate storage state tracking
- Persistent storage serialization

---

# 🔄 Inventory Transfers

Transfers between player inventory and storage are handled through controlled operations.

Supported actions:

- Move item to storage
- Move item to player
- Validate ownership
- Preserve item counts

Failed transfers support rollback protection.

---

# 🛡️ Equipment Manager

`EquipmentManager` controls equipped item state.

## Responsibilities

- Equip items
- Unequip items
- Manage equipment slots
- Validate restrictions
- Apply equipment modifiers
- Apply equipment effects
- Synchronize equipment state

---

# ⚙️ Equip Validation

Before equipment is applied, the system validates:

- Item ownership
- Equipment slot availability
- Required level
- Maximum level restrictions
- Required class
- Weapon restrictions
- Armor restrictions

---

# 🔁 Equipment Synchronization

Equipment changes use a centralized rebuild process.

When equipment changes:

1. Existing equipment effects are cleared
2. Equipment modifiers are rebuilt
3. Stats are recalculated
4. Runtime player stats are refreshed
5. Equipment effects are reapplied

This ensures equipment remains the authoritative source for equipment-based stat changes.

---

# 📊 Equipment Modifiers

Equipment supports stat modifiers through item data.

Modifiers can contain:

- Flat values
- Percentage values

Multiple equipped items are merged into a combined equipment modifier set before being applied.

---

# ✨ Equipment Effects

Equipment can apply runtime effects through the effect system.

Examples:

- Stat bonuses
- Passive effects
- Equipment-based modifiers

Effects are synchronized from currently equipped items.

---

# 📡 Signals

## Inventory Events

### `inventory_changed`

Used for:

- Inventory UI updates
- Save synchronization

### `item_acquired`

Broadcast when items are added.

Used for:

- Quest tracking
- Progression hooks

### `item_removed`

Broadcast when items leave inventory.

Used for:

- Quest tracking
- Progression hooks

---

# 💾 Serialization

Both inventory and equipment support save/load serialization.

## Inventory Saves

Stores:

- Player inventory
- Storage inventories

## Equipment Saves

Stores:

- Equipped slot assignments

This allows complete restoration of player item state.

---

# 🔗 System Relationship

```

ItemResource
|
|
ItemDatabase
|
|
+-------------+
| Inventory   |
| Manager     |
+-------------+
|
|
+-------------+
| Equipment   |
| Manager     |
+-------------+
|
|
Stat / Effect Systems

```

The Inventory System manages **ownership**.

The Equipment System manages **active item usage**.

Together they provide the framework's complete item management layer.


# 📦 Item Resource

## Overview

The Item Resource is the base data definition for all item objects.

It stores item identity, classification, equipment information, requirements, economy values, stack rules, stat modifiers, and effects.

---

# Resource Structure

```

ItemResource
│
├── Visual
│   ├── icon
│   ├── name
│   └── description
│
├── Identity
│   ├── item_id
│   ├── type
│   ├── item_type
│   ├── category
│   └── subcategory
│
├── Equipment
│   ├── equip_slot
│   ├── armor_type
│   └── weapon_type
│
├── Requirements
│   ├── required_class_id
│   ├── required_level
│   ├── max_level
│   └── level_scaling
│
├── Economy
│   ├── price
│   └── rarity
│
├── Rules
│   └── stackable
│
├── Stats
│   └── modifiers
│
└── Effects
└── effects

```

---

# Visual Data

| Property | Description |
|---|---|
| icon | Item display icon |
| name | Item display name |
| description | Item description text |

---

# Identity Data

| Property | Description |
|---|---|
| item_id | Unique item identifier |
| type | Item category enum |
| item_type | String-based item type identifier |
| category | Item category grouping |
| subcategory | Item subgroup |

---

# Item Types

| Type | Purpose |
|---|---|
| AMMO | Ammunition items |
| CONSUMABLE | Consumable items |
| CURRENCY | Currency items |
| EQUIPMENT | Equipment items |
| GEM | Gem items |
| INGREDIENT | Ingredient items |
| ITEM | Generic item |
| METAL | Metal materials |
| MINERAL | Mineral materials |
| MISC | Miscellaneous items |
| POTION | Potion items |
| QUEST | Quest items |
| STONE | Stone materials |

---

# Equipment Data

| Property | Description |
|---|---|
| equip_slot | Equipment slot assignment |
| armor_type | Armor classification |
| weapon_type | Weapon classification |

---

# Armor Types

| Type |
|---|
| CLOTH |
| LIGHT |
| MEDIUM |
| HEAVY |

---

# Weapon Types

| Type |
|---|
| AXE |
| BOW |
| CROSSBOW |
| DAGGER |
| GUN |
| HAMMER |
| INSTRUMENT |
| MACE |
| SCYTHE |
| SHIELD |
| SPEAR |
| STAFF |
| SWORD |
| TOTEM |
| UNARMED |
| WAND |

---

# Requirements

| Property | Description |
|---|---|
| required_class_id | Required class identifier |
| required_level | Minimum required level |
| max_level | Maximum level restriction |
| level_scaling | Enables level scaling |

---

# Economy

| Property | Description |
|---|---|
| price | Item value |
| rarity | Item rarity classification |

---

# Rules

| Property | Description |
|---|---|
| stackable | Determines if multiple items can share a stack |

---

# Stats

| Property | Description |
|---|---|
| modifiers | Dictionary of stat modifiers applied by the item |

---

# Effects

| Property | Description |
|---|---|
| effects | Array of StatEffect resources attached to the item |
