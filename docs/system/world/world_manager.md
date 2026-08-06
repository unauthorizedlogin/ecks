# 🌍 World Manager

## Overview

The **World Manager** is the central authority for managing active world state, map loading, map transitions, world reconstruction, and world-system initialization within the framework.

It separates **game state** from **world state**.

`GameManager` controls the overall game state such as `MENU`, `PLAYING`, `PAUSED`, and `LOADING`, while `WorldManager` controls which map is active and how the active world is constructed and synchronized.

The World Manager coordinates:

* 🗺️ Map loading
* 🚪 Map transitions
* 📍 Spawn point management
* 🧍 Player placement
* 🎥 Camera attachment
* 🧩 Chunk system initialization
* 🌦️ Environment system integration
* 🎨 UI initialization
* ⚙️ World system synchronization
* 💾 World state serialization

---

# 🧠 Core Responsibilities

The World Manager provides:

* World initialization
* Active map tracking
* Map loading
* Map transition validation
* Map scene resolution
* World reconstruction
* Player placement
* Camera synchronization
* Chunk Manager initialization
* Environment Manager integration
* World UI initialization
* World activation state
* World save/load state

The manager does not own global game state.

That responsibility belongs to `GameManager`.

---

# ⚙️ Initialization

```gdscript
func initialize()
```

Registers the World Manager as an active framework system.

Prevents duplicate initialization.

Output:

```text
🌍 WorldManager initialized
```

---

# 🗺️ World State

The World Manager maintains the runtime state required to identify and reconstruct the active world.

```gdscript
var current_map_id: String = ""

var pending_spawn_point_name: String = ""
var pending_spawn_position: Vector2 = Vector2.ZERO
var current_save_data: SaveData = null

var world_active := false
```

The primary world identity is the active `map_id`.

This allows world state to be referenced through stable database identifiers rather than direct scene paths.

---

# 🔁 Map Loading

```gdscript
func load_map(map_id: String)
```

Loads the framework world wrapper and prepares it to resolve and construct the requested map.

The World Manager validates the requested `map_id` through `MapDatabase`.

```text
Map ID
  ↓
MapDatabase
  ↓
World Wrapper
  ↓
Map Resolution
  ↓
World Reconstruction
```

The world wrapper provides the runtime environment in which the selected map is generated.

This separates:

**World**

> The framework-level container responsible for hosting the active map.

**Map**

> The actual playable world-space content selected through `MapResource`.

---

# 🚪 Map Transitions

```gdscript
func change_map(new_map_id: String, spawn_point: String = "")
```

Handles runtime transitions between maps without rebuilding the entire application flow.

The transition pipeline validates the requested map before modifying world state.

```text
Requested Map ID
        ↓
Validate ID
        ↓
MapDatabase
        ↓
Resolve Map Scene
        ↓
Reset Chunk System
        ↓
Generate Map
        ↓
Rebuild World
        ↓
Attach Player
        ↓
Attach Camera
        ↓
Initialize World Systems
        ↓
Activate World
```

Invalid map IDs are rejected before the transition proceeds.

The manager also rejects direct scene paths and resource identifiers being passed as map IDs.

---

# 🛡️ Map Validation

Map transitions perform several safety checks.

### Empty ID

```text
change_map("")
```

is rejected.

### Invalid Resource Path

Values resembling:

```text
uid://...
*.tscn
```

are rejected as invalid map identifiers.

### Unknown Map

The requested ID must exist within `MapDatabase`.

```gdscript
MapDatabase.has_map(new_map_id)
```

This establishes `map_id` as the authoritative identifier used by the runtime world system.

---

# 🗺️ Map Resolution

Map scene paths are resolved through `MapDatabase`.

```gdscript
var map_path: String = MapDatabase.get_map_path(new_map_id)
```

The World Manager does not require callers to know the physical scene path.

```text
map_id
   ↓
MapDatabase
   ↓
MapResource
   ↓
scene path
   ↓
PackedScene
```

This maintains separation between runtime world logic and content storage.

---

# 🌍 World Reconstruction

```gdscript
func rebuild_world(world: Node, spawn_point_name: String = "")
```

Reconstructs the active world after a map has been loaded or changed.

The reconstruction process establishes the runtime relationships between:

* Map
* Player
* Camera
* Environment
* Chunks
* UI
* Difficulty

The world remains inactive until the reconstruction process completes successfully.

```text
World
 ↓
Map
 ↓
Player
 ↓
Camera
 ↓
Environment
 ↓
Chunks
 ↓
UI
 ↓
World Active
```

---

# 🧹 Chunk System Reset

Before reconstructing a map, the existing chunk state is cleared.

```gdscript
ChunkManager.reset_chunk_manager()
```

This prevents chunks belonging to the previous map from remaining attached to the newly active map.

When the map uses world systems, the World Manager subsequently initializes the Chunk Manager using the new map's `MapResource`.

---

# 🧍 Player Placement

The World Manager obtains the active player through `PlayerManager`.

```gdscript
var player = PlayerManager.get_or_create_player()
```

The player is detached from its previous parent and inserted into the active map's spawn hierarchy.

Spawn resolution follows this order:

```text
Named Spawn Point
        ↓
First Available Spawn Point
        ↓
Map Root
```

A pending saved position takes priority when one exists.

This allows both normal map transitions and save-game restoration to use the same world reconstruction pipeline.

---

# 🎥 Camera Integration

Once the player has been placed into the active map:

```gdscript
CameraManager.attach_player(player)
```

The Camera Manager then establishes the player as the active camera target.

The World Manager therefore coordinates **when** the camera should attach, while `CameraManager` owns camera behavior itself.

```text
WorldManager
     ↓
Player Placement
     ↓
CameraManager
     ↓
Camera Target
```

---

# 🌦️ Environment Integration

Maps may opt into the framework's world systems through:

```gdscript
map.uses_world_systems
```

When enabled, the World Manager refreshes the environment:

```gdscript
EnvironmentManager.refresh_environment()
```

Maps that do not use world systems bypass environmental initialization.

This allows the framework to support maps that do not require the full world presentation stack.

```text
Map
 ↓
uses_world_systems?
 ├── YES → Environment + Chunk Systems
 └── NO  → Skip World Systems
```

---

# 🧩 World System Initialization

```gdscript
func init_world_systems(world: Node)
```

Initializes systems that depend on the active map and camera being fully established.

The current `MapResource` is retrieved through `MapDatabase`.

```text
Current map_id
      ↓
MapDatabase
      ↓
MapResource
      ↓
WorldManager
      ↓
ChunkManager
```

The Chunk Manager receives:

* Active map
* Active camera
* Chunk configuration
* Chunk scene template
* Chunk data template
* LOD configuration
* Chunk boundaries

This ensures chunk streaming is configured from the currently active map's data rather than hardcoded world settings.

---

# 🎨 UI Integration

Once the map, player, camera, and world systems have been established:

```gdscript
UIManager.init_ui(world)
```

The UI Manager is initialized against the active world.

This places UI initialization within the world reconstruction lifecycle rather than requiring individual maps to manually initialize global UI systems.

---

# 🌍 World Activation

The World Manager maintains:

```gdscript
var world_active := false
```

The world begins reconstruction in an inactive state.

After the map, player, camera, environment, chunks, and UI have been synchronized, the manager marks the world active.

```text
World Created
      ↓
Map Ready
      ↓
Player Ready
      ↓
Camera Ready
      ↓
World Systems Ready
      ↓
UI Ready
      ↓
World Active
```

This provides a clear synchronization boundary for systems that depend on a fully constructed world.

---

# 🎮 Game State Coordination

The World Manager does not own game state.

Instead, it communicates world transitions to `GameManager`.

```gdscript
GameManager.set_state(GameManager.GameState.LOADING)
```

After reconstruction:

```gdscript
GameManager.set_state(GameManager.GameState.PLAYING)
```

This establishes the architectural separation:

**GameManager**

> Owns global game state.

**WorldManager**

> Owns active world state.

---

# 🔔 Map Changed Signal

```gdscript
signal map_changed(map)
```

The World Manager broadcasts map transitions through the `map_changed` signal.

This allows other systems to respond to world changes without directly coupling themselves to the map-loading implementation.

---

# 💾 World Serialization

```gdscript
func serialize_world() -> Dictionary
```

The World Manager exposes the minimum persistent world identity required to reconstruct the active world.

Current serialized state:

```gdscript
{
    "current_map_id": current_map_id
}
```

---

# 💾 World Deserialization

```gdscript
func deserialize_world(state: Dictionary)
```

Restores the active map identity from saved world state.

```text
SaveData
   ↓
World State
   ↓
current_map_id
   ↓
WorldManager
   ↓
Map Reconstruction
```

This allows save data to identify the map without storing direct scene references.

---

# 🔗 System Relationships

```text
                    GameManager
                         |
                  Game State
                         |
                         ↓
                   WorldManager
                         |
        ┌────────────────┼────────────────┐
        ↓                ↓                ↓
   MapDatabase     PlayerManager    CameraManager
        |                |                |
    MapResource        Player          Camera
        |                |                |
        └────────────┬───┴────────────────┘
                     ↓
                  Active Map
                     |
          ┌──────────┼──────────┐
          ↓          ↓          ↓
   ChunkManager  Environment  UIManager
```

The World Manager acts as the orchestration layer connecting these systems during world construction and transitions.

---

# 🧭 System Boundaries

| Responsibility           | System              |
| ------------------------ | ------------------- |
| Global game state        | Game Manager        |
| Active map state         | World Manager       |
| Map definitions          | `MapResource`       |
| Map lookup               | Map Database        |
| Player lifecycle         | Player Manager      |
| Camera behavior          | Camera Manager      |
| Chunk streaming          | Chunk Manager       |
| Environment presentation | Environment Manager |
| UI initialization        | UI Manager          |
| Difficulty configuration | Difficulty Database |
| Save data                | Save Manager        |

The World Manager coordinates these systems but does not absorb their individual responsibilities.

---

# 🌱 Data-Driven World Architecture

World construction is driven by map identity and map resource data.

```text
map_id
   ↓
MapDatabase
   ↓
MapResource
   ↓
WorldManager
   ↓
World Systems
   ├── Player
   ├── Camera
   ├── Environment
   ├── Chunks
   └── UI
```

This allows maps to define their world requirements through data while the World Manager provides the common runtime construction pipeline.

---

# 🚀 Launch Flow Integration

The World Manager is initialized as part of the centralized Launch Flow.

The Launch Flow establishes the framework systems first, after which the World Manager becomes responsible for constructing the active playable world.

```text
Launch Flow
    ↓
Framework Initialization
    ↓
WorldManager
    ↓
Load Map
    ↓
Rebuild World
    ↓
Initialize World Systems
    ↓
Gameplay Ready
```

This establishes a consistent world startup path independent of individual map scenes.

---

# 🔮 Future Expansion

The World Manager is intentionally structured as a world orchestration layer rather than a 2D-specific implementation.

Future world configurations can provide different combinations of:

* Camera systems
* Environment systems
* Streaming systems
* Map representations
* Player controllers
* World presentation systems

The World Manager can therefore remain responsible for **world lifecycle and orchestration** while specialized managers provide the implementation for different game formats.

This supports the broader framework goal of allowing the Launch Flow to construct different types of games without making the core world lifecycle dependent on a single 2D RPG implementation.

---

# ✅ Design Rule

**WorldManager is the central authority for active world state and world reconstruction.**

`GameManager` determines **what state the game is in**.

`WorldManager` determines **what world is active and how that world is constructed**.

`MapDatabase` and `MapResource` define **what map should be loaded**.

Specialized managers determine **how individual world systems operate**.

Future world systems should integrate through this orchestration boundary rather than embedding world lifecycle logic directly into maps or the Launch Flow.
