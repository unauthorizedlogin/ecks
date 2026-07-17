# 🗺️ Map Database Architecture

## Overview

The Map Database provides the centralized runtime authority for all world map resources.

Originally built around the concept of "levels", the system is being migrated toward a broader **Map architecture**. The term **Map** is used intentionally to avoid confusion with player progression levels, experience levels, and character leveling systems.

The Map Database manages:

* World maps
* Regions
* Dungeon maps
* Injectable maps
* Overworld areas
* Future expandable world content

The architecture separates:

1. **Map Database API**
2. **Map Resource Database**
3. **Generated Map Index**

This follows the same generator-driven database architecture used throughout the engine.

---

# 🏗️ Architecture Structure

```
MapDatabase (Autoload)
        |
        |
        v
MapResourceDatabase
        |
        |
        v
Generated Map Index
        |
        |
        v
MapResource (.tres)
```

---

# 🧠 MapDatabase

## Responsibilities

`al_map_database.gd` is the gameplay-facing interface for world map access.

It is responsible for:

* Initializing map data
* Tracking the currently active map
* Providing map queries
* Resolving map IDs and scene paths
* Exposing map resources to gameplay systems

It does **not**:

* Scan directories
* Handle filesystem operations
* Load resources directly
* Manage scene transitions
* Handle chunk streaming
* Control world generation

---

# 🚀 Initialization Pipeline

When initialized:

1. MapDatabase starts loading.
2. MapResourceDatabase loads generated map resources.
3. Every indexed map resource is registered.
4. Maps become available through the runtime API.

Example:

```
MapDatabase.initialize()

        ↓

MapResourceDatabase.load_all()

        ↓

map_index.gd

        ↓

MapResource registration

        ↓

Runtime map lookup available
```

---

# 📍 Current Map State

The database maintains:

```gdscript
current_level_id
```

This value represents the active **map identifier**.

The naming is currently being migrated from the previous Level terminology.

Future conversion:

```
current_level_id
        ↓
current_map_id
```

The purpose remains the same:

* Track current world location
* Provide active map information
* Allow systems to query current map data

---

# 📥 Core Map API

## Set Current Map

```gdscript
set_current_level(id)
```

Sets the active map by ID.

Validation occurs before assignment:

* Map must exist
* Invalid IDs are rejected

---

## Get Current Map

```gdscript
get_current_level()
```

Returns the active map ID.

Future:

```gdscript
get_current_map()
```

---

## Get Current Map Data

```gdscript
get_current_level_data()
```

Returns the complete `MapResource`.

Contains:

* Map ID
* Scene reference
* World grouping
* Region grouping
* Additional map metadata

---

## Retrieve Map Data

```gdscript
get_level_data(id)
```

Provides direct access to any registered map.

---

## Retrieve Scene Path

```gdscript
get_level_path(id)
```

Returns the scene path associated with a map.

Example:

```
world_overworld_001

↓

res://world/overworld/world.tscn
```

---

# 🔎 Path Resolution

The database supports resolving map IDs from scene paths.

Function:

```gdscript
get_level_id_from_path(path)
```

Handles:

* Normal resource paths
* UID based resource references
* Scene path matching

Example:

```
res://world/dungeon/desert.tscn

        ↓

desert_dungeon_map
```

This allows systems such as:

* Save/load
* Scene transitions
* World persistence

to identify maps reliably.

---

# 🗂️ Map Resource Database

## Responsibilities

`map_resource_database.gd` manages the internal map cache.

It handles:

* Loading indexed map resources
* Registering maps
* Duplicate validation
* World organization
* Region organization

It maintains two primary caches:

---

# 🗺️ Map Registry

## map_map

Direct map lookup by ID.

Example:

```
map_map

world_overworld_001
        |
        v
MapResource
```

Used for:

* Loading maps
* Save systems
* Runtime lookup
* World navigation

---

# 🌎 World Registry

## world_map

Groups maps by world and region.

Structure:

```
world_map

Overworld
 ├── Forest
 │     ├── forest_map_001
 │
 └── Desert
       ├── desert_map_001


DungeonWorld
 └── Caves
       ├── cave_map_001
```

Used for:

* World selection
* Region filtering
* Map browsers
* Future world systems

---

# 📦 Resource Registration

Every map resource is validated before registration.

Validation includes:

## Missing Map ID Protection

Resources require:

```gdscript
level_id
```

Future:

```gdscript
map_id
```

Missing IDs are rejected.

---

## Duplicate Protection

Duplicate IDs are prevented:

```
❌ Duplicate level_id: world_overworld_001
```

This guarantees every map has a unique runtime identity.

---

# 🗂️ Generated Map Index

## map_index.gd

The generated index contains every map resource path.

Example:

```gdscript
const LEVEL_PATHS := [

"res://data/databases/levels/world/world_overworld_001.tres",

"res://data/databases/levels/dungeon/desert/world_level_002.tres"

]
```

Future migration:

```gdscript
const MAP_PATHS := []
```

---

# ⚙️ Generator Workflow

Maps are not discovered at runtime.

The workflow is:

```
MapResource Created

        ↓

Map Index Generator

        ↓

map_index.gd

        ↓

MapDatabase Startup

        ↓

Runtime Map Cache
```

Benefits:

* Faster loading
* Export-safe references
* Deterministic behavior
* No runtime directory scanning

---

# 🌍 Map Categories

The current database already supports organizational grouping:

## World

Large-scale world collections.

Examples:

* Overworld
* DungeonWorld
* Future continents

---

## Region

Logical subdivisions inside worlds.

Examples:

* Forest
* Desert
* Caves
* Towns

---

## Map Types

Supported through resource organization:

* Overworld Maps
* Dungeon Maps
* Injectable Maps
* Future procedural/world instances

---

# 🔧 Expansion Support

Adding new maps requires:

1. Creating a new `MapResource`
2. Assigning map metadata
3. Placing it in the map database
4. Running the map index generator

No runtime database changes are required.

---

# ✅ Design Benefits

The Map Database provides:

* Centralized world authority
* Generator-driven loading
* Export-safe resource discovery
* World and region organization
* Deterministic map lookup
* Save-system compatibility
* Future chunk/world streaming support

The architecture creates a scalable foundation for large ARPG worlds while avoiding confusion between **world maps** and **player progression levels**.
