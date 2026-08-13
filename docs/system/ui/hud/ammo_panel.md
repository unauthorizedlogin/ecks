# 🏹 Ammo HUD Panel

## Overview

The **Ammo HUD Panel** is the UI layer responsible for displaying the player's available ammunition.

It reads ammunition counts from `AmmoManager` and updates dedicated HUD slots for each supported ammo type.

The panel does not manage ammunition state. `AmmoManager` remains the source of truth.

---

# Architecture Role

```text
AmmoManager
     │
     │ inventory_changed
     ▼
Ammo HUD Panel
     │
     ├── Arrow Slot
     └── Bomb Slot
          │
          ▼
      Ammo Slot UI
```

### Ammo HUD Panel

The panel:

* Connects to `AmmoManager.inventory_changed`
* Retrieves arrow and bomb counts
* Builds the display data for each ammo type
* Updates the corresponding slots
* Always displays the ammo entries, including a count of `0`

### `ui_slot_ammo.gd`

Provides the generic ammo slot behavior:

* Item ID
* Icon
* Count
* Visibility
* Count updates
* Selection state
* Clearing

### `ui_slot_arrow.gd`

Provides the arrow-specific slot presentation.

### `ui_slot_bomb.gd`

Provides the bomb-specific slot presentation.

---

# Runtime Flow

When the panel initializes:

```text
_ready()
   │
   ├── Connect AmmoManager.inventory_changed
   │
   └── _on_inventory_changed()
```

The update then follows:

```text
AmmoManager
     │
     ├── get_arrow_count()
     └── get_bomb_count()
            │
            ▼
   _on_inventory_changed()
            │
            ├── Arrow data → Arrow Slot
            └── Bomb data  → Bomb Slot
```

Whenever ammunition changes, the same update path runs again.

---

# Ammo Display Data

Each slot receives a dictionary containing:

```gdscript
{
    "id": "arrow_normal",
    "icon": arrow_icon,
    "count": arrow_count
}
```

or:

```gdscript
{
    "id": "bomb_basic",
    "icon": bomb_icon,
    "count": bomb_count
}
```

The count is clamped to zero before being displayed:

```gdscript
max(arrow_count, 0)
max(bomb_count, 0)
```

This prevents the HUD from displaying negative ammunition values.

---

# Always-Visible Ammo

Unlike a traditional inventory slot, the ammo HUD always displays its configured ammunition types.

```text
Arrows   [icon] 0
Bombs    [icon] 0
```

The slot therefore remains visible even when the player has no ammunition.

This makes the HUD a **fixed ammunition display**, rather than an inventory-driven list of currently owned items.

---

# Generic Ammo Slot

`ui_slot_ammo.gd` defines the common slot interface:

```text
set_item()
    ↓
Display item data

update_count()
    ↓
Change only ammunition count

clear()
    ↓
Remove slot contents

set_selected()
    ↓
Toggle selection outline
```

The slot maintains:

```text
item_id
count
icon
selection state
```

This provides a common foundation for additional ammunition types.

---

# Specialized Slots

`ui_slot_arrow.gd` and `ui_slot_bomb.gd` currently implement the same basic visual slot behavior independently.

Their primary distinction is their editor icon configuration:

```text
ui_slot_arrow.gd
    → Arrow icon

ui_slot_bomb.gd
    → Bomb icon
```

This allows the individual slot scenes/scripts to be visually identifiable while maintaining the same basic display structure:

```text
Ammo Slot
├── Icon
├── CountLabel
└── SelectOutline
```
