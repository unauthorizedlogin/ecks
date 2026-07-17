# 🧱 Chunk Loader System

## Overview

The Chunk Loader System provides dynamic world streaming capabilities for large-scale maps.

It manages the loading, unloading, and updating of world chunks based on the player's position and camera location.

Built around the Map System and Chunk Manager, the loader allows creators to build expansive worlds while only keeping required sections active.

The system is fully data-driven, allowing chunk behavior, templates, boundaries, and loading rules to be configured per world.

---

# 🎯 Responsibilities

The Chunk Loader is responsible for:

- Initializing chunk streaming for a world
- Providing chunk configuration data
- Connecting maps with the Chunk Manager
- Controlling chunk loading updates
- Supporting LOD-based chunk loading
- Validating world streaming configuration

The Chunk Loader acts as the bridge between the active world scene and the global chunk management system.

---

# 🏗️ Architecture

The chunk pipeline:

```

World
│
├── Map
│
├── CameraGrid
│
└── ChunkLoader
│
▼
ChunkManager
│
▼
Chunk Scenes

````

The Chunk Loader does not directly manage chunk instances.

Instead, it configures and delegates loading behavior to the Chunk Manager.

---

# 📦 Data-Driven Configuration

Chunk behavior is controlled through exported properties.

Creators can configure:

- Chunk size
- Loading radius
- World boundaries
- Scene templates
- Data templates
- LOD distances

Example:

```gdscript
chunk_size:
512 x 512

chunk_load_radius:
3

lod_distances:
1, 2, 3
````

---

# 🗺️ Chunk Templates

Chunks use generated templates for scalable world creation.

## Scene Template

Defines the chunk scene structure.

Example:

```
chunk_{x}_{y}_LOD{level}.tscn
```

Generates:

```
chunk_0_0_LOD1.tscn
chunk_0_0_LOD2.tscn
chunk_0_0_LOD3.tscn
```

---

## Data Template

Defines chunk metadata.

Example:

```
chunk_{x}_{y}.tres
```

Stores:

* Spawn data
* Objects
* World information
* Persistent content

---

# 🌍 World Initialization

The Chunk Loader is intentionally initialized manually.

It does not automatically start during scene loading.

This prevents:

* Duplicate initialization
* Scene swap conflicts
* Invalid camera references

The world explicitly initializes the loader once the map and player are ready.

---

# 🔄 Initialization Flow

Startup sequence:

```
World Loaded
      │
      ▼
Map Generated
      │
      ▼
Player Spawned
      │
      ▼
Camera Target Assigned
      │
      ▼
ChunkLoader.initialize()
      │
      ▼
ChunkManager Configured
      │
      ▼
World Streaming Enabled
```

---

# 🎥 Camera Integration

The Chunk Loader uses the active camera target to determine loading position.

Before initialization it validates:

* Camera exists
* Camera target exists
* Player reference is valid

If required, the system can restore the camera target after world swaps.

---

# 🧱 Chunk Boundaries

Creators can define world limits.

Example:

```gdscript
min_chunk_bounds:
(-1, -1)

max_chunk_bounds:
(2, 2)
```

This allows:

* Small contained zones
* Large open worlds
* Controlled streaming areas

---

# 📏 Load Radius

The loader determines how many chunks surround the player remain active.

Example:

```
Load Radius: 3

+---+---+---+---+---+---+---+
|   |   |   |   |   |   |   |
+---+---+---+---+---+---+---+
|   | 3 | 2 | 1 | 2 | 3 |   |
+---+---+---+---+---+---+---+
|   | 2 | 1 | P | 1 | 2 |   |
+---+---+---+---+---+---+---+
|   | 3 | 2 | 1 | 2 | 3 |   |
+---+---+---+---+---+---+---+
```

Only required chunks remain active.

---

# 🧬 Level of Detail Support

The system supports multiple chunk detail levels.

Example:

```
LOD 1
High detail
Near player

LOD 2
Medium detail

LOD 3
Low detail
Far distance
```

This allows large environments without requiring every area to remain fully loaded.

---

# 🔒 Safety Validation

The loader performs validation before enabling streaming.

Checks include:

* Valid world reference
* Valid map reference
* Valid camera reference
* Valid player target
* Valid chunk scene template
* Valid chunk data template

Missing configuration prevents unsafe initialization.

---

# ⚙️ Runtime Updates

During gameplay the loader monitors:

* World active state
* Initialization state
* Camera target availability

Only after all requirements are met does it update chunk loading.

This prevents unnecessary processing during:

* Menus
* Loading screens
* Scene transitions

---

# 🛠️ Creator Workflow

Creators configure chunk streaming by:

1. Creating chunk scene templates
2. Creating chunk data resources
3. Defining world boundaries
4. Setting load radius
5. Configuring LOD distances
6. Assigning templates to the map/world resource

The framework handles runtime loading automatically.

---

# 🔗 System Integration

The Chunk Loader integrates with:

* Map System
* World System
* Chunk Manager
* Camera System
* Save System
* Persistence Systems
* Level Database

---

# 🏆 Framework Benefits

The Chunk Loader provides:

- ✅ Large world support
- ✅ Data-driven streaming
- ✅ Generated chunk workflows
- ✅ LOD support
- ✅ Safe world transitions
- ✅ Creator-configurable boundaries
- ✅ Integration with persistent worlds

---

# Summary

The Chunk Loader System enables scalable world creation by separating chunk configuration from runtime management.

Creators define how worlds are structured through data and templates, while the framework handles loading, unloading, and optimization.

Combined with the Map System and Chunk Manager, it provides the foundation for building large seamless environments without manually managing every loaded area.
