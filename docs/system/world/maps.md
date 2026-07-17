# 🗺️ Map System

## Overview

The Map System provides the foundation layer for all playable areas within the game world.

Maps are built around a reusable base scene and data-driven configuration resources, allowing creators to generate, modify, and manage large numbers of world areas without manually rebuilding each scene.

Every map instance inherits the same core structure, allowing framework-wide changes to propagate across all maps while still supporting unique environments, gameplay rules, and world behaviors.

---

# 🎯 Responsibilities

The Map System is responsible for:

- Providing the base scene architecture for all maps
- Managing world environment connections
- Defining map metadata through resources
- Supporting chunk-based world streaming
- Controlling map-specific gameplay rules
- Handling persistence settings
- Providing creator-friendly map generation support

The Map System acts as the foundation layer between world loading, environmental systems, and gameplay content.

---

# 🏗️ Base Map Scene

All maps are instantiated from the shared `Map` base scene.

The base scene provides:

- Standard map root structure
- Environment integration
- Screen alignment references
- World registration
- Shared functionality for every map instance

Because every map inherits from this structure, system-wide improvements can be applied once and automatically benefit every generated map.

---

# 🧩 Map Scene Structure

Example structure:

```

Map
│
├── ScreenGridRef
│
├── EnvironmentArea
│
└── Map Content
├── Terrain
├── NPCs
├── Objects
├── Triggers
└── Gameplay Elements

````

Creators only need to build the unique world content.

The framework handles the shared infrastructure.

---

# 🌍 Data-Driven Map Resources

Each playable map is defined through a `LevelResource`.

Maps do not rely on hardcoded scene logic.

Instead, map behavior is controlled through editable data fields.

Example:

```gdscript
LevelResource
````

stores:

* Map identity
* World information
* Environment settings
* Streaming configuration
* Gameplay restrictions
* Persistence rules
* Presentation settings

---

# 🆔 Map Identity Data

Each map can define:

* Level ID
* Display name
* Save identifier
* Scene path
* Spawn point

Example:

```
level_id:
forest_region_01

save_tag:
forest_start

scene:
forest_map.tscn
```

This allows maps to be referenced safely by systems instead of relying on scene paths directly.

---

# 🌎 World Structure

Maps support hierarchical world organization.

Available metadata:

* World
* Region
* Biome

Example:

```
World
 └── Northern Kingdom
      └── Frost Valley
           └── Snow Biome
```

This allows future systems such as:

* World maps
* Region tracking
* Biome modifiers
* Environmental rules

to use the same data foundation.

---

# 🌦️ Environment Integration

Maps connect directly with the Environment System.

Supported data:

* Music profiles
* Ambient audio
* Weather profiles

Examples:

```
Music:
forest_theme

Ambient:
wildlife_day

Weather:
rainy_forest
```

The map can automatically request environment refreshes when required.

---

# 🧱 Chunk Streaming Support

The Map System is designed to support large-scale worlds through chunk-based loading.

Chunk data includes:

* Chunk profile
* Chunk dimensions
* Load radius
* World boundaries
* Scene templates
* Data templates
* Level of detail distances

Example:

```
Chunk Size:
512 x 512

Load Radius:
3

LOD:
1
2
3
```

This allows creators to build:

* Small handcrafted zones
* Large streamed worlds
* Seamless overworld environments

using the same architecture.

---

# 🎮 Gameplay Rules

Each map can define gameplay behavior.

Supported options:

## Mount Rules

```
allow_mounts
```

Controls whether mounts are available.

---

## Fast Travel Rules

```
allow_fast_travel
```

Controls teleportation availability.

---

## Combat Rules

```
combat_enabled
```

Allows safe areas, towns, and combat zones to share the same framework.

---

# 💾 Persistence Settings

Maps can control world persistence behavior.

Supported:

## Enemy Persistence

```
persistent_enemies
```

Controls whether enemy states survive leaving the map.

---

## Loot Persistence

```
persistent_loot
```

Controls whether collected or dropped items persist.

---

# 🖥️ Presentation Data

Maps also contain display-related information.

Supported:

* Display type
* Minimap availability
* Debug visualization color

Example:

```
Display Type:
Dungeon

Minimap:
Enabled
```

---

# ⚙️ World System Toggle

Each map can enable or disable global world features.

```gdscript
@export var uses_world_systems: bool = true
```

This allows the same map architecture to support:

## Full World Maps

Includes:

* Weather
* Environment
* Chunk loading
* Streaming systems

---

## Lightweight Maps

Examples:

* Menus
* Small interiors
* Special scenes

without requiring the full world pipeline.

---

# 🛠️ Creator Workflow

Creators can create maps through:

1. Duplicate or generate a Map scene
2. Add unique world content
3. Create a LevelResource entry
4. Assign:

   * Scene
   * Spawn points
   * Environment profile
   * Gameplay rules
   * Chunk settings

The framework handles the rest.

---

# 🧬 Mass Generation Support

The Map System is built around generated content pipelines.

Resources can be created through:

* Data generators
* Resource templates
* Database indexing systems

This allows creators to produce large amounts of world content while maintaining consistent structure.

---

# 🔗 System Integration

The Map System integrates with:

* World Launcher
* Level Database
* Chunk Manager
* Chunk Loader
* Environment System
* Audio System
* Save System
* Teleporter System
* UI Systems

---

# 🏆 Framework Benefits

The Map System provides:

✅ Shared map foundation
✅ Data-driven world definitions
✅ Mass generation support
✅ Chunk streaming compatibility
✅ Environment integration
✅ Persistent world settings
✅ Creator-controlled gameplay rules
✅ Global map architecture updates

---

# Summary

The Map System is the backbone of world creation.

By separating map structure from map data, creators can generate and manage hundreds of unique locations while maintaining a consistent framework.

Every map benefits from improvements made to the base architecture, allowing the entire world system to evolve without requiring individual scene rewrites.
