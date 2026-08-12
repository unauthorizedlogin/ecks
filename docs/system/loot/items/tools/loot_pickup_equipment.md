# 🎁 Equipment Pickup Architecture

## Overview

`PickupEquipment` is the world-interaction component responsible for representing an equipment item that can be collected by the player.

It acts as the bridge between a physical item pickup in the world and the game's item/inventory systems. The pickup identifies the item through its `item_id`, resolves its item definition from `ItemDatabase`, detects player interaction through its `Area2D`, delegates inventory ownership to `InventoryManager`, notifies the quest system of the collection event, provides optional UI feedback, and removes itself once the pickup has been processed.

The pickup does **not** own the item definition or inventory state. It is a temporary world-side representation of an item that delegates those responsibilities to the appropriate managers.

---

## Architectural Role

`PickupEquipment` sits at the boundary between the **world interaction layer** and the **item/inventory systems**.

```text
                     ItemDatabase
                          │
                          │ item definition
                          ▼
                   PickupEquipment
                          │
             Player enters pickup area
                          │
            ┌─────────────┼─────────────┐
            ▼             ▼             ▼
    InventoryManager  QuestManager    UI Feedback
            │
            ▼
       Player Inventory
```

The pickup is therefore responsible for coordinating the collection event, rather than becoming another inventory or item-data system.

---

## Item Identity

The pickup is identified by an exported `item_id`.

This ID is the authoritative reference used to resolve the item's definition through `ItemDatabase`.

The pickup may also accept the identifier through node metadata for compatibility with existing or legacy spawning workflows.

Once the ID is resolved, the pickup obtains the relevant item properties required for its world representation and pickup behavior.

The pickup does not duplicate the complete `ItemResource` as its own authoritative item definition.

---

## Item Database Integration

`ItemDatabase` provides the definition used by the pickup.

This allows the world pickup to remain lightweight while item properties continue to be centrally authored and managed.

The pickup can obtain information such as:

* Display name
* Item quantity
* Item type
* Notification behavior
* Stackability
* Item-specific definition data

This establishes the separation:

```text
ItemDatabase
    │
    │ authoritative item definition
    ▼
PickupEquipment
    │
    │ temporary world representation
    ▼
InventoryManager
```

The pickup does not become the source of truth for item data.

---

## Player Detection

The pickup uses `Area2D` collision detection to determine when the player enters or exits its interaction area.

Player presence is tracked locally through `player_in_area`.

The pickup also maintains an `already_picked_up` state to prevent the same world item from being processed more than once.

The interaction flow is therefore event-driven:

```text
Player enters Area2D
        │
        ▼
Validate Player
        │
        ▼
Validate pickup state
        │
        ▼
Process collection
```

Once collection begins, the pickup is immediately marked as processed before delegating the item transfer.

This prevents repeated collision events from producing duplicate inventory additions.

---

## Inventory Integration

`InventoryManager` remains the authority over the player's inventory.

`PickupEquipment` does not directly manipulate inventory slots or storage structures.

Instead, it delegates the item transfer using the resolved `item_id`.

```text
PickupEquipment
      │
      │ add item_id
      ▼
InventoryManager
      │
      ▼
Player Inventory
```

This keeps world pickups independent from the internal inventory implementation.

As a result, inventory behavior such as stacking, slot management, capacity, and persistence remains owned by `InventoryManager`.

---

## Quest Integration

Equipment collection also provides a quest-system integration point.

After the item is transferred to inventory, the pickup emits an item collection event through `QuestManager`.

The event communicates the collected item and quantity rather than requiring the quest system to inspect or reference the pickup node directly.

```text
PickupEquipment
      │
      ├── InventoryManager → inventory update
      │
      └── QuestManager → item collected event
```

This keeps quest objectives decoupled from the physical pickup implementation.

The quest system therefore reacts to the **collection event**, not to the existence of a specific pickup scene.

---

## UI Integration

The pickup supports optional references to:

* Pickup notification UI
* Player inventory UI

These references are injected externally through setter methods.

The pickup therefore does not search for or own the global UI hierarchy.

Its UI responsibility is limited to communicating the result of a successful pickup.

The inventory UI may be refreshed after the inventory operation, while the notification system can display the item's friendly name.

```text
PickupEquipment
      │
      ├── InventoryManager
      │
      └── UI
          ├── Inventory refresh
          └── Pickup notification
```

UI functionality remains optional and does not define the underlying item collection operation.

---

## Pickup Lifecycle

A pickup follows a short-lived world-object lifecycle:

```text
Spawned
   │
   ▼
Resolve Item Definition
   │
   ▼
Wait for Player
   │
   ▼
Player Enters Area
   │
   ▼
Mark As Picked Up
   │
   ├── Add Item
   ├── Notify Quest System
   └── Begin Feedback
          │
          ▼
      Visual Feedback
          │
          ▼
       queue_free()
```

The pickup is therefore intentionally transient.

Once the item has been transferred into the player's inventory, the world representation has fulfilled its purpose and is removed from the scene.

---

## Feedback Lifecycle

Pickup feedback is separated from the actual inventory transaction.

The inventory operation occurs immediately when the pickup is collected.

Visual feedback is then delayed through `PickupDelayTimer`, allowing the player to receive a pickup notification before the world object is removed.

This separation prevents presentation timing from controlling the underlying inventory transaction.

```text
Collection
   │
   ├── Immediate → Inventory
   │
   ├── Immediate → Quest Event
   │
   └── Delayed → Notification
                    │
                    ▼
                Destroy Pickup
```

---

## State Ownership

`PickupEquipment` owns only the state required for its temporary world representation and interaction lifecycle.

### Local State

* `item_id`
* Resolved display/item properties
* Player presence
* Pickup completion state
* Optional UI references

### External State

The following remain owned by their respective systems:

| State                            | Owner               |
| -------------------------------- | ------------------- |
| Item definition                  | `ItemDatabase`      |
| Inventory contents               | `InventoryManager`  |
| Quest progression                | `QuestManager`      |
| Inventory presentation           | `PlayerInventoryUI` |
| Pickup notification presentation | UI layer            |

This prevents the pickup from becoming a secondary source of truth for any persistent game system.

---

## Architectural Boundaries

`PickupEquipment` **does own**:

* Physical pickup representation
* Player proximity detection
* Pickup lifecycle
* Item ID resolution
* Collection coordination
* Pickup feedback triggering
* World-object cleanup

`PickupEquipment` **does not own**:

* Item definitions
* Inventory storage
* Inventory persistence
* Quest progression
* Item database management
* UI state
* Item stacking rules

Its role is orchestration at the point where a world item becomes an inventory item.

---

## Design Intent

The equipment pickup architecture keeps the physical representation of an item deliberately lightweight.

The pickup knows **what item it represents**, detects **when the player collects it**, and delegates the consequences of that collection to the systems responsible for them.

This provides a clean separation:

```text
World Object
     │
     │ "The player collected item X"
     ▼
PickupEquipment
     │
     ├── InventoryManager
     ├── QuestManager
     └── UI
```

The result is a disposable world component rather than a second inventory implementation, item database, or quest system.

`PickupEquipment` exists to translate a physical world interaction into the appropriate system-level events and then remove itself from the world.
