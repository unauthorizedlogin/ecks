# 📦 Dynamic Chest Architecture

## Overview

`ChestDynamic` is a world-interaction component responsible for managing a dynamically spawned chest and its immediate interaction lifecycle.

It is implemented as a `StaticBody2D` and represents a physical world object that:

* Detects player proximity
* Responds to the interaction input
* Maintains its open state
* Plays its opening animation
* Spawns its configured item scene
* Provides optional UI references to the spawned item
* Emits a standardized interaction event to the quest system

`ChestDynamic` acts as the **world-side interaction controller** for the chest. It does not own the inventory system, quest system, or item system.

---

# Architectural Role

The chest sits at the boundary between the physical world and several game systems.

```text
                     ┌──────────────────┐
                     │   Player Input   │
                     └────────┬─────────┘
                              │
                              ▼
                     ┌──────────────────┐
                     │  ChestDynamic    │
                     │                  │
                     │ Proximity        │
                     │ Interaction     │
                     │ Open State       │
                     │ Animation       │
                     │ Item Spawn      │
                     └───────┬──────────┘
                             │
              ┌──────────────┼──────────────┐
              ▼              ▼              ▼
       ┌────────────┐ ┌─────────────┐ ┌─────────────┐
       │ Item Scene │ │ UI References│ │ QuestManager│
       └────────────┘ └─────────────┘ └─────────────┘
```

The chest is therefore an **integration point**, rather than a replacement for any of the systems it communicates with.

---

# Core Responsibilities

`ChestDynamic` owns the chest's local runtime state and interaction behavior.

Its responsibilities are:

* Detect whether the player is within interaction range
* Gate interaction based on chest state
* Transition the chest into its opened state
* Trigger the chest opening animation
* Spawn the configured item scene
* Prevent duplicate item spawning
* Provide optional UI references to the spawned item
* Notify `QuestManager` when the chest is interacted with

The component is intentionally lightweight. Persistent inventory behavior, item behavior, quest progression, and UI management remain external responsibilities.

---

# Interaction Model

The chest uses an `Area2D` to determine player proximity.

The interaction flow is:

```text
Player enters Chest Area
        ↓
player_nearby = true
        ↓
Player presses Interact
        ↓
Chest is not already open
        ↓
open_chest()
        ↓
Chest state becomes open
        ↓
Quest interaction event emitted
        ↓
Opening animation
        ↓
Configured item spawned
```

Leaving the interaction area removes the interaction eligibility.

This keeps proximity detection local to the chest rather than requiring a centralized interaction manager.

---

# State Model

The chest maintains two independent runtime state flags:

```text
is_open
item_spawned
```

`is_open` represents whether the chest has already been activated.

`item_spawned` represents whether its configured item scene has already been instantiated.

The distinction is useful because the two states represent different concerns:

```text
Chest State
    └── Open / Closed

Spawn State
    └── Item Spawned / Not Spawned
```

The interaction path requires the chest to be closed, while the spawn path separately protects against duplicate item creation.

---

# Item Spawning

The chest does not contain item data directly.

Instead, it exposes:

```text
item_scene : PackedScene
```

This makes the chest a generic container capable of spawning different item scenes without changing its implementation.

The runtime relationship is:

```text
ChestDynamic
    │
    └── PackedScene
          ↓
       Instantiate
          ↓
     World Item Node
```

The spawned item is added to the current world scene rather than becoming a child of the chest.

This establishes the spawned item as an independent world object after creation.

---

# UI Integration

The chest discovers references to the world's existing UI hierarchy and optionally passes those references to the spawned item.

The chest itself does not manipulate inventory UI behavior.

Instead:

```text
ChestDynamic
     │
     ├── PlayerInventoryUI reference
     └── Notification reference
              ↓
        Spawned Item
```

The item can consume those references if it exposes the corresponding setter methods.

This is a deliberately loose integration model: the chest does not require the spawned item to implement those methods.

The resulting architecture allows the same chest spawning mechanism to work with item scenes that do or do not require those UI integrations.

---

# World Dependency

`ChestDynamic` expects to operate inside the project's world scene hierarchy.

At runtime it resolves:

```text
World
 ├── MenusUI
 │    └── PlayerInventoryUI
 │
 └── Notification
      └── itemnotification
```

These references are acquired after the initial frame so the world scene hierarchy has finished constructing.

The chest therefore depends on the world providing these optional integration points, but does not own them.

---

# Quest Integration

Opening the chest produces a standardized interaction event through `QuestManager`.

The event identifies:

* Objective type
* Interaction target ID
* Interaction amount

Conceptually:

```text
Chest Interaction
      ↓
QuestManager
      ↓
INTERACT Objective
      ↓
target_id
      ↓
amount = 1
```

The chest therefore does not contain quest-specific progression logic.

It simply reports the interaction using the project's existing objective-event architecture.

This allows quests to react to chest interaction without the chest knowing which quests exist or how objectives are evaluated.

---

# Interaction Identity

`interact_id` provides the chest's external identity for quest/objective resolution.

This separates:

```text
Physical Node
     ≠
Quest Target Identity
```

Multiple chest instances can therefore use the same interaction architecture while providing different target IDs to the quest system.

The chest only reports the configured identity; `QuestManager` remains responsible for determining what that interaction means to active objectives.

---

# Animation Responsibility

The chest owns its local opening presentation.

When opened, it triggers the configured `AnimatedSprite2D` animation:

```text
open
```

Animation is therefore part of the chest's local state transition rather than a responsibility of the quest, item, or UI systems.

The architecture remains:

```text
Interaction
    ↓
Chest State Transition
    ↓
Chest Presentation
```

rather than allowing external systems to control the chest's visual lifecycle.

---

# Dependency Boundaries

| Component             | Responsibility in Chest Architecture                      |
| --------------------- | --------------------------------------------------------- |
| `ChestDynamic`        | Chest interaction, state, animation, and item spawning    |
| `Area2D`              | Player proximity detection                                |
| `AnimatedSprite2D`    | Chest opening presentation                                |
| `PackedScene`         | Defines the item scene to instantiate                     |
| World Scene           | Provides the runtime scene destination and UI hierarchy   |
| `UIManager`           | Provides access to the global UI system                   |
| `PlayerInventoryUI`   | Receives inventory-related interaction from spawned items |
| Notification UI       | Receives item notification integration                    |
| `QuestManager`        | Processes the chest interaction event                     |
| `ObjectiveDefinition` | Defines the standardized objective type                   |

`ChestDynamic` coordinates these systems but does not replace their responsibilities.

---

# Architectural Boundaries

The chest intentionally does **not**:

* Manage player inventory
* Add items directly to inventory
* Define item behavior
* Evaluate quest objectives
* Track quest completion
* Own notification behavior
* Own global UI state
* Persist chest state
* Define item generation rules
* Manage world transitions

Its responsibility ends at **detecting interaction, transitioning its own state, spawning its configured world item, and reporting the interaction**.

---

# Event-Driven Quest Integration

The quest connection is deliberately event-based rather than direct objective manipulation.

```text
Chest
  │
  │ notify_event()
  ▼
QuestManager
  │
  ▼
Objective Resolution
```

This prevents the chest from needing knowledge of the quest architecture beyond the standardized event contract.

As a result, other world interactables can use the same objective-event pattern without duplicating quest logic inside each interactable.

---

# Runtime Lifecycle

The complete runtime lifecycle can be represented as:

```text
Scene Instantiation
        ↓
Initialize World References
        ↓
Connect Proximity Signals
        ↓
Wait for Player
        ↓
Player Enters Area
        ↓
Interaction Enabled
        ↓
Interact Input
        ↓
Open Chest
        ├── Set Open State
        ├── Emit Quest Event
        ├── Play Animation
        └── Spawn Item
                 ↓
          Item Becomes World Object
```

After the item has been spawned, `ChestDynamic` retains responsibility only for its own runtime state.

---

# Design Principle

The core architectural principle of `ChestDynamic` is:

> **The chest owns the interaction, not the systems that the interaction touches.**

The chest knows **when it was interacted with** and **what it should spawn**, but it does not need to know how inventory, quests, notifications, or item behavior work internally.

This keeps the chest as a small world-facing component while allowing it to participate in the larger architecture through established system interfaces and event contracts.

---

# Summary

`ChestDynamic` is a **world interaction and spawn component**.

Its architectural role is to bridge a physical chest in the world with existing game systems:

```text
Physical World
     ↓
ChestDynamic
     ├── Player Interaction
     ├── Chest State
     ├── Animation
     ├── Item Spawn
     └── Quest Event
              ↓
       External Systems
```

The component remains intentionally narrow: **detect → interact → open → spawn → notify**.

All higher-level ownership remains with the systems responsible for inventory, items, UI, quests, and world state.
