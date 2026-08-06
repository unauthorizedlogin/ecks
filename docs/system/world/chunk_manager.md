# 🧩 Chunk Manager

## Overview

The **Chunk Manager** controls dynamic world chunk loading for large, grid-based environments.

It manages the relationship between the active **Map**, **CameraGrid**, and streamed chunk scenes, allowing the world to load only the required portions of the map around the player.

The system supports:

* 🗺️ Grid-based world streaming
* 📦 Asynchronous chunk loading
* 🧹 Automatic chunk unloading
* 🎯 Distance-based LOD selection
* 📐 Configurable world bounds
* 💾 Chunk destruction persistence
* 🚪 Spawn-point resolution during teleportation
* 🔄 Runtime rebinding when changing maps

Chunk configuration is provided by the active `MapResource`, keeping world streaming behavior data-driven rather than hardcoded into the manager.

---

# 🧠 Core Responsibilities

The Chunk Manager provides:

* Chunk loader initialization
* Map and camera binding
* Player chunk-position resolution
* Chunk range evaluation
* LOD selection
* Asynchronous scene loading
* Chunk instantiation and placement
* LOD replacement
* Unloading of unused chunks
* Chunk destruction serialization
* Chunk destruction restoration
* Teleport spawn resolution

The manager owns **streaming state**, while the `MapResource` defines the configuration used to perform that streaming.

---

# ⚙️ Initialization

```gdscript
func initialize() -> void
```

Registers the Chunk Manager as an active system.

Initialization is guarded by:

```gdscript
is_initialized
```

Output:

```text
🧩 ChunkManager initialized
```

---

# 🌍 World Binding

## `init_chunk_loader()`

```gdscript
func init_chunk_loader(
    _map: Node,
    _camera: Node,
    map_data: MapResource
) -> void
```

Binds the manager to the active world and loads its streaming configuration from `MapResource`.

The manager receives:

* Active map
* CameraGrid
* Chunk size
* Load radius
* Minimum bounds
* Maximum bounds
* Chunk scene template
* Chunk data template
* LOD distances

This allows different maps to provide different streaming configurations without changing the manager itself.

Example:

```text
MapResource
     ↓
ChunkManager
     ├── Chunk Size
     ├── Load Radius
     ├── World Bounds
     ├── Scene Template
     ├── Data Template
     └── LOD Distances
```

---

# 🔄 Runtime Reset

## `reset_chunk_manager()`

Performs a complete streaming reset.

Handles:

* Unloading all active chunks
* Clearing loaded chunk tracking
* Removing map references
* Removing camera references
* Clearing scene/data templates
* Resetting loader initialization state

This is used when the active map changes.

---

# 🧭 Player Chunk Resolution

The player's world position is converted into a grid coordinate.

Conceptually:

```text
World Position
      ↓
Chunk Size
      ↓
Grid Position
```

For example:

```text
Player:
(1400, 700)

Chunk Size:
512 × 512

Chunk:
(2, 1)
```

The resulting grid position becomes the center point for determining which chunks should remain loaded.

---

# 📦 Chunk Loading System

## `update_chunk_loading()`

Evaluates the world around the player and determines which chunks should exist.

The manager:

1. Validates streaming configuration
2. Validates map and camera references
3. Determines the player's current chunk
4. Scans the configured world bounds
5. Calculates distance to each chunk
6. Selects an appropriate LOD
7. Loads required chunks
8. Unloads chunks outside the active range

The resulting state is maintained through:

```gdscript
loaded_chunks
```

---

# 🎯 LOD Selection

The manager supports multiple levels of detail.

Current implementation uses:

```text
LOD0
LOD1
LOD2
```

LOD selection is based on distance from the player's current chunk.

Conceptually:

```text
Player
  │
  ├── Near       → LOD0
  │
  ├── Mid-range  → LOD1
  │
  └── Far        → LOD2
```

LOD distances are supplied by `MapResource`.

This allows individual maps to define their own streaming characteristics.

---

# 🧩 Chunk Identity

Loaded chunks use a composite identifier:

```text
X_Y_LOD
```

Example:

```text
2_1_LOD0
2_2_LOD1
1_0_LOD2
```

This allows multiple LOD representations of the same grid position to be tracked independently while ensuring only one LOD remains active for a given chunk coordinate.

---

# ⚡ Asynchronous Loading

## `load_chunk_async()`

Chunks are loaded through Godot's threaded resource loading system.

Pipeline:

```text
Chunk Request
      ↓
Build Scene Path
      ↓
Threaded Resource Load
      ↓
Instantiate Scene
      ↓
Assign World Position
      ↓
Load Optional Chunk Data
      ↓
Attach To Map
      ↓
Register In loaded_chunks
```

The scene path is generated from the configured chunk template.

Example:

```text
Chunk Template
      ↓
X = 2
Y = 1
LOD = 0
      ↓
2_1_LOD0.tscn
```

---

# 📐 Chunk Placement

Loaded chunks are positioned using their grid coordinate and the configured chunk size.

```text
Grid Position × Chunk Size
             ↓
      World Position
```

This keeps chunk scenes aligned to the world grid regardless of which chunk is loaded.

---

# 📄 Chunk Data

Chunks may optionally reference separate `.tres` data resources.

The data path is generated independently from the scene path.

Pipeline:

```text
Chunk Scene
    +
Chunk Data Resource
    ↓
Chunk Instance
```

When supported by the chunk scene, the manager assigns:

```gdscript
chunk_data
lod_map
```

This separates chunk presentation from serialized or generated chunk data.

---

# 🔁 LOD Replacement

When a chunk changes LOD, the manager removes previously loaded versions of that grid position before loading the new representation.

Example:

```text
2_1_LOD1
     ↓
Player approaches
     ↓
Unload LOD1
     ↓
Load LOD0
```

This prevents multiple LOD representations of the same chunk from occupying the world simultaneously.

---

# 🧹 Chunk Unloading

## `unload_chunk()`

Removes a chunk from the active world and the manager's tracking dictionary.

Unused chunks are identified during `update_chunk_loading()`.

Pipeline:

```text
Chunk Outside Streaming Range
            ↓
unload_chunk()
            ↓
queue_free()
            ↓
Remove From loaded_chunks
```

This keeps the active world footprint bounded by the configured streaming range.

---

# 🚪 Teleport Spawn Integration

The Chunk Manager also participates in spawn-point resolution.

When a teleport operation specifies a pending spawn ID, newly loaded chunks are checked for the requested spawn point.

Flow:

```text
Teleport Request
      ↓
Pending Spawn ID
      ↓
Chunk Loaded
      ↓
Find Spawn Point
      ↓
Move Player
      ↓
Clear Pending Spawn ID
```

This allows teleport destinations to exist inside streamed chunks without requiring the entire map to be loaded first.

Save loading is respected so teleport spawning does not override player positioning during save restoration.

---

# 💾 Chunk State Persistence

The manager supports persistence for chunk-level world state.

Currently the primary persisted state is destruction.

Example:

```text
Chunk:
2_1_LOD0

State:
destroyed = true
```

---

## Serialization

```gdscript
func serialize_chunk_state() -> Dictionary
```

Collects the state of currently loaded chunks.

A chunk can expose:

```gdscript
is_destroyed()
```

to participate in persistence.

Serialized state follows the chunk identity:

```text
Chunk ID
    ↓
Destroyed State
```

---

## Deserialization

```gdscript
func deserialize_chunk_state(saved_state: Dictionary) -> void
```

Restores saved destruction state to currently loaded chunks.

Chunks exposing:

```gdscript
set_destroyed()
```

receive their saved state.

Invalid or freed chunk references are removed from active tracking.

---

# 🌍 World-Level Chunk Serialization

The manager also provides a broader serialization path:

```gdscript
serialize_chunks()
deserialize_chunks()
```

This operates against chunks currently present in the active world scene and uses the `"chunks"` node group to identify them.

This provides an additional world-state persistence layer for chunk-specific changes.

---

# 🗺️ World Boundary Control

Chunk scanning is constrained by:

```gdscript
min_chunk_bounds
max_chunk_bounds
```

This prevents the streaming system from evaluating an unlimited grid.

Conceptually:

```text
┌───────────────────────────────┐
│       Maximum Chunk Bounds    │
│                               │
│    ┌─────────────────────┐    │
│    │   Active Streaming  │    │
│    │       Region        │    │
│    └─────────────────────┘    │
│                               │
│       Minimum Bounds          │
└───────────────────────────────┘
```

The bounds are defined by `MapResource`, allowing individual maps to have different playable dimensions.

---

# 🔗 System Relationships

```text
                     MapResource
                          │
                          │ Streaming Configuration
                          ↓
                    ChunkManager
                          │
             ┌────────────┼────────────┐
             ↓            ↓            ↓
          Map        CameraGrid    SaveManager
             │            │            │
             │            ↓            │
             │         Player          │
             │                         │
             ↓                         ↓
        Chunk Scenes              Chunk State
             │                         │
             └──────────┬──────────────┘
                        ↓
                 Active World Chunks
```

The manager also integrates with:

```text
TeleportManager
      ↓
Spawn Resolution
```

and:

```text
WorldManager
      ↓
Map Transition
      ↓
Chunk Manager Reset / Rebind
```

---

# 🧠 Architectural Role

The Chunk Manager is a **world streaming service**, not a map-definition system.

`MapResource` defines **what streaming configuration a map requires**.

`ChunkManager` determines **what chunks should currently exist**.

`Map` provides **the world container where chunks are placed**.

`CameraGrid` provides **the player's spatial reference** used for streaming decisions.

This separation allows the same streaming manager to operate across maps with different sizes, chunk layouts, LOD configurations, and scene/data templates.

---

# 🚀 Future Expansion

The current implementation establishes the foundation for broader world streaming support.

Potential expansion includes:

* Dynamic LOD policies
* Larger world bounds
* Multiple chunk streaming strategies
* Background world generation
* More advanced persistence
* 3D chunk streaming
* Non-RPG world layouts
* Camera-driven streaming independent of a specific player implementation

The manager's dependence on `CameraGrid` as a spatial reference provides a natural expansion point for the broader **Launch Flow** architecture, where camera implementations can be supplied according to the type of game being launched.

---

# ✅ Design Rule

**ChunkManager owns runtime world streaming state.**

Maps define their streaming configuration through `MapResource`; the Chunk Manager evaluates that configuration and maintains the active chunk set.

World systems should not directly manage individual chunk loading and unloading outside this pipeline.

The architecture is intended to keep chunk streaming independent from the specific gameplay genre, with future camera and world implementations able to provide the spatial context required by the streaming system.
