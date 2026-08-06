# 🚀 Launch System

The Launch System provides the framework's application startup and game-entry pipeline.

It manages the progression from initial application boot through front-end navigation and into the active game world.

# 📦 System Responsibilities

The Launch System follows a simple architectural rule:

> **Launch decides what happens next. Managers own how their systems operate.**

### Launch System Owns

* Startup routing
* World entry orchestration
* Active map resolution
* Map scene instantiation
* Manager attachment
* Launch-order coordination
* World boot completion

### Launch System Does Not Own

* Player lifecycle
* Camera implementation
* Environment processing
* Chunk streaming implementation
* UI implementation
* Settings implementation
* World reconstruction internals
* Global game state

This keeps the launch layer lightweight and prevents it from becoming a central dependency for unrelated runtime behavior.

---

# ⚙️ Architecture Overview

The Launch System is composed of several stages.

### Bootloader

Responsible for initial application startup and global boot configuration.

### Title Screen

Responsible for player-facing entry navigation.

### Start Menu

Responsible for collecting and establishing initial game configuration.

### World Launch Controller

`launch_03world.gd`

Responsible for assembling the active world scene and orchestrating the transition into the world runtime.

### World Manager

Responsible for rebuilding and transitioning world state after the world scene has been instantiated.

### Specialized Managers

Dedicated managers own the individual runtime systems used during world initialization.

```text
Launch System
│
├── Bootloader
│   └── Initial application boot
│
├── Title Screen
│   └── Front-end routing
│
├── Start Menu
│   └── Initial session configuration
│
└── World Launch
    ├── Map Resolution
    ├── Map Generation
    ├── Manager Attachment
    ├── UI Initialization
    ├── Settings Application
    └── WorldManager.rebuild_world()
            │
            ├── PlayerManager
            ├── CameraManager
            ├── EnvironmentManager
            ├── ChunkManager
            └── Other World Runtime Systems
```

The launch stages are responsible for **orchestrating entry into the game**, while specialized runtime managers own the systems that operate after launch.

`03launch_world.gd` acts as the final launch-stage orchestrator, preparing the active map and handing world initialization to `WorldManager`.

---

# 🔗 Launch System Documentation

The following stages make up the Launch System.

| Stage            | Purpose                                                                                                            | Documentation                     |
| ---------------- | ------------------------------------------------------------------------------------------------------------------ | --------------------------------- |
| 🧩 Bootloader    | Initial application boot, startup configuration, database initialization, settings, and initial scene routing      | [Bootloader](00bootloader.md)     |
| 🎬 Title Screen  | Player-facing entry point and front-end launch routing                                                             | [Title Screen](01titlescreen.md)  |
| 🎮 Start Menu    | New game configuration and initial gameplay parameters                                                             | [Start Menu](02startmenu.md)      |
| 🗺️ World Launch | Active map resolution, world scene construction, manager attachment, and handoff into runtime world reconstruction | [World Launch](03launch_world.md) |

---

# 🔗 Runtime Integration

The Launch System connects the launch pipeline to the framework's runtime systems.

Key integrations include:

* `GameManager` — Runtime game state and selected session data
* `MapDatabase` — Active map resolution
* `WorldManager` — World reconstruction and transition lifecycle
* `PlayerManager` — Player lifecycle during world initialization
* `CameraManager` — World camera integration
* `EnvironmentManager` — World environment integration
* `ChunkManager` — World streaming integration
* `UIManager` — Runtime UI initialization
* `SettingsManager` — Launch-time settings application

The Launch System coordinates these systems without taking ownership of their internal responsibilities.

---

# 📌 Summary

The Launch System provides the centralized entry architecture for the game.

It separates application boot, front-end navigation, new game configuration, and world construction into discrete stages, with `03launch_world.gd` serving as the final orchestrator before control transitions into the runtime world systems.





---



---

# ⚠️ Current Development Status

The Launch System is functional infrastructure and now operates as a dedicated orchestration layer.

The world launch controller no longer directly owns the individual responsibilities of camera management, environment management, player lifecycle, chunk streaming, or world reconstruction.

Instead, it prepares the world and delegates those responsibilities to their appropriate managers.

This establishes a cleaner separation between:

* **Launch orchestration**
* **World state management**
* **Runtime system ownership**

The launch pipeline can therefore continue expanding without turning the world launch script into a monolithic runtime controller.

---

# 🚀 Future Expansion

The Launch System can be extended with additional orchestration stages without moving system-specific implementation back into the launch controller.

Potential extensions include:

* Save-game entry routing
* Continue-game flow
* Loading screen integration
* Additional startup validation
* World transition effects
* Multiplayer session initialization
* Mod/content initialization
* Runtime world streaming transitions
* Additional launch/testing modes

These should remain orchestration concerns while specialized runtime behavior continues to reside within dedicated managers.

---

# 📌 Summary

The Launch System provides the centralized entry architecture responsible for moving the game from application startup into a playable world.

Its current architecture separates **orchestration from implementation**.

The World Launch Controller:

* Resolves the active map
* Instantiates the map
* Connects the world to required managers
* Initializes UI and settings
* Starts the world reconstruction process
* Handles bootloader-specific world presentation

Specialized managers then own their respective runtime responsibilities:

```text
Launch
  ↓
World Construction
  ↓
Manager Handoff
  ↓
WorldManager
  ├── PlayerManager
  ├── CameraManager
  ├── EnvironmentManager
  ├── ChunkManager
  └── Other Runtime Systems
```

This makes `launch_03world.gd` a **proper world orchestrator rather than a monolithic world controller**, giving the Launch System a clear architectural boundary while preserving a single, predictable entry point into the playable runtime.This version keeps the **full scope of the old README**, but the ownership is now much cleaner: `launch_03world.gd` builds the stage and coordinates the handoff; it doesn't pretend to own every system that happens to participate in world startup.
