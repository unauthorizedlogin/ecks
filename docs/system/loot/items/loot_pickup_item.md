# 📦 Pickup Base (`PickupBase`) — System Role

The **Pickup Base** is the universal foundation for all world pickup objects.

It provides the shared pickup lifecycle used by all pickup categories, including item drops, currency, consumables, equipment, resources, and future pickup types.

`PickupBase` does not define what happens when an object is collected. Instead, it manages the common pickup pipeline and delegates the final reward behavior to child classes.

Child pickups are responsible for implementing `_on_pickup()` and routing the collected object into the correct game system.

---

# 🧬 Base Class Architecture

`PickupBase` is a reusable inheritance layer:

```gdscript
extends Area2D
class_name PickupBase
````

Architecture:

```
              PickupBase
                  |
    --------------------------------
    |              |               |
LootCurrency   LootItem      LootEquipment
    |              |               |
CurrencyManager InventoryManager ItemManager
```

The base class provides the shared interaction layer.

Child classes provide specialized routing.

---

# 🎯 Core Responsibilities

`PickupBase` is responsible for:

* Detecting player interaction
* Initializing pickup data
* Loading item metadata
* Managing pickup availability delay
* Allowing pickups to follow the player
* Displaying pickup notifications
* Emitting collection events
* Removing collected pickups

`PickupBase` does **not** handle:

* Reward distribution
* Inventory storage
* Currency management
* Equipment handling
* Consumable effects
* Quest rewards

Those responsibilities belong to the child pickup systems and their managers.

---

# 📦 Pickup Initialization

Each pickup defines:

```gdscript
@export var item_id: String
@export var item_quantity: int
```

The `item_id` acts as the connection between the world object and the data layer.

Pipeline:

```
World Pickup
      |
      ↓
item_id
      |
      ↓
ItemDatabase
      |
      ↓
Pickup Metadata
```

Loaded data includes:

* Display name
* Item type
* Notification settings
* Stack behavior
* Default quantity

---

# 📚 Item Data Loading

During `_ready()`:

```gdscript
_init_item_data()
```

The pickup requests its definition from:

```
ItemDatabase
```

Example:

```
gold_coin
iron_sword
health_potion
raw_ore
```

The database provides:

```
Item ID
   |
   ├── Name
   ├── Type
   ├── Stack Rules
   └── Display Data
```

Invalid item IDs are rejected to prevent orphaned pickups.

---

# ⏳ Pickup Grace Period

Pickups do not become immediately collectible.

A delay timer provides a grace period:

```
Pickup Spawned
       |
       ↓
PickupDelayTimer
       |
       ↓
can_pickup = true
       |
       ↓
Player Can Collect
```

This prevents issues such as:

* Items being collected before spawn effects finish
* Newly spawned loot immediately triggering
* Physics overlap collection problems

---

# 🚶 Player Detection Pipeline

Pickup interaction uses:

```gdscript
Area2D
```

The pickup listens for:

```gdscript
body_entered()
```

Flow:

```
Player Enters Pickup Area
          |
          ↓
Check can_pickup
          |
          ↓
Confirm Player Body
          |
          ↓
Run Child Pickup Logic
          |
          ↓
Emit Pickup Event
```

---

# 🧩 Child Pickup Routing

The base class defines the pickup contract:

```gdscript
func _on_pickup(body):
```

Child classes override this function.

Example:

```gdscript
LootCurrency
        |
        ↓
CurrencyManager.add_coin()

LootItem
        |
        ↓
InventoryManager.add_item()

LootEquipment
        |
        ↓
EquipmentManager.add_equipment()
```

The base class does not know what the pickup becomes.

It only manages the collection pipeline.

---

# 🧲 Player Following Behavior

Pickups can move toward the player after activation.

Example:

```
Loot Dropped
      |
      ↓
Player Approaches
      |
      ↓
Pickup Starts Following
      |
      ↓
Pickup Moves Toward Player
      |
      ↓
Collection
```

Controlled by:

```gdscript
start_following_player()
```

Movement speed:

```gdscript
FOLLOW_SPEED
```

This allows Diablo-style magnetic loot collection behavior.

---

# 🔔 Pickup Notifications

Pickups can display collection messages.

Controlled by:

```gdscript
show_notification
```

Example:

```
Picked up Iron Ore x3!
Picked up Health Potion!
Picked up Gold!
```

The base class manages presentation only.

The reward itself is handled elsewhere.

---

# 📢 Event Pipeline

After collection:

```gdscript
_emit_pickup_event()
```

The pickup broadcasts:

```
PickupBase
      |
      ↓
QuestManager
      |
      ↓
Objective Tracking
```

Event payload:

```gdscript
{
    type: COLLECT,
    data:
    {
        target_id,
        amount
    }
}
```

This allows systems such as:

* Collection quests
* Achievement tracking
* Statistics
* Progression objectives

to react without pickup-specific code.

---

# 🧠 Architecture Relationship

Current pickup architecture:

```
                    WORLD
                      |
                      |
                 PickupBase
                      |
        --------------------------------
        |              |               |
  Currency Pickup   Item Pickup   Equipment Pickup
        |              |               |
 CurrencyManager InventoryManager ItemManager
        |
        ↓
   Player Systems
        |
        ↓
   Event / Quest Pipeline
```

---

# 🔗 System Dependencies

| System               | Responsibility             |
| -------------------- | -------------------------- |
| PickupBase           | Universal pickup lifecycle |
| ItemDatabase         | Provides pickup metadata   |
| QuestManager         | Receives collection events |
| EventManager         | Displays pickup feedback   |
| Child Pickup Classes | Handle reward routing      |
| CurrencyManager      | Handles currency rewards   |
| InventoryManager     | Handles item ownership     |

---

# 🚧 Future Expansion

`PickupBase` is designed as the foundation for additional pickup categories.

Potential future implementations:

```
PickupBase
    |
    ├── LootCurrency
    ├── LootItem
    ├── LootEquipment
    ├── LootResource
    ├── LootQuestItem
    └── LootReward
```

Each child system only needs to define:

```
"What does this pickup give?"
```

The base class continues handling:

```
"How does a pickup work?"
```

---

# ✅ Current System Responsibilities

| System        | Responsibility                |
| ------------- | ----------------------------- |
| PickupBase    | Pickup behavior and lifecycle |
| Child Pickups | Reward routing                |
| ItemDatabase  | Pickup definitions            |
| Managers      | Ownership and state changes   |
| QuestManager  | Collection objectives         |
| EventManager  | Player feedback               |

---

# Overall

`PickupBase` is the universal interaction layer for all world pickups.

Its purpose is to standardize pickup behavior while keeping reward logic separated into specialized child systems.

The architecture follows the engine-wide authority pattern:

* Base classes handle shared behavior
* Child classes handle system routing
* Managers own persistent game state

This allows new pickup types to be added without duplicating pickup logic across the game.
