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



