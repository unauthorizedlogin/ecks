# 💰 Currency Panel

## Overview

The Currency Panel is the **UI controller for the player's currency display**.

It listens for currency inventory changes from `CurrencyManager` and updates the coin slot with the current coin count.

The panel does not manage currency. It only reads the current value and presents it.

```text
CurrencyManager
      │
      │ inventory_changed
      ▼
ui_panel_currency.gd
      │
      │ set_item()
      ▼
ui_slot_coin.gd
      │
      ├── Coin Icon
      └── Coin Count
```

---

# Architecture Role

The system is split into two UI responsibilities:

### `ui_panel_currency.gd`

Owns:

* CurrencyManager connection
* Currency change response
* Current coin count retrieval
* Currency icon assignment
* Updating the coin slot

### `ui_slot_coin.gd`

Owns:

* Coin icon display
* Count label
* Selection outline
* Slot data
* Showing / hiding the slot

### `CurrencyManager`

Owns:

* Currency state
* Coin inventory
* Currency change signaling
* Coin count retrieval

---

# Runtime Flow

When the panel enters the scene:

```text
_ready()
   │
   ▼
Connect to CurrencyManager.inventory_changed
   │
   ▼
_on_currency_changed()
   │
   ▼
CurrencyManager.get_coin_count()
   │
   ▼
coin_slot.set_item()
   │
   ▼
Updated Coin UI
```

Whenever the currency changes:

```text
CurrencyManager
      │
      │ inventory_changed
      ▼
_on_currency_changed()
      │
      ▼
get_coin_count()
      │
      ▼
set_item()
```

This keeps the UI synchronized with the currency system without the panel needing to poll for changes.

---

# Coin Slot

`ui_slot_coin.gd` provides the reusable visual representation of the coin entry.

Its `set_item()` method receives:

```gdscript
{
    "id": "coin",
    "icon": coin_icon,
    "count": count
}
```

The slot then updates:

* `item_data`
* Icon texture
* Count text
* Visibility

The slot also supports:

```gdscript
clear_item()
set_selected(value)
```

allowing it to hide its contents or display its selection state.

---

# Architectural Boundary

```text
CurrencyManager
    │
    │ Currency State
    ▼
Currency Panel
    │
    │ UI Data
    ▼
Coin Slot
    │
    ├── Icon
    ├── Count
    └── Selection State
```

The important separation is that **currency logic remains in `CurrencyManager` while presentation remains entirely in the UI layer**.

The panel acts as the bridge between the two.
