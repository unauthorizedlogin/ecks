# 🧩 Chunk Loader

## Overview

The **Chunk Loader** is the world-facing runtime component responsible for activating and driving dynamic chunk streaming within the active world.

It acts as the **scene-level integration layer** between the active world, `CameraManager`, `WorldManager`, and `ChunkManager`.

The Chunk Loader does not implement chunk streaming logic itself.

Instead, it:

* 🗺️ Resolves the active map configuration
* 🎥 Validates the active camera and camera target
* 🧩 Initializes `ChunkManager`
* 🔄 Drives chunk loading updates during gameplay
* ⏳ Waits for the world to become active
* 🛡️ Prevents chunk processing before initialization is complete

This keeps chunk streaming logic centralized in `ChunkManager` while allowing the world scene to participate in the framework's Launch Flow.

---

# 🧠 Core Responsibilities

The Chunk Loader provides:

* Chunk system initialization
* Active world integration
* Camera target validation
* Map configuration resolution
* Chunk Manager initialization
* Runtime chunk update driving
* World activation gating
* Scene-swap safety

The Chunk Loader does not:

* Load chunk scenes directly
* Determine which chunks should exist
* Manage LOD selection
* Unload chunks directly
* Store chunk persistence data
* Define map chunk configuration

Those responsibilities belong to `ChunkManager` and `MapResource`.

---

# ⚙️ Initialization

```gdscript
func initialize(world: Node)
```

Initializes the Chunk Loader against the currently active world.

Initialization is intentionally **manual** rather than automatic.

The loader does not initialize itself during `_enter_tree()` because world scenes may still be undergoing reconstruction when the node enters the scene tree.

```text
World Created
     ↓
World Rebuilt
     ↓
Camera Available
     ↓
Player Available
     ↓
ChunkLoader.initialize()
     ↓
ChunkManager Ready
```

This prevents chunk streaming from beginning against an incomplete world.

---

# 🌍 World Integration

The Chunk Loader receives the active world during initialization.

```gdscript
var map = world.get_map()
var camera = CameraManager.get_camera_grid()
```

The loader uses these references to establish the runtime dependencies required by `ChunkManager`.

```text
World
 ├── Map
 │
 └── Camera
       ↓
 ChunkLoader
       ↓
 ChunkManager
```

The world itself remains responsible for constructing the map and establishing the player.

---

# 🎥 Camera Integration

The Chunk Loader obtains the active camera through `CameraManager`.

```gdscript
CameraManager.get_camera_grid()
```

The camera must have a valid target before chunk streaming can begin.

If the camera target has been invalidated during a scene transition, the loader attempts to rebind it to the world player.

```text
CameraManager
      ↓
CameraGrid
      ↓
Camera Target
      ↓
ChunkLoader
      ↓
ChunkManager
```

The camera therefore provides the runtime positional reference used by the chunk system.

---

# 🗺️ Map Configuration

The Chunk Loader resolves the active `MapResource` through `MapDatabase`.

```gdscript
MapDatabase.get_map_data(
    WorldManager.current_map_id
)
```

This keeps chunk configuration data-driven.

The loader does not define chunk dimensions, bounds, templates, or LOD behavior itself.

Instead:

```text
WorldManager.current_map_id
          ↓
      MapDatabase
          ↓
      MapResource
          ↓
     ChunkManager
```

The active `MapResource` provides the configuration required by the chunk streaming system.

---

# 🧩 Chunk Manager Integration

Once the world, camera, camera target, and map data are validated, the Chunk Loader initializes `ChunkManager`.

```gdscript
ChunkManager.init_chunk_loader(
    map,
    camera,
    map_data
)
```

`ChunkManager` then becomes responsible for actual chunk streaming.

The separation is intentional:

**Chunk Loader**

> Connects the world to the chunk system.

**Chunk Manager**

> Performs chunk streaming and chunk state management.

---

# 🔄 Runtime Chunk Updates

During gameplay, the Chunk Loader drives the Chunk Manager's update cycle.

```gdscript
ChunkManager.update_chunk_loading()
```

The loader performs this update only when all required conditions are satisfied.

```text
World Active?
     ↓
   YES
     ↓
Loader Active?
     ↓
   YES
     ↓
Loader Initialized?
     ↓
   YES
     ↓
Camera + Target Valid?
     ↓
   YES
     ↓
ChunkManager.update_chunk_loading()
```

This keeps chunk processing synchronized with the world lifecycle.

---

# 🛡️ World Activation Gating

The Chunk Loader does not process chunks until:

```gdscript
WorldManager.world_active
```

is true.

This prevents chunk streaming from running while the World Manager is still reconstructing the world.

```text
World Reconstruction
        ↓
Player Placement
        ↓
Camera Attachment
        ↓
World Systems
        ↓
Chunk Loader
        ↓
World Active
        ↓
Chunk Updates
```

This establishes the World Manager as the authority over when the world is considered ready for runtime processing.

---

# 🚦 Active State

The loader maintains:

```gdscript
var active := false
```

This provides an additional runtime gate independent of world activation.

The loader must satisfy both:

```text
WorldManager.world_active
        AND
ChunkLoader.active
        AND
ChunkLoader.is_initialized
```

before it can drive chunk updates.

This allows the loader to remain initialized while temporarily disabling runtime chunk processing.

---

# 🔁 Scene Swap Safety

The Chunk Loader intentionally avoids automatic initialization.

```gdscript
func _enter_tree():
    pass
```

World scenes may be created and destroyed during map transitions.

Automatic initialization at scene entry could occur before:

* The map exists
* The camera is attached
* The player exists
* The correct `MapResource` is resolved
* The world has completed reconstruction

Manual initialization allows the Launch Flow and World Manager to control the correct initialization order.

---

# 🧱 Chunk Configuration

The loader exposes configuration fields for scene-level configuration and compatibility:

```gdscript
@export var chunk_size := Vector2i(512, 512)
@export var chunk_load_radius := 3
@export var min_chunk_bounds := Vector2i(-1, -1)
@export var max_chunk_bounds := Vector2i(2, 2)

@export var chunk_path_template := ""
@export var chunk_data_template := ""
@export var lod_distances := [1, 2, 3]
```

Runtime world configuration is ultimately sourced from `MapResource` and passed into `ChunkManager`.

This allows map-specific chunk configuration to remain data-driven rather than requiring each world scene to implement its own streaming logic.

---

# 🔗 System Relationships

```text
                    WorldManager
                         |
                  current_map_id
                         ↓
                    MapDatabase
                         |
                    MapResource
                         |
                         ↓
World ─────────→ ChunkLoader
                  /          \
                 /            \
                ↓              ↓
       CameraManager      ChunkManager
              |                |
         CameraGrid       Chunk Streaming
              |
           Player
```

The Chunk Loader acts as the integration boundary between the active world and the centralized chunk streaming system.

---

# 🧭 System Boundaries

| Responsibility           | System                      |
| ------------------------ | --------------------------- |
| World lifecycle          | World Manager               |
| Active map identity      | World Manager               |
| Map configuration        | `MapResource`               |
| Map lookup               | Map Database                |
| Camera lifecycle         | Camera Manager              |
| Camera targeting         | Camera Manager              |
| Chunk system integration | Chunk Loader                |
| Chunk streaming          | Chunk Manager               |
| Chunk LOD selection      | Chunk Manager               |
| Chunk loading/unloading  | Chunk Manager               |
| Chunk persistence        | Chunk Manager / Save System |

The Chunk Loader should remain lightweight and should not absorb chunk-streaming responsibilities from `ChunkManager`.

---

# 🌱 Data-Driven Chunk Architecture

Chunk configuration originates from the active map resource.

```text
Map ID
  ↓
MapDatabase
  ↓
MapResource
  ↓
ChunkLoader
  ↓
ChunkManager
  ↓
Chunk Streaming
```

This allows different maps to provide different:

* Chunk sizes
* Streaming radii
* Chunk boundaries
* LOD distances
* Scene templates
* Chunk data templates

without changing the Chunk Loader implementation.

---

# 🚀 Launch Flow Integration

The Chunk Loader is designed to participate in the centralized Launch Flow.

It is intentionally separated from the actual chunk implementation so the Launch Flow can establish the world and its dependencies before enabling chunk streaming.

```text
Launch Flow
    ↓
WorldManager
    ↓
World Construction
    ↓
Player + Camera
    ↓
ChunkLoader.initialize()
    ↓
ChunkManager
    ↓
World Active
    ↓
Runtime Chunk Updates
```

This allows chunk streaming to become a modular world capability rather than a hardcoded responsibility of individual map scenes.

---

# 🔮 Future Expansion

The Chunk Loader is positioned as an integration layer for future world-streaming architectures.

Future expansion can introduce different streaming implementations while retaining the same world-level initialization pattern.

Potential implementations include:

* Grid-based chunk streaming
* Large-world streaming
* Region streaming
* 3D terrain streaming
* Multi-layer world streaming
* LOD-driven world streaming
* Platform-specific streaming strategies

The World and Launch Flow layers can remain responsible for **when streaming is initialized**, while specialized managers determine **how world content is streamed**.

---

# ✅ Design Rule

**ChunkLoader is the world-facing integration layer for the chunk streaming system.**

The World Manager determines **when the world is ready**.

The Chunk Loader establishes **the connection between that world and the chunk system**.

`MapResource` defines **how the map should be streamed**.

`ChunkManager` determines **which chunks are loaded, unloaded, and maintained**.

Future streaming systems should preserve this separation rather than embedding streaming behavior directly into world scenes.
