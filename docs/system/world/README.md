# 🌎 World Systems README

## Overview

The World Systems framework provides the foundation for managing world loading, map structure, environmental presentation, interactive objects, and player navigation.

The system is designed around a **data-driven world architecture**, separating:

- Map data definitions
- Chunk loading and unloading
- Runtime world streaming
- Interactive world objects
- Environmental systems
- Player transportation

World systems are responsible for managing the game world state while remaining independent from gameplay systems such as quests, combat, and progression.

---

# 🧠 System Architecture

The World Systems are organized into independent systems working together:

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

     Chunks       Environment     Destroyables   Teleporters
        |
        ↓
 Chunk Manager
        |
        ↓
 Chunk Loader
```

---

# 🗺️ Map System

The Map System provides the foundation for all playable world areas through reusable map scenes and data-driven map resources.

Responsibilities include:

- Base map architecture
- World metadata
- Environment integration
- Chunk streaming support
- Map-specific gameplay rules
- Persistence configuration

Documentation:

📄 [Map System Documentation](world/maps.md)

---

# 🌍 Chunk Manager

The Chunk Manager provides the runtime world streaming foundation for large-scale environments.

It manages dynamic chunk loading, unloading, world isolation, LOD support, chunk persistence, and player spawn placement.

The system allows maps to be divided into manageable world sections while maintaining a seamless exploration experience.

Responsibilities include:

- Dynamic chunk streaming
- Grid-based world management
- Asynchronous chunk loading
- Chunk unloading
- LOD selection support
- Multi-world isolation
- Persistent world state handling
- Teleport spawn integration

Documentation:

📄 [Chunk Manager Documentation](world/chunk_manager.md)

---

# 🧱 Chunk Loader

The Chunk Loader provides the connection layer between active maps and the runtime chunk streaming system.

It initializes world streaming, provides chunk configuration data, and delegates loading behavior to the Chunk Manager.

The system is designed around data-driven world configuration, allowing creators to define chunk templates, boundaries, loading rules, and LOD settings per world.

Responsibilities include:

- Initializing chunk streaming
- Connecting maps to the Chunk Manager
- Providing chunk configuration
- Managing chunk loading updates
- Supporting LOD-based streaming
- Validating world streaming setup

Documentation:

📄 [Chunk Loader Documentation](world/chunk_loader.md)

---

## 💥 Destroyables

The **Destroyable System** provides a reusable framework for creating interactive world objects that can receive damage, react to hits, and be removed from the environment.

Destroyables are modular gameplay objects built from reusable components, allowing creators to quickly create breakable props, destructible scenery, environmental hazards, and interactive objects without requiring custom scripts for each object.

The system separates health management, damage handling, hit detection, and visual feedback into independent layers, allowing destroyable objects to integrate directly with the framework's combat and world systems.

Supports:

- Breakable objects
- Damage reception
- Health-based destruction
- Knockback reactions
- Hit feedback
- Particle effects
- Combat integration
- World persistence support

Documentation:

📄 [Destroyables Documentation](world/destroyables.md)

---


