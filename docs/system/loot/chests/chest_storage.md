# 🗄️ Storage Chest Architecture

## Overview

The Storage Chest is a persistent world container that provides access to a dedicated inventory associated with a unique `chest_id`.

Unlike a Dynamic Chest, the Storage Chest does not spawn a predefined item when opened. It acts as a world-facing interface to storage inventory managed by `InventoryManager`.

Its primary architectural purpose is to connect a physical chest in the world with the inventory system while allowing the same storage data to persist independently of the chest node.

---

## Architectural Role

Storage Chests occupy the **world interaction layer** while delegating inventory ownership to `InventoryManager`.

```text
Player
  │
  │ Interact
  ▼
Storage Chest
  │
  ├── Open / Close State
  ├── UI Coordination
  └── chest_id
          │
          ▼
   InventoryManager
          │
          ▼
   Persistent Storage Data
```

The chest is the physical access point. `InventoryManager` owns the actual storage inventory.

---

## Responsibilities

The Storage Chest is responsible for:

* Detecting player proximity.
* Handling keyboard and mouse interaction.
* Opening and closing the chest.
* Controlling the chest animation.
* Identifying its storage inventory through `chest_id`.
* Initializing the corresponding storage inventory through `InventoryManager`.
* Connecting the storage inventory to `PlayerInventoryUI`.
* Coordinating player movement state while storage UI is open.
* Providing the inventory operations required by the storage UI.
* Closing automatically when the player leaves the interaction area.

The chest does **not** directly own or persist the storage contents.

---

## Storage Identity

Each Storage Chest is identified by:

```text
chest_id : String
```

This identifier is the architectural link between the physical world object and its inventory data.

```text
Chest Node
    │
    │ chest_id
    ▼
InventoryManager
    │
    ▼
Storage Inventory
```

This allows the physical chest node to be recreated during level loading without requiring the inventory contents to be stored directly on that node.

The inventory therefore belongs to the storage identity, not the scene instance.

---

## Inventory Ownership

`InventoryManager` is the authoritative owner of storage inventory data.

The Storage Chest provides the world-facing access layer through:

* `get_items()`
* `add_item()`
* `remove_item()`

These methods form the interface expected by the storage UI.

The architecture therefore separates:

```text
Storage Data
    ↓
InventoryManager

Storage Interaction
    ↓
Chest

Storage Presentation
    ↓
PlayerInventoryUI
```

The chest does not duplicate inventory state locally.

---

## Opening Lifecycle

Opening a Storage Chest follows this general sequence:

```text
Player enters range
        │
        ▼
Interact
        │
        ▼
Storage Chest
        │
        ├── Set open state
        ├── Play open animation
        ├── Initialize storage inventory
        ├── Disable player movement
        └── Open storage UI
```

The storage inventory is initialized using the chest's identity before the UI is opened.

This ensures that the UI operates against the correct storage container.

---

## Closing Lifecycle

Storage closure can originate from several conditions:

* Explicit close-menu input.
* Inventory toggle input.
* Player leaving the chest's interaction area.

Closing performs the inverse coordination:

```text
Close Storage
      │
      ├── Close storage UI
      ├── Close player inventory UI
      ├── Restore player movement
      └── Play close animation
```

This keeps the physical chest state, UI state, and player-control state synchronized.

---

## UI Integration

The Storage Chest does not instantiate or own the inventory UI.

Instead, it resolves `PlayerInventoryUI` through `UIManager`.

This is important because the UI may persist across world or level transitions while the chest itself is a scene-local object.

The chest therefore treats the UI as an external service:

```text
Storage Chest
      │
      ▼
   UIManager
      │
      ▼
PlayerInventoryUI
```

The retry-based UI resolution allows the chest to wait for the UI hierarchy to become available before attempting to bind to it.

---

## Storage UI Interface

The Storage Chest exposes a small interface consumed by the storage UI:

```text
get_items()
add_item()
remove_item()
```

These methods deliberately forward operations to `InventoryManager`.

This prevents the UI from needing to know how storage inventories are internally represented or indexed.

The resulting dependency direction is:

```text
PlayerInventoryUI
        │
        ▼
Storage Chest Interface
        │
        ▼
InventoryManager
```

The UI interacts with the chest as the active storage endpoint, while the manager remains responsible for inventory data.

---

## Player Control Coordination

Opening storage temporarily disables player movement through `UIManager`.

The chest therefore participates in gameplay/UI state coordination without becoming responsible for player movement itself.

```text
Storage Open
     │
     ▼
UIManager.movement_disabled
     │
     ▼
Player Movement Restricted
```

Closing storage emits the corresponding movement-enabled signal.

This keeps player-control implementation outside the chest while allowing the chest interaction to establish the required gameplay state.

---

## Interaction Model

Storage Chests support both standard input interaction and mouse-driven interaction.

### Input Interaction

The chest monitors the interaction action while the player is within its interaction area.

### Mouse Interaction

The `mouse_interact()` entry point provides an external interaction surface for mouse-based world interaction systems.

Both paths ultimately converge on:

```text
open_chest()
```

This prevents separate interaction implementations from producing different storage-opening behavior.

---

## World Lifecycle

Storage Chests are world-scene objects and may be created or destroyed as levels change.

Their inventory contents are not tied to the lifetime of the node.

The `chest_id` provides stable identity across scene lifecycles, while `InventoryManager` provides the persistent inventory state.

This separation is particularly important for level switching and save/load behavior.

---

## Architectural Boundaries

### Owns

* Physical chest interaction
* Open/closed state
* Chest animation
* Player proximity state
* Storage identity reference
* Storage UI coordination

### Delegates To

**InventoryManager**

* Storage inventory ownership
* Storage initialization
* Item retrieval
* Item insertion
* Item removal

**UIManager**

* Player inventory UI resolution
* Player movement state signaling

**PlayerInventoryUI**

* Inventory and storage presentation
* Storage UI interaction

### Does Not Own

* Persistent storage data
* Inventory serialization
* Item database logic
* Player inventory data
* UI construction
* Player movement implementation

---

## Dynamic Chest vs Storage Chest

The two chest architectures intentionally serve different purposes.

|                   | Dynamic Chest               | Storage Chest                    |
| ----------------- | --------------------------- | -------------------------------- |
| Primary Role      | Spawn world content         | Access persistent storage        |
| Content Ownership | Configured scene            | InventoryManager                 |
| Identity          | `interact_id`               | `chest_id`                       |
| Inventory         | None                        | Dedicated storage inventory      |
| Opening Result    | Item spawned                | Storage UI opened                |
| Persistence       | Content is spawned once     | Inventory persists independently |
| Quest Event       | Emits interaction event     | No direct quest event            |
| UI Dependency     | Optional item UI references | Required storage UI coordination |
| World Object      | Content source              | Storage access point             |

The distinction is architectural rather than merely behavioral:

**Dynamic Chest = world reward container.**

**Storage Chest = world interface to persistent inventory.**
