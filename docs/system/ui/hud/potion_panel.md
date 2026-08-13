# 🧪 Potion HUD Panel — Architecture

## System Role

The **Potion HUD Panel** is the UI layer responsible for displaying the player's available health, mana, and stamina potions.

It reads potion counts from `PotionManager` and updates the corresponding potion slots.

The panel does **not** manage potion inventory or potion usage.

---

## Data Flow

```text
PotionManager
      │
      │ inventory_changed
      ▼
Potion HUD Panel
      │
      ├── Health → Health Slot
      ├── Mana   → Mana Slot
      └── Stamina → Stamina Slot
```

---

## Potion Panel

The panel:

* Connects to `PotionManager.inventory_changed`
* Retrieves health, mana, and stamina potion counts
* Builds the display data for each potion
* Updates the three configured potion slots
* Initializes the display immediately on `_ready()`

Each slot receives:

```gdscript
{
    "id": "potion_health_small",
    "icon": health_icon,
    "count": health_count
}
```

with equivalent data for mana and stamina.

---

## Potion Slot

`ui_slot_potion.gd` provides the generic potion slot interface.

It handles:

* Potion ID
* Icon
* Count
* Visibility
* Count updates
* Selection outline
* Clearing the slot

Primary interface:

```text
set_item()
update_count()
clear()
set_selected()
```

---

## Specialized Slots

The panel uses dedicated UI slots for each potion type:

```text
ui_slot_hp.gd
ui_slot_mp.gd
ui_slot_stamina.gd
```

These provide the individual health, mana, and stamina slot presentation while following the same basic slot structure:

```text
Potion Slot
├── Icon
├── CountLabel
└── SelectOutline
```

---

## Architectural Boundary

**PotionManager**
Owns potion inventory and potion counts.

**Potion HUD Panel**
Translates manager state into UI display data.

**Potion Slots**
Render the potion icon, quantity, and selection state.

The system is therefore **manager-driven and event-driven**, with the HUD acting purely as a presentation layer.
