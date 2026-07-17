# 🌍 ChunkManager System

`al_chunk_manager.gd`

The `ChunkManager` handles dynamic world streaming for large grid-based environments. It manages asynchronous chunk loading, unloading, world isolation, LOD selection, chunk persistence, and player spawn placement.

---

# 🧱 Core Responsibilities

The ChunkManager provides:

- Dynamic chunk streaming
- Grid-based world loading
- Asynchronous scene loading
- Chunk unloading
- Level of Detail (LOD) support
- Multi-world isolation
- Chunk persistence hooks
- Teleport spawn placement
- Chunk data loading

---

# 🏗️ Base System

```gdscript
extends Node
````

The manager operates as a global world streaming service.

---

# 📦 Chunk Configuration

## Chunk Size

```gdscript
var chunk_size := Vector2i(512,512)
```

Defines the size of each world chunk in world units.

---

## Load Radius

```gdscript
var chunk_load_radius := 3
```

Controls how far chunks load around the player.

Example:

```
Radius 1:

[ ][ ][ ]
[ ][P][ ]
[ ][ ][ ]

3x3 loaded area
```

---

## LOD Distances

```gdscript
var lod_distances := [1,2,3]
```

Defines distance thresholds for future Level of Detail switching.

Current support:

```
LOD0 = closest
LOD1 = medium
LOD2 = farthest
```

---

# 🌎 World Isolation System

The ChunkManager supports multiple independent worlds.

Each world maintains:

```gdscript
WorldChunkState
```

---

# 🌍 WorldChunkState

Stores isolated runtime state.

## World References

| Variable      | Purpose                       |
| ------------- | ----------------------------- |
| `map`         | Active world map              |
| `camera_grid` | Player tracking camera system |

---

## Chunk Configuration

| Variable              | Purpose                |
| --------------------- | ---------------------- |
| `chunk_size`          | Chunk dimensions       |
| `chunk_load_radius`   | Streaming radius       |
| `min_chunk_bounds`    | World minimum grid     |
| `max_chunk_bounds`    | World maximum grid     |
| `chunk_path_template` | Scene loading template |
| `chunk_data_template` | Data resource template |
| `lod_distances`       | LOD thresholds         |

---

## Runtime State

| Variable                      | Purpose                    |
| ----------------------------- | -------------------------- |
| `loaded_chunks`               | Active chunk instances     |
| `initialized`                 | World initialization state |
| `chunk_ready`                 | Streaming readiness        |
| `chunk_update_missing_warned` | Prevents warning spam      |

---

# 🌐 World Configuration Registry

Worlds define their chunk templates.

Example:

```gdscript
"world:level:001":
{
"path":
"res://content/map/worlds/overworld/chunks/chunk_{0}_{1}_LOD{2}.tscn",

"data":
"res://content/map/worlds/overworld/chunk_data/chunk_{0}_{1}.tres"
}
```

Each world controls:

* Chunk scene location
* Chunk data location
* Streaming structure

---

# 🔄 World Switching

## set_world()

```gdscript
set_world(world_id)
```

Changes the active world configuration.

Process:

1. Set active world ID
2. Validate configuration
3. Load world chunk templates
4. Update ChunkManager state

---

# 🧠 Initialization

## initialize()

Bootstraps the manager.

Output:

```
🌍 ChunkManager initialized
```

---

# 📥 Chunk Loader Binding

## init_chunk_loader()

Connects ChunkManager to a world instance.

Receives:

```gdscript
_map
_camera
_chunk_size
_chunk_load_radius
_min_chunk_bounds
_max_chunk_bounds
_chunk_path_template
_chunk_data_template
_lod_distances
```

---

## Binding Process

1. Reset previous world state
2. Clear loaded chunks
3. Remove previous references
4. Bind new map
5. Bind new camera
6. Apply chunk configuration
7. Begin loading

---

# 🧹 World Cleanup

## reset_chunk_manager()

Performs full world cleanup.

Removes:

* Loaded chunks
* Map references
* Camera references
* Templates
* Runtime flags

Used during:

* World transitions
* Teleports
* Reloads

---

# 🧭 Dynamic Chunk Updating

## update_chunk_loading()

Main streaming loop.

Responsibilities:

* Determine player chunk position
* Calculate required chunks
* Select LOD level
* Load missing chunks
* Remove unused chunks

---

# 📍 Player Chunk Calculation

Player position:

```gdscript
global_position / chunk_size
```

Example:

```
Player:

X:1024
Y:512


Chunk:

X:2
Y:1
```

---

# 🧩 Chunk Selection

The manager scans world bounds:

```gdscript
for x in range(min,max)
for y in range(min,max)
```

Each chunk receives:

* Grid position
* Distance from player
* LOD level

---

# 🎯 LOD Selection

Current logic:

```text
Distance <= Load Radius
        |
        v
       LOD0

Distance <= LOD1 Range
        |
        v
       LOD1

Distance <= LOD2 Range
        |
        v
       LOD2
```

---

# 📦 Async Chunk Loading

## load_chunk_async()

Loads chunk scenes asynchronously.

Pipeline:

```
Request Scene
      |
      v
Threaded Loading
      |
      v
Instantiate Chunk
      |
      v
Apply Position
      |
      v
Load Chunk Data
      |
      v
Add To World
```

---

# 🗂️ Chunk Naming

Loaded chunks use:

```
X_Y_LOD#
```

Example:

```
0_1_LOD0
2_-1_LOD1
```

---

# 🔁 LOD Replacement

Only one LOD version of a chunk exists at a time.

Example:

Before:

```
0_0_LOD1
```

Player approaches:

```
0_0_LOD0
```

Manager removes:

```
0_0_LOD1
```

and replaces it.

---

# 📄 Chunk Data Loading

Optional `.tres` chunk resources are loaded.

Example:

```
chunk_0_0.tres
```

Provides:

* Chunk metadata
* Persistent information
* Runtime configuration

---

# 🚀 Player Spawn Handling

After loading:

```gdscript
TeleportManager.next_spawn_id
```

is checked.

If valid:

1. Find spawn point
2. Move player
3. Clear teleport target

---

# 🧹 Chunk Unloading

## unload_chunk()

Safely removes chunk instances.

Process:

1. Validate chunk exists
2. Queue free node
3. Remove from tracking dictionary

---

# 💾 Chunk Persistence

## serialize_chunk_state()

Creates save data.

Currently tracks:

```gdscript
destroyed
```

Example:

```json
{
 "0_0_LOD0":
 {
   "destroyed": true
 }
}
```

---

## deserialize_chunk_state()

Restores saved chunk state.

Used for:

* Destroyed objects
* World changes
* Persistent gameplay changes

---

# 🏗️ Architecture

```
World
 |
 ├── ChunkManager
 |
 ├── WorldChunkState
 |       |
 |       ├── Map
 |       ├── CameraGrid
 |       ├── Loaded Chunks
 |       └── Templates
 |
 └── Chunk Instances
         |
         ├── Scene (.tscn)
         └── Data (.tres)
```

---

# 🔗 System Integrations

## CameraGrid

Provides:

* Player tracking
* World position
* Chunk calculation

---

## TeleportManager

Provides:

* Spawn target
* Post-load placement

---

## GameManager

Used to prevent teleport logic during save loading.

---

# 🌍 Current Capabilities

- ✅ Dynamic open-world streaming
- ✅ Async chunk loading
- ✅ Chunk unloading
- ✅ Multi-world isolation
- ✅ LOD framework
- ✅ Chunk resource loading
- ✅ Persistent chunk state hooks
- ✅ Teleport spawn support
- ✅ World-specific templates

