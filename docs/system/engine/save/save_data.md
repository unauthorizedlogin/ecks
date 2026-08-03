# 💾 SaveData Architecture

## Overview

`SaveData` is the central persistence resource used to represent a complete game save slot.

It acts as the shared data contract between gameplay systems and the `SaveManager`.

The resource does not control saving or loading behavior.

Instead, it provides a structured container where systems store their persistent state.

```

Gameplay Systems
│
▼
SaveData Resource
│
▼
SaveManager
│
▼
Serialized Save File

```

---

# Responsibilities

## SaveData Owns

- Persistent game state storage
- Save slot metadata
- Player progression data
- World state snapshots
- Inventory snapshots
- Quest progression
- System-specific persistence containers

---

## SaveData Does Not Own

- Save timing
- Autosave logic
- File paths
- Serialization handling
- Loading behavior
- Data validation
- Runtime system management

Those responsibilities belong to:

```

SaveManager

```

---

# Design Philosophy

SaveData is designed as a growing persistence hub.

As new gameplay systems are created, they may add their required save containers to this resource.

Example:

```

New System Created
│
▼
Needs persistence?
│
▼
Add SaveData field
│
▼
SaveManager serializes automatically

```

The resource grows alongside the game architecture.

---

# Save Flow

## Saving

```

System State
│
▼
Collect Data
│
▼
Populate SaveData
│
▼
SaveManager
│
▼
ResourceSaver.save()
│
▼
Save File

```

---

## Loading

```

Save File
│
▼
ResourceLoader.load()
│
▼
SaveData Instance
│
▼
Systems Restore State

````

---

# Resource Structure

## Version Information

```gdscript
@export var version: int = 1
````

Tracks save format versions.

Used for future migration support when save structures change.

Example:

```
Version 1
    ↓
Version 2
    ↓
Migration Handler
```

---

# Slot Metadata

Stores information about the save slot itself.

```gdscript
slot_index
player_name
selected_class_id
difficulty
total_playtime
last_save_time
```

Used for:

* Save selection menus
* Save previews
* Slot management
* Player identification

---

# Player State

```gdscript
player_data: Dictionary
```

Stores player-specific runtime information.

Examples:

* Character customization
* Attributes
* Unlocks
* Persistent player flags

---

# World State

## Player Position

```gdscript
player_position: Vector2
```

Stores the player's last known world location.

Used during world restoration.

---

## World State

```gdscript
world_state: Dictionary
```

General world persistence container.

Potential data:

* NPC states
* World flags
* Events
* Interactions
* Discovered locations

---

## Chunk State

```gdscript
chunk_state: Dictionary
```

Stores generated world/chunk information.

Examples:

* Modified terrain
* Gathered resources
* Generated structures
* Persistent world changes

---

# Inventory Persistence

Inventory-related systems store their snapshots separately.

```
SaveData
│
├── inventory_data
├── equipment_data
├── currency_data
├── ammo_data
├── consumable_data
├── potion_data
├── gem_data
├── ingredient_data
├── metal_data
├── mineral_data
└── stone_data
```

---

# Inventory Categories

## Equipment

```gdscript
equipment_data
```

Stores:

* Equipped items
* Gear slots
* Item identifiers

---

## Currency

```gdscript
currency_data
```

Stores:

* Gold
* Currency types
* Wallet values

---

## Materials

Persistent resource categories:

```
Gems
Ingredients
Metals
Minerals
Stones
```

Each category has independent storage.

This allows:

* Individual system loading
* Database expansion
* Resource-specific processing

---

# Quest Persistence

```gdscript
quest_data: Dictionary
```

Stores quest progression.

Examples:

* Active quests
* Completed quests
* Objective progress
* Quest flags

Used by:

```
QuestManager
```

---

# Player Progression

## Experience

```gdscript
current_total_xp
```

Stores lifetime player experience.

---

## Level

```gdscript
current_level
```

Stores player level progression.

Future architecture should avoid confusing:

```
Player Level
World Level
Map Level
Difficulty Level
```

Each progression type should have its own identifier.

---

# SaveManager Relationship

```
SaveManager
    │
    ├── Creates SaveData
    │
    ├── Requests system snapshots
    │
    ├── Saves resource
    │
    └── Restores systems
```

SaveManager is the coordinator.

SaveData is the storage model.

---

# System Integration Pattern

When a new system requires persistence:

## Step 1

Create system save data.

Example:

```gdscript
var fishing_data: Dictionary = {}
```

---

## Step 2

System writes its state.

Example:

```gdscript
save_data.fishing_data = FishingManager.get_save_data()
```

---

## Step 3

System restores state.

Example:

```gdscript
FishingManager.load_save_data(save_data.fishing_data)
```

---

# Future Expansion

Potential future additions:

```
SaveData
│
├── achievement_data
├── discovery_data
├── crafting_data
├── reputation_data
├── housing_data
├── companion_data
├── achievement_titles
└── collection_data
```

---

# Architectural Rule

## SaveData stores state.

## SaveManager controls persistence.

## Systems own their own data interpretation.

No gameplay logic should exist inside `SaveData`.

---

# Related Systems

* SaveManager
* GameManager
* PlayerManager
* WorldManager
* Inventory System
* Quest System
* Achievement System
* Audio Settings System
* Configuration System
