# 💾 Save System — Persistent Game State Architecture

The Save System provides the persistence foundation for the Ecks Framework.

It is divided into two cooperating layers:

1. **SaveManager**

   * Runtime save/load authority
   * Controls save slots
   * Serializes active systems
   * Restores gameplay sessions
   * Handles save migration

2. **SaveData**

   * Save file schema
   * Stores serialized game state
   * Defines persistent data structure

Together they create a complete save pipeline:

```text
Active Game Session

        |
        ↓

   SaveManager

        |
        ↓

Serialize Managers

        |
        ↓

   SaveData Resource

        |
        ↓

ResourceSaver

        |
        ↓

user://saves/
```

---

# 💾 SaveManager — Save Authority

`SaveManager` is the central authority for all persistence operations.

It is implemented as an Autoload Singleton.

Responsibilities:

* Save creation
* Save loading
* Slot management
* Autosaves
* Quicksaves
* Manual saves
* Serialization routing
* Save migration
* Save notifications

The SaveManager does not directly own gameplay data.

Instead, it requests each system to serialize its own state.

---

# 📦 SaveData — Save Schema Resource

`SaveData` defines the structure of a save file.

Each save slot is represented as:

```gdscript
SaveData
```

and stored using:

```gdscript
ResourceSaver.save()
```

Loaded using:

```gdscript
ResourceLoader.load()
```

The resource acts as a snapshot of the player's entire session.

---

# 🗂️ Save File Structure

Save files are stored:

```text
user://saves/
```

Format:

```text
save_slot_<index>.res
```

Example:

```text
save_slot_0.res
save_slot_1.res
save_slot_2.res
```

---

# 🎮 Save Slot Architecture

The framework supports expandable save slots.

Current configuration:

```gdscript
MAX_SLOTS = 12
```

Layout:

```text
Slot 0
 |
 └── Autosave


Slot 1
 |
 └── Quicksave


Slots 2-11
 |
 └── Manual Saves
```

---

# 🔄 Save Slot Cache

SaveManager maintains an in-memory cache:

```gdscript
var saves: Array
```

Structure:

```text
SaveManager

saves[]

[0] SaveData
[1] SaveData
[2] SaveData
[3] Empty
...
```

Loaded during initialization:

```gdscript
load_all_slots()
```

This allows:

* fast save menu loading
* slot previews
* availability checks

---

# 🔔 Save Update Events

After saving:

```gdscript
signal save_data_updated
```

is emitted.

Used by:

* Load Menu
* Save Menu
* Slot UI

Flow:

```text
Save Complete

      ↓

save_data_updated

      ↓

Refresh Save UI
```

---

# 💾 Save Types

The system supports three save categories.

---

# 🧠 Autosave

Handled through:

```gdscript
save_autosave()
```

Uses:

```text
Slot 0
```

Designed for:

* checkpoints
* quest progression
* world persistence

---

# ⚡ Quicksave

Handled through:

```gdscript
save_quick()
```

Uses:

```text
Slot 1
```

Designed for:

* player-controlled quick saves
* hotkeys
* temporary progress

---

# 🎮 Manual Saves

Created through:

```gdscript
save_new_game()
```

Automatically finds:

```text
First available slot
```

Range:

```text
2 → 11
```

---

# 📸 Save Snapshot Pipeline

When a save occurs:

```text
Save Request

      |
      ↓

Create SaveData

      |
      ↓

Capture GameManager State

      |
      ↓

Capture Player State

      |
      ↓

Serialize World

      |
      ↓

Serialize Managers

      |
      ↓

Write .res File

      |
      ↓

Update Cache

      |
      ↓

Notify UI
```

---

# 🧠 Session Metadata

SaveData stores global session information.

Fields:

```gdscript
player_name
selected_class_id
difficulty
current_level_id
total_playtime
last_save_time
```

Example:

```text
Save Slot

 ├── Player Name
 ├── Class
 ├── Difficulty
 ├── Current Level
 └── Playtime
```

---

# 🧍 Player Persistence

The save system stores:

```gdscript
player_position
```

This allows:

```text
Save

Player Location
      |
      ↓
SaveData


Load

SaveData
      |
      ↓
Restore Position
```

---

# 🌍 World Persistence

The world state system stores:

```gdscript
world_state
```

Designed for:

* world flags
* NPC states
* interaction states
* future persistence data

Example:

```json
{
 "door_open": true,
 "boss_defeated": true
}
```

---

# 🧱 Chunk Persistence

Procedural world changes are stored separately:

```gdscript
chunk_state
```

Connected with:

```gdscript
ChunkManager.serialize_chunk_state()
```

and:

```gdscript
ChunkManager.deserialize_chunk_state()
```

Architecture:

```text
ChunkManager

      |
      ↓

Chunk State Dictionary

      |
      ↓

SaveData

      |
      ↓

Disk
```

Allows:

* destroyed terrain
* modified chunks
* persistent world edits

---

# 🎒 Inventory Persistence Layer

SaveData contains dedicated storage for item systems.

Supported systems:

```text
Inventory
Equipment
Ammo
Consumables
Potions
Currency
Gems
Ingredients
Metals
Minerals
Stones
```

---

# 🔗 Manager Serialization Architecture

Each system owns its serialization logic.

Example:

```text
InventoryManager

serialize_inventory()
        |
        ↓
inventory_data
```

Loading:

```text
inventory_data

        ↓

deserialize_inventory()

        ↓

Inventory Restored
```

---

# ⚔️ Equipment Persistence

Equipment is saved through:

```gdscript
equipment_data
```

Handled by:

```gdscript
EquipmentManager
```

Supports:

* equipped weapons
* armor
* accessories
* custom equipment slots

---

# 📜 Quest Persistence

Quest progression is stored:

```gdscript
quest_data
```

Controlled by:

```gdscript
QuestManager
```

Preserves:

* active quests
* completed quests
* objective states
* quest progress

---

# 📈 Character Progression Persistence

Character progression stores:

```gdscript
current_level
current_total_xp
```

Connected to:

```text
PlayerLevelComponent
```

Save:

```text
PlayerLevelComponent

        ↓

SaveData
```

Load:

```text
SaveData

        ↓

PlayerLevelComponent
```

---

# 📤 Load Pipeline

Loading restores the complete player session.

Flow:

```text
Select Save Slot

        |
        ↓

Load SaveData

        |
        ↓

Restore GameManager

        |
        ↓

Restore Managers

        |
        ↓

Load World

        |
        ↓

Find Player

        |
        ↓

Restore XP

        |
        ↓

Resume Gameplay
```

---

# 🧪 Preloaded System Restoration

Before gameplay resumes:

```text
SaveData

 |
 +── AmmoManager
 |
 +── InventoryManager
 |
 +── EquipmentManager
 |
 +── CurrencyManager
 |
 +── PotionManager
 |
 +── QuestManager
 |
 +── ChunkManager
```

Each system reconstructs itself.

---

# 🔄 Save Version Migration

The save system supports format upgrades.

Controlled by:

```gdscript
CURRENT_SUPPORTED_VERSION
```

Migration flow:

```text
Load Save

      |
      ↓

Check Version

      |
      ↓

Migration Required?

      |
      ↓

Apply Conversion

      |
      ↓

Rewrite Save
```

Example:

```text
Save Version 0

missing new fields

        ↓

Migration

        ↓

Save Version 1
```

---

# 🏗️ Complete Save Architecture

```text
                    Gameplay

                       |
                       ↓

                 SaveManager

                       |
      +----------------+----------------+
      |                |                |
      ↓                ↓                ↓

 GameManager     ChunkManager    Gameplay Managers

      |                |                |
      ↓                ↓                ↓

 Session Data    World Data     System Data


              ↓

          SaveData

              ↓

       ResourceSaver

              ↓

       user://saves/
```

---

# Framework Role

The Save System provides:

✅ resource-based save files
✅ expandable save schema
✅ autosave support
✅ quicksave support
✅ manual save slots
✅ persistent world state
✅ chunk persistence
✅ inventory persistence
✅ equipment persistence
✅ quest persistence
✅ XP persistence
✅ save version migration
✅ centralized save authority

The architecture keeps persistence modular:

* **SaveManager owns the save process**
* **SaveData defines the storage format**
* **Gameplay systems own their own serialization**

This allows the framework to continue expanding without rewriting the save system every time a new gameplay feature is added.
