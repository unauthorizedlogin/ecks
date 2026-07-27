# 🌎 World Systems

## Overview

The World Systems layer provides the framework foundation for managing world structure, loading, environmental presentation, interactive objects, and player navigation.

The system uses a data-driven architecture separating:

- World and map definitions
- Runtime streaming
- Interactive objects
- Environmental systems
- Player transportation

World systems manage world state while remaining independent from gameplay systems such as quests, combat, and progression.

---

# 🧠 System Architecture

```text
                         Map Resource
                              |
                              ↓
                         Map Scene
                              |
                              ↓
                     World Management Layer
                              |
        ┌──────────────┬──────────────┬──────────────┐
        ↓              ↓              ↓              ↓

     Chunks       Environment     Destroyables   Audio

        |
        ↓

 Chunk Manager
        |
        ↓

 Chunk Loader
````

---

# 🔗 World System Documentation

The following systems make up the World layer.

| System | Purpose | Documentation |
|---|---|---|
| 🔊 Audio System | Dynamic music, sound effects, ambience, and world audio zones | [Audio System](./docs/system/world/audio_system.md) |
| 🧩 Chunk Loader | Connects maps to runtime chunk streaming configuration | [Chunk Loader](./docs/system/world/chunk_loader.md) |
| 🗺️ Chunk Manager | Handles runtime world streaming and chunk lifecycle management | [Chunk Manager](./docs/system/world/chunk_manager.md) |
| 💥 Destroyables | Interactive world objects with damage, destruction, and persistence support | [Destroyables](./docs/system/world/destroyables.md) |
| 🌦️ Environment System | Data-driven weather, atmosphere, lighting, and world environments | [Environment System](./docs/system/world/environment_system.md) |
| 🗺️ Map System | Defines playable world areas, metadata, and map configuration | [Maps](./docs/system/world/maps.md) |

---

# Summary

The World Systems layer provides a scalable foundation for building and managing game environments through modular, data-driven systems.

Each subsystem specializes in a specific aspect of world management while integrating through shared resources, managers, and runtime pipelines.
