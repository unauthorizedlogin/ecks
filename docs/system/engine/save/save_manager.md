# Save Manager

The `SaveManager` is the central persistence orchestration layer for game save and load operations.

It manages save slots, secure save encoding/decoding, save-file storage, system serialization and deserialization, world restoration, save migration, and save-state notifications. Individual gameplay systems remain responsible for their own runtime state while `SaveManager` coordinates when and where that state is persisted.

## Responsibilities

* Initialize save security and persistence services.
* Load all configured save slots into memory.
* Manage autosave, quicksave, and manual save slots.
* Create and write `SaveData` resources.
* Collect serialized state from game systems.
* Securely encode save data before writing to disk.
* Decode and authenticate save data during loading.
* Restore global, player, world, environment, time, inventory, quest, and minimap state.
* Coordinate world loading before restoring dependent runtime state.
* Migrate older save versions to the current supported format.
* Notify UI systems when save data changes.

## Architecture

`SaveManager` acts as the persistence coordinator rather than owning the serialized state of individual systems.

```text
                    SaveManager
                         │
          ┌──────────────┼──────────────┐
          │              │              │
    SaveSettings   SaveKeyProvider  SaveSecurity
          │                             │
          │                       Encode / Decode
          │                             │
          ▼                             ▼
     Slot Layout                    Save Files
                                         │
                                         ▼
                                    SaveData
                                         │
        ┌────────────────────────────────┼──────────────────────────────┐
        │                                │                              │
   GameManager                      PlayerManager                  WorldManager
   EnvironmentManager              Inventory Systems              ChunkManager
   QuestManager                     TimeManager                    MiniMapManager
        │                                │                              │
        └────────────── serialize / deserialize ────────────────────────┘
```

## Save Slot Management

Slot configuration is provided by `SaveSettings`.

The manager no longer hardcodes the slot layout directly. Slot behavior is derived from:

* `max_slots`
* `autosave_slot`
* `quicksave_slot`
* `first_manual_slot`
* `save_directory`
* `file_name`
* `current_supported_version`

Manual saves search from `first_manual_slot` through `max_slots` for the first empty entry.

### Slot Types

```text
0             Autosave
1             Quicksave
first_manual  Manual saves
```

The configured slot count can be expanded without changing the core save-management logic.

## Initialization

`initialize()` performs the persistence bootstrap:

1. Prevents duplicate initialization.
2. Creates `SaveKeyProvider`.
3. Creates `SaveSecurity`.
4. Configures save security.
5. Loads all configured save slots into memory.
6. Marks the manager initialized.
7. Reports initialization timing.

If save security configuration fails, initialization stops before save operations are enabled.

## Save Cache

`SaveManager` maintains an in-memory `saves` array representing the configured slots.

```gdscript
var saves: Array = []
```

Each entry contains either decoded `SaveData` or `null` when the slot has no save file.

`load_all_slots()` rebuilds this cache from the configured save directory.

The cache allows slot availability to be queried without repeatedly reading save files from disk.

## Save Operations

### Autosave

```gdscript
save_autosave()
```

Writes the current game state to the configured autosave slot.

### Quicksave

```gdscript
save_quick()
```

Writes the current game state to the configured quicksave slot.

### New Manual Save

```gdscript
save_new_game()
```

Finds the first available manual slot and writes the current game state there.

### Save Current State

```gdscript
save_current_state()
```

Provides a named autosave checkpoint used by gameplay systems or world transitions.

## Save Pipeline

`save_game()` coordinates the complete serialization pipeline.

```text
Current Runtime State
        │
        ▼
    SaveData.new()
        │
        ├── Core metadata
        ├── Player state
        ├── World state
        ├── Environment state
        ├── Chunk state
        ├── Progression state
        ├── Inventory state
        ├── Quest state
        ├── Minimap/Fog state
        └── Time state
        │
        ▼
 SaveSecurity.encode()
        │
        ▼
   Save File (.res)
        │
        ▼
  Slot Cache Refresh
        │
        ▼
save_data_updated
```

The slot index is always written into the resulting `SaveData`, ensuring the serialized data remains associated with the slot that produced it.

## Serialized Systems

`SaveManager` delegates state serialization to the owning systems.

Current serialized systems include:

* `PlayerManager`
* `WorldManager`
* `EnvironmentManager`
* `ChunkManager`
* `AmmoManager`
* `ConsumableManager`
* `CurrencyManager`
* `EquipmentManager`
* `GemManager`
* `IngredientManager`
* `InventoryManager`
* `MetalManager`
* `MineralManager`
* `PotionManager`
* `StoneManager`
* `QuestManager`
* `MiniMapManager`
* `TimeManager`

`SaveManager` does not reconstruct these systems' internal state itself. It stores the serialized payload returned by each system.

## Core Save Metadata

In addition to system-specific state, the save records core session information:

* Player name
* Selected class
* Difficulty
* Total playtime
* Last save timestamp
* Current map ID
* Current level
* Current total XP
* Slot index

Legacy map UID values are converted to database map IDs before being stored.

## Secure Persistence

Save data passes through `SaveSecurity` before reaching disk.

```text
SaveData
   │
   ▼
SaveSecurity.encode()
   │
   ▼
FileAccess
   │
   ▼
user:// configured save directory
```

Loading reverses this process:

```text
Save File
   │
   ▼
FileAccess
   │
   ▼
SaveSecurity.decode()
   │
   ▼
SaveData
```

Invalid or unauthenticated save data is rejected before restoration.

## Load Pipeline

`load_game()` coordinates the complete restoration process.

```text
Save File
   │
   ▼
Decode / Authenticate
   │
   ▼
Restore Session Metadata
   │
   ▼
Deserialize Preload Systems
   │
   ▼
Load Saved World
   │
   ├── Environment
   ├── Time
   └── Minimap / Fog
   │
   ▼
Restore Player
   │
   ├── Player data
   └── Level / XP
   │
   ▼
Resume Gameplay
```

The world is loaded before player restoration is finalized so that dependent runtime nodes and systems exist when their state is applied.

## World Restoration

The load sequence restores world-dependent state after loading the saved map.

### Environment

`EnvironmentManager.deserialize_environment()` restores the saved environment configuration and temperature state.

### Time

`TimeManager.deserialize_time()` restores the saved temporal state.

### Minimap

`MiniMapManager` is allowed to finish initialization before fog-of-war data is restored.

This ensures the minimap runtime exists before applying serialized exploration state.

## Player Restoration

Player state is restored through `PlayerManager`.

After the player is available, the manager restores:

* Player serialized data
* Current level
* Total XP

The player's `LevelComponent` receives its progression data through its own load interface.

## Save Notifications

`save_data_updated` is emitted after successful save operations.

This allows UI systems such as save/load menus to refresh their slot information without directly monitoring the filesystem.

```text
SaveManager
     │
     └── save_data_updated
              │
              ▼
        Save / Load UI
```

## Save Migration

Save compatibility is handled through versioned migration.

`SaveSettings.current_supported_version` defines the active save format version.

```text
Older SaveData
      │
      ▼
migrate_save_data()
      │
      ├── v0 → v1
      ├── v1 → v2
      └── future migrations
      │
      ▼
Current Save Format
```

Each migration step updates the data structure and increments the stored version.

When migration modifies the save, the migrated data is written back to the original slot.

## Dependencies

`SaveManager` coordinates with:

* `SaveSettings`
* `SaveData`
* `SaveKeyProvider`
* `SaveSecurity`
* `GameManager`
* `PlayerManager`
* `WorldManager`
* `EnvironmentManager`
* `ChunkManager`
* `TimeManager`
* `MiniMapManager`
* `QuestManager`
* Inventory and progression managers
* `TransitionManager`
* `EventManager`
* `MapDatabase`

## Design Principle

`SaveManager` is the **persistence orchestration boundary**.

Systems own their runtime state and provide serialization interfaces. `SaveManager` determines when that state is captured, how it is secured, where it is stored, and in what order it is restored.

This keeps save/load logic centralized while preventing individual gameplay systems from becoming responsible for filesystem management or cross-system restoration sequencing.
