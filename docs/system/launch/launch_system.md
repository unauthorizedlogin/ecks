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

# 🧩 Bootloader

The Bootloader is the first runtime entry point into the Launch System.

Its responsibility is to establish the initial application state and prepare the game for front-end or direct world entry.

The Bootloader handles:

* Initial game state
* Required startup configuration
* Database initialization
* User settings
* Initial scene routing
* Startup transition behavior

The Bootloader can enter the world through different launch paths.

---

## Title Screen Launch

The normal player-facing route.

```text
Bootloader
    ↓
Title Screen
    ↓
New Game / Continue / Other Front-End Flow
    ↓
World Launch
```

---

## Direct World Injection

The boot flow can also support direct world entry for development and testing.

```text
Bootloader
    ↓
World Launch
```

When the world is launched directly through the bootloader, the world controller can receive:

```gdscript
bootloader_setup()
```

This allows world-specific presentation adjustments without making the world controller responsible for bootloader behavior itself.

---

# 🎬 Title Screen Flow

The Title Screen provides the player-facing entry layer between application startup and gameplay.

Its responsibilities include:

* Handling initial player input
* Routing into the appropriate game flow
* Starting new game configuration
* Providing an expandable front-end entry point

The front-end can be expanded with additional flows such as:

* Continue Game
* Save Selection
* Options
* Credits
* Additional menus

The Title Screen does not construct the playable world directly.

Instead, it eventually routes the selected game flow into the Launch System.

---

# 🎮 New Game Initialization

The New Game flow establishes the initial parameters required to begin a game session.

Typical player configuration includes:

* Player name
* Character class
* Difficulty
* Initial map or world configuration

The resulting values become runtime configuration consumed by downstream systems.

Example:

```text
Player Name
Class ID
Difficulty ID
Map ID
        ↓
Game Runtime State
        ↓
World Launch
```

The Launch System does not own the underlying class, difficulty, player, or world databases.

It consumes the resulting configuration and passes control into the appropriate runtime systems.

---

# 🗺️ World Launch Controller

## `launch_03world.gd`

The World Launch Controller is the primary **world-entry orchestrator**.

Its purpose is to assemble the active world scene and establish the initial connections required before handing control to the world runtime.

It is responsible for:

* Resolving the active map
* Loading the map scene
* Generating the active map instance
* Attaching the world to required managers
* Initializing the UI against the active world
* Preparing settings presentation
* Starting world reconstruction
* Providing bootloader-specific visual setup

It does **not** directly own the internal implementation of:

* Player lifecycle
* Camera behavior
* Environment behavior
* Chunk streaming
* World reconstruction
* UI behavior
* Settings behavior

Those responsibilities belong to their respective managers.

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

# 🗺️ Map Resolution

The World Launch Controller determines which map should be instantiated.

The exported fallback map is:

```gdscript
@export var starting_map: PackedScene
```

This provides a default scene when no active map has been selected.

When a runtime map ID exists:

```gdscript
GameManager.current_map_id
```

the controller resolves it through:

```gdscript
MapDatabase.get_map_data()
```

The resulting `MapResource` provides the scene path.

```text
GameManager.current_map_id
        ↓
MapDatabase
        ↓
MapResource
        ↓
scene
        ↓
PackedScene
```

If no runtime map ID exists, the exported:

```gdscript
starting_map
```

is used.

---

# 🔄 Map Resolution Priority

The effective resolution order is:

```text
GameManager.current_map_id
        ↓
MapDatabase
        ↓
MapResource.scene
        ↓
PackedScene
```

Fallback:

```text
starting_map
```

Invalid map IDs, missing scene paths, and failed scene loads are reported through runtime errors.

---

# 🏗️ Map Generation

Map construction is handled by:

```gdscript
generate_map(map_scene)
```

The function is intentionally limited to **map instance construction and world attachment**.

Its responsibilities are:

1. Remove an existing map
2. Validate the supplied scene
3. Instantiate the new map
4. Add it to the world
5. Attach the world to `EnvironmentManager`
6. Connect environment change events

```text
Existing Map
      ↓
queue_free()
      ↓
Instantiate PackedScene
      ↓
Active Map
      ↓
Add to World
      ↓
Attach EnvironmentManager
```

The controller does not perform the environment processing itself.

---

# 🌍 Environment Integration

Environment behavior is owned by:

```gdscript
EnvironmentManager
```

The World Launch Controller establishes the relationship between the active world/map and that manager.

During map generation:

```gdscript
EnvironmentManager.attach_world(self, map)
```

is called.

If the map exposes an environment area:

```gdscript
map.environment_area
```

its environment change signal is connected to:

```gdscript
EnvironmentManager.apply_environment
```

This allows the environment system to respond to map/environment changes without requiring `launch_03world.gd` to contain environment logic.

---

# 🌦️ Environment Ownership

The environment system may control world presentation such as:

* Weather
* Fog
* Clouds
* Leaves
* Rain
* Snow
* Raylight
* Music
* Color correction
* Other environment-driven effects

These are **EnvironmentManager responsibilities**, not World Launch Controller responsibilities.

The Launch System only establishes the connection between the active map and the environment system.

---

# 🎥 Camera Integration

Camera behavior is owned by:

```gdscript
CameraManager
```

During world startup, the controller attaches the newly created world:

```gdscript
CameraManager.attach_world(self)
```

The Launch System therefore establishes the camera's relationship with the active world but does not implement camera behavior itself.

Camera responsibilities can include:

* World camera ownership
* Player camera targeting
* Camera transitions
* Camera positioning
* Camera lifecycle management

The world launch controller simply ensures the camera system knows which world instance is active.

---

# 🖥️ UI Integration

UI behavior is owned by:

```gdscript
UIManager
```

The world controller initializes the UI against the newly created world:

```gdscript
UIManager.init_ui(self)
```

This is performed during world startup so the UI can acquire references associated with the active world instance.

The Launch System does not directly manage individual UI components.

Its responsibility is to ensure the UI system is initialized at the appropriate point in the world launch lifecycle.

---

# ⚙️ Settings Integration

The world controller prepares the settings system for the active world presentation.

It calls:

```gdscript
SettingsManager.cache_brightness_overlay()
SettingsManager.apply_settings()
```

This allows settings to be applied after the world scene has been established.

The Launch System therefore coordinates **when settings are applied**, while `SettingsManager` owns the settings implementation.

---

# 💡 Bootloader Visual Handling

The World Launch Controller provides:

```gdscript
bootloader_setup()
```

This is a specialized entry configuration used when the world is launched directly through the bootloader.

It sets:

```gdscript
is_bootloader_launch = true
```

and hides:

```gdscript
brightness_overlay
```

This prevents the bootloader's visual treatment from being duplicated when entering the world.

The function exists specifically as a bridge between the bootloader launch path and world presentation.

---

# 🌍 World Reconstruction

After the map and world dependencies have been established, the controller delegates world initialization to:

```gdscript
WorldManager.rebuild_world(self)
```

This is a significant architectural boundary.

`launch_03world.gd` does **not** perform the complete player/world reconstruction itself.

Instead:

```text
World Launch Controller
        ↓
WorldManager
        ↓
World Reconstruction
```

The World Manager owns the runtime world lifecycle.

---

# 🔄 World Rebuild Pipeline

World reconstruction is responsible for rebuilding the active gameplay state after the world scene has been established.

It can be used for scenarios such as:

* Initial world entry
* Save restoration
* Map transitions
* Teleports
* Runtime world switching
* Player repositioning
* Reinitialization after world changes

The Launch Controller initiates this process but does not own its internal sequence.

---

# 🧑 Player Lifecycle

Player lifecycle responsibilities have been relocated out of the World Launch Controller.

The Launch System does not directly create, initialize, position, or bootstrap the player.

Instead, those responsibilities belong to:

```gdscript
PlayerManager
```

as part of the world reconstruction lifecycle.

Conceptually:

```text
World Launch
      ↓
WorldManager.rebuild_world()
      ↓
PlayerManager
      ↓
Player Ready
```

This keeps player lifecycle logic independent from world scene construction.

---

# 🧩 Chunk Streaming

Chunk streaming is also no longer directly implemented by the World Launch Controller.

The Launch System does not manually configure or load individual chunks.

Chunk lifecycle belongs to:

```gdscript
ChunkManager
```

The World Manager coordinates when the chunk system needs to participate in world reconstruction.

Conceptually:

```text
World Launch
      ↓
WorldManager
      ↓
ChunkManager
      ↓
Chunk Streaming
```

This allows chunk behavior to remain independent from the launch scene.

---

# 🧠 World Manager Integration

The primary runtime handoff is:

```gdscript
await WorldManager.rebuild_world(self)
```

This establishes the World Manager as the owner of world reconstruction.

The Launch Controller's responsibility ends at preparing the world and invoking the reconstruction pipeline.

The separation is:

| Responsibility            | Owner                   |
| ------------------------- | ----------------------- |
| Determine launch route    | Launch System           |
| Resolve active map        | World Launch Controller |
| Instantiate map           | World Launch Controller |
| Attach world dependencies | World Launch Controller |
| Rebuild world state       | WorldManager            |
| Player lifecycle          | PlayerManager           |
| Camera lifecycle          | CameraManager           |
| Environment behavior      | EnvironmentManager      |
| Chunk streaming           | ChunkManager            |
| UI lifecycle              | UIManager               |
| Settings application      | SettingsManager         |
| Global game state         | GameManager             |

---

# 🔀 Map Switching

The World Launch Controller supports map replacement through:

```gdscript
generate_map()
```

An existing map is removed before the new map is instantiated.

```text
Current Map
    ↓
queue_free()
    ↓
New PackedScene
    ↓
New Map Instance
```

The actual runtime world reconstruction following a map switch belongs to:

```gdscript
WorldManager
```

This prevents map construction and world-state reconstruction from becoming the same responsibility.

---

# 🏁 World Boot Sequence

The current `_ready()` pipeline is intentionally straightforward:

```text
World Scene Enters Tree
        ↓
Resolve Active Map
        ↓
Generate Map
        ↓
Attach Camera World
        ↓
Initialize UI
        ↓
Prepare Settings
        ↓
Apply Settings
        ↓
WorldManager.rebuild_world()
        ↓
World Boot Complete
```

The controller then reports:

```text
✅ World boot complete
```

after the reconstruction pipeline has completed.

---

# 🗺️ Active Map Access

The active map is stored as:

```gdscript
var map: Map
```

and exposed through:

```gdscript
func get_map() -> Map:
    return map
```

This provides other systems with a controlled way to retrieve the currently instantiated map.

The Launch Controller therefore serves as the world-level access point for the active map instance.

---

# 🔁 Runtime World Switching

The controller contains world-switching state:

```gdscript
var world_switching := false
var active_map_id := 0
```

These variables provide world-level switching state for the launch/world lifecycle and can support future expansion of runtime map transitions.

The actual state transition and reconstruction responsibilities remain with the appropriate world management systems.

---

# 🛡️ Runtime Validation

The World Launch Controller validates critical launch conditions before continuing.

Validation includes:

### Map ID

Invalid map IDs produce an error.

### Map Resource

Missing map data prevents successful map resolution.

### Scene Path

An empty scene path is rejected.

### PackedScene

Failed scene loads are reported.

### Map Generation

A null scene passed into `generate_map()` is rejected.

These checks ensure the launch pipeline fails visibly when required world data is invalid.

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
