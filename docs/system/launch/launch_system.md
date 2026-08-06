# 🚀 Launch System

The Launch System provides the game's complete startup and entry pipeline, transitioning the application from initial boot through front-end configuration and into a fully initialized playable world.

The system is organized as a **multi-stage launch pipeline**:

```text
Application Boot
      ↓
Bootloader
      ↓
Title Screen
      ↓
New Game Configuration
      ↓
World Launch
      ↓
World Reconstruction
      ↓
Playable Runtime
```

The architecture separates **launch orchestration** from the specialized systems responsible for world state, player lifecycle, camera control, environment presentation, UI, settings, and chunk streaming.

The launch layer determines **what needs to happen and in what order**, while dedicated managers perform the actual system-specific work.

---

# ⚙️ Architecture Overview

The Launch System is composed of several stages.

### Bootloader

Responsible for initial application startup and global boot configuration.

### Title Screen

Responsible for player-facing entry navigation.

### New Game Initialization

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
├── New Game
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

# 🧭 World Launch Responsibilities

The world launch sequence is now intentionally thin.

```text
Resolve Map
     ↓
Generate Map
     ↓
Attach World Managers
     ↓
Initialize UI
     ↓
Apply Settings
     ↓
WorldManager.rebuild_world()
     ↓
World Ready
```

The controller acts as the **orchestration boundary** between the global launch flow and the active world instance.

---

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

# 🔗 System Integration

The Launch System currently integrates with:

* `GameManager`
* `MapDatabase`
* `MapResource`
* `WorldManager`
* `PlayerManager`
* `CameraManager`
* `EnvironmentManager`
* `ChunkManager`
* `UIManager`
* `SettingsManager`
* Bootloader
* Title Screen
* New Game Flow

The primary world-launch integration point is:

```gdscript
await WorldManager.rebuild_world(self)
```

This provides the boundary between **world scene construction** and **runtime world initialization**.

---

# 🏗️ Current Architecture

The resulting architecture is:

```text
                    LAUNCH SYSTEM
                         │
             ┌───────────┴───────────┐
             │                       │
         Front End              World Entry
             │                       │
     ┌───────┴───────┐       ┌───────┴────────┐
     │               │       │                │
 Bootloader      Title/New   Map Resolution   │
                                │             │
                                ↓             │
                           Map Generation     │
                                │             │
                    ┌───────────┼─────────────┤
                    ↓           ↓             ↓
              CameraManager  UIManager  SettingsManager
                    │
                    └───────────┬─────────────┘
                                ↓
                       WorldManager
                                │
                  ┌─────────────┼─────────────┐
                  ↓             ↓             ↓
            PlayerManager  EnvironmentManager ChunkManager
```

The important architectural distinction is that the Launch System **coordinates these systems without becoming the owner of them**.

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
