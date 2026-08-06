# 🧍 Player Manager

## Overview

The **Player Manager** is the central authority for player lifecycle management.

It owns the active player reference, coordinates player creation and discovery, manages player position persistence, and orchestrates the player's runtime bootstrap sequence.

The Player Manager does not own individual gameplay systems. Instead, it connects the player to the systems required for a functional gameplay session.

This includes:

* 🧍 Player creation and lookup
* 💾 Player position persistence
* 🧠 Class and core identity initialization
* 🎒 Inventory integration
* ⚔️ Equipment integration
* 🔑 Requirement system integration
* 📜 Quest state synchronization
* 📊 Stat initialization and finalization
* ❤️ Vital component configuration
* 📈 Progression initialization
* 🖥️ UI binding
* 🌍 New-game startup flow

---

# 🧠 Core Responsibilities

The Player Manager provides:

* Centralized player ownership
* Player instance creation
* Active player lookup
* Player position access
* Save/load player data staging
* Player subsystem binding
* Player stat bootstrap
* Equipment synchronization
* Vital component configuration
* Progression initialization
* Player UI integration
* New-game initialization
* Final player bootstrap signaling

The manager acts as the **lifecycle coordinator** for the player without becoming the owner of the individual systems it initializes.

---

# ⚙️ Initialization

```gdscript
func initialize() -> void
```

Registers the Player Manager as an active system.

Initialization is guarded by:

```gdscript
is_initialized
```

Output:

```text
🧍 PlayerManager initialized
```

---

# 🧍 Player Instance Management

## `get_or_create_player()`

Creates the player when no active player instance exists.

The player scene is instantiated and stored as the manager's active reference.

If pending player data exists, position data is applied during creation.

Pipeline:

```text
Player Request
      ↓
Existing Player?
   ↙       ↘
 Yes        No
  ↓          ↓
Return    Instantiate
             ↓
       Apply Pending Data
             ↓
       player_ready
             ↓
        Return Player
```

The `player_ready` signal announces that the player instance has been created and registered.

---

# 🔎 Player Lookup

## `get_player()`

Provides centralized access to the active player.

The manager first checks its stored reference.

If no reference exists, it searches the current world scene recursively for the player node.

This provides resilience between:

* Dynamically instantiated players
* Scene-provided players
* World transitions
* Runtime player reconstruction

If no player can be located, the failure is reported through the Event Manager.

---

## Recursive Discovery

```gdscript
find_node_recursive()
```

Provides the internal scene traversal used by player lookup.

The function searches the active scene hierarchy until the requested player node is located.

This keeps scene discovery contained within the Player Manager rather than requiring other systems to know where the player exists in the world hierarchy.

---

# 📍 Player Position

## `get_player_position()`

Provides the canonical player position used by persistence systems.

The manager resolves the active player and returns its global position.

---

## `set_player_position()`

Restores a saved or externally supplied player position.

If the player provides a dedicated `set_position()` method, that interface is used.

Otherwise, the manager falls back to directly assigning `global_position`.

This allows player positioning to remain compatible with both specialized and standard player implementations.

---

# 💾 Pending Player Data

The manager maintains:

```gdscript
pending_player_data
```

This provides a staging area for player data loaded before the player instance exists.

Current persisted data includes:

```text
Position
```

Flow:

```text
SaveManager
     ↓
deserialize_player()
     ↓
pending_player_data
     ↓
Player Creation
     ↓
Apply Position
```

This separates **save loading** from **player instantiation timing**.

Future player persistence can expand this structure to include:

* Stats
* Equipment
* Abilities
* Additional player state

---

# 🚀 Player Bootstrap Architecture

## `bootstrap_player()`

The bootstrap process establishes the player's complete runtime state after the player has entered the world.

The sequence is intentionally staged:

```text
Bootstrap Player
      ↓
Core Identity
      ↓
System Bindings
      ↓
Stats
      ↓
Equipment
      ↓
New Game Flow
      ↓
Vitals
      ↓
Finalize Stats
      ↓
Progression
      ↓
Spawn Finalization
      ↓
UI Binding
```

This establishes dependencies before systems that rely on those dependencies are finalized.

---

# 🧠 Core Player Initialization

## `_bootstrap_core()`

Establishes the player's fundamental identity.

Responsibilities:

* Assign selected class ID
* Resolve class data
* Assign class ID to the level component
* Validate required class configuration

Flow:

```text
GameManager.selected_class_id
          ↓
Player.class_id
          ↓
ClassDatabase
          ↓
Player.class_data
          ↓
LevelComponent.class_id
```

Bootstrap is halted if the player has no valid class configuration.

---

# 🔗 System Binding

## `_bootstrap_modules()`

Connects the player to the systems that require an active player reference.

The manager assigns the player to:

```text
InventoryManager
EquipmentManager
RequirementsManager
```

It also synchronizes the player's progression state with:

```text
QuestManager
```

This establishes the player as the active subject for systems that operate against player state.

---

# 🎒 Inventory Integration

The Player Manager does not manage inventory contents itself.

Instead, it establishes the player reference used by `InventoryManager`.

This maintains separation between:

```text
PlayerManager
     ↓
Player Lifecycle

InventoryManager
     ↓
Inventory State
```

---

# ⚔️ Equipment Integration

The Player Manager establishes the player reference with `EquipmentManager`.

Equipment state is then synchronized during bootstrap through:

```gdscript
EquipmentManager.sync_equipment_state()
```

The synchronization occurs after a frame boundary to ensure the required player state and scene components are available.

---

# 🔑 Requirements Integration

The active player is assigned to `RequirementsManager`.

This allows item, quest, and other requirement validation to operate against the current player without other systems needing to manage their own player references.

---

# 📊 Stat Initialization

## `_bootstrap_stats()`

Initializes the player's stat structures through the player itself:

```gdscript
player.safe_initialize_stats()
```

The Player Manager coordinates initialization but does not calculate the player's final stats directly.

---

# 📐 Stat Finalization

## `_finalize_stats()`

Finalizes the player's runtime stat state after initialization and equipment synchronization.

Pipeline:

```text
Player Stat Blocks
       ↓
StatManager.rebuild_stats()
       ↓
Player.apply_runtime_stats()
       ↓
PlayerManager.stats_finalized
```

The `stats_finalized` signal establishes a centralized lifecycle event indicating that the player's runtime stats are ready.

This allows other systems to respond to completed player stat initialization without owning or duplicating the bootstrap sequence.

---

# ❤️ Vital Component Bootstrap

## `_bootstrap_vitals()`

Configures the player's `VitalComponent` after the player's core identity and stat configuration are established.

The manager assigns:

```text
stat_owner
damage_team
```

The damage team is resolved from the player's class configuration.

The player's death signal is also connected to the Quest Manager.

Flow:

```text
Player Class
     ↓
ClassDatabase
     ↓
Damage Team
     ↓
VitalComponent
```

This keeps the Vital Component itself entity-agnostic while allowing the Player Manager to provide the player-specific configuration required by it.

---

# 📈 Progression Bootstrap

## `_bootstrap_progression()`

Initializes the player's progression system after stats have been finalized.

Responsibilities include:

* XP initialization
* Level growth event binding
* Maximum-level event binding

The Player Manager establishes the player's progression lifecycle without becoming the owner of progression calculations.

---

# 🆕 New Game Flow

## `_bootstrap_start_flow()`

Handles player-specific initialization that should occur only when starting a new game.

The flow is skipped when:

```text
Loading From Save
```

or:

```text
New Game Already Initialized
```

For a new game, the manager:

* Marks the new-game initialization state
* Grants class starting equipment
* Adds starting items to inventory
* Equips starting equipment
* Emits the world-ready message
* Starts the initial quest
* Starts automatic quests
* Creates the initial save

Flow:

```text
New Game
    ↓
Class Data
    ↓
Starting Equipment
    ↓
Inventory
    ↓
Equipment
    ↓
Starting Quest
    ↓
Auto Quests
    ↓
Initial Save
```

This keeps new-game setup separate from save restoration.

---

# 🖥️ UI Integration

## `_bootstrap_ui()`

Connects the player to the runtime UI after the gameplay systems have been initialized.

The manager locates:

```text
MenusUI
PlayerStats
PlayerHUD
```

and provides the active player to UI components that support player binding.

It also connects UI movement state signals to the player's movement controls.

This creates the relationship:

```text
UIManager
    ↓
Movement State
    ↓
PlayerControl
```

while keeping UI state ownership inside `UIManager`.

---

# 🖱️ Spawn Finalization

## `_finalize_spawn()`

Performs final player-session setup after the bootstrap sequence.

Currently this establishes the gameplay cursor used by the player session.

This stage provides a dedicated location for future spawn/session finalization without mixing it into core player initialization.

---

# 📡 Player Lifecycle Signal

## `player_ready`

Emitted when the Player Manager creates and registers a new player instance.

This provides an external lifecycle event for systems that need to react to player creation.

---

## `stats_finalized`

Emitted after:

* Stat blocks are rebuilt
* Runtime stats are applied

This represents the completion of player stat initialization.

Systems can therefore distinguish between:

```text
Player Exists
```

and:

```text
Player Is Fully Stat-Initialized
```

---

# 💾 Player Serialization

## `serialize_player()`

Produces the player-specific save representation.

Current persisted state:

```text
Position
```

The serialization boundary is intentionally centralized so additional player state can be added without requiring `SaveManager` to understand the player's internal structure.

Future expansion includes:

```text
Stats
Equipment
Abilities
```

---

# 🔄 Player Deserialization

## `deserialize_player()`

Loads saved player data into the manager's pending state.

The current format stores:

```text
position
```

Legacy save data using:

```text
player_position
```

is also recognized.

The manager therefore provides a compatibility boundary between saved player data and the current runtime player structure.

---

# 🔗 System Relationships

```text
                         GameManager
                              │
                       Class / Game State
                              │
                              ↓
                       PlayerManager
                              │
          ┌───────────────────┼───────────────────┐
          ↓                   ↓                   ↓
     ClassDatabase       PlayerControl       SaveManager
                              │                   │
                              │                   ↓
                              │            Player Serialization
                              │
        ┌─────────────┬───────┼────────┬───────────────┐
        ↓             ↓       ↓        ↓               ↓
 InventoryManager  Equipment  Stats  VitalComponent  LevelComponent
                    Manager   Manager
        │             │        │         │              │
        └─────────────┴────────┴─────────┴──────────────┘
                              │
                              ↓
                       Player Runtime
                              │
                ┌─────────────┴─────────────┐
                ↓                           ↓
          QuestManager                RequirementsManager
```

The Player Manager coordinates these relationships but does not absorb their responsibilities.

---

# 🧠 Architectural Role

The Player Manager is the **player lifecycle coordinator**.

It owns:

* The active player reference
* Player creation
* Player discovery
* Player bootstrap sequencing
* Player persistence boundaries
* Player lifecycle events

It does **not** own:

* Inventory state
* Equipment state
* Stat calculation
* Quest state
* Ability execution
* Combat resolution
* Progression calculations
* UI state

Those responsibilities remain with their respective systems.

The Player Manager establishes the connections between those systems and the active player.

---

# 🚀 Expansion Model

The Player Manager is structured around lifecycle coordination rather than a fixed player implementation.

Future expansion can support:

* Multiple player archetypes
* Alternate player scenes
* Additional player serialization
* Expanded player modules
* Different progression models
* Multiple gameplay modes
* Additional player-controlled entities

The manager's role remains consistent:

```text
Create
  ↓
Bind
  ↓
Initialize
  ↓
Finalize
  ↓
Persist
```

This allows the broader Launch Flow to remain responsible for assembling the required gameplay systems while the Player Manager remains responsible for establishing a valid player runtime.

---

# ✅ Design Rule

**PlayerManager is the single authority for player lifecycle.**

Systems should obtain the active player through the Player Manager rather than independently creating, discovering, or maintaining competing player references.

The Player Manager coordinates player initialization and system integration while individual managers retain ownership of their own domains.
