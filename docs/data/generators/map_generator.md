## 🗺️ Map Generator System

The Map Generator System provides a data-driven pipeline for creating all playable maps and world locations.

Maps are authored through CSV data and automatically converted into optimized `.tres` resources, allowing the world to be managed entirely through data rather than manual resource creation.

The system is responsible for:

* map resource generation
* world and region organization
* scene assignment
* spawn point configuration
* chunk profile assignment
* gameplay rule configuration
* environment settings
* persistence settings
* presentation metadata
* generated map indexing

See:

- Chunk Streaming System
- Weather System
- Audio System

---

### 🧱 Map Resource Architecture

Each generated map is currently represented by a `LevelResource`. <- Soon to be `MapResource`

Map resources contain:

* map identity
* world hierarchy
* scene references
* chunk configuration
* gameplay rules
* environmental settings
* persistence options
* presentation data

Maps are fully data-driven and do not require manual resource creation.

---

### 🌍 World Organization

Maps can be organized into worlds, regions, biomes, categories, and optional subtypes.

Example:

```text
data/databases/levels/
├── overworld/
│   ├── grasslands/
│   ├── forest/
│   └── desert/
│
├── dungeons/
│   ├── caves/
│   └── ruins/
│
└── towns/
```

This organization scales naturally as the world grows while keeping resources easy to maintain.

---

### 🧩 Chunk Configuration

Each map references a chunk profile that defines how the world is streamed.

Map resources include configuration such as:

* chunk profile
* chunk dimensions
* load radius
* world bounds
* chunk templates
* LOD distances

The Map Generator stores these settings, while the Chunk Streaming System manages runtime loading and unloading.

---

### 🌦️ Environment Settings

Maps define environmental metadata including:

* background music
* ambient audio
* weather profile
* biome

These references are consumed by their respective runtime systems.

---

### 🎮 Gameplay Rules

Each map can configure gameplay behavior independently.

Supported options include:

* combat availability
* mount support
* fast travel availability
* enemy persistence
* loot persistence

This allows individual maps to enforce unique gameplay rules without additional scripting.

---

### 🖥️ Presentation

Map resources also contain presentation metadata for UI and debugging.

This includes:

* display type
* minimap availability
* debug colors
* save identifiers
* version metadata

---

### ⚙️ Generation Pipeline

The generation process:

1. Reads map CSV definitions
2. Validates map data
3. Creates map resources
4. Assigns world and gameplay metadata
5. Configures chunk settings
6. Saves `.tres` resources
7. Rebuilds the map index

---

### 🔎 Generated Map Index

The Map Index Generator scans generated map resources and builds a runtime lookup index.

The runtime database provides:

* fast map loading
* deterministic resource discovery
* filesystem-independent lookups
* scalable world organization

The index is automatically rebuilt whenever map generation runs.

---

### ✅ System Responsibilities

The Map Generator System:

✅ Creates map resources  
✅ Handles CSV → Resource conversion  
✅ Organizes world data  
✅ Stores chunk configuration  
✅ Stores gameplay rules  
✅ Builds runtime indexes

The Map Generator System does **not**:

❌ Stream chunks  
❌ Load or unload maps  
❌ Control weather  
❌ Play music or ambient audio  
❌ Handle save/load logic

Those responsibilities belong to:

- Chunk Streaming System
- Weather System
- Audio System
- Save System
````
