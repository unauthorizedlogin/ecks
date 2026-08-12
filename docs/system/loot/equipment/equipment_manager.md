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
