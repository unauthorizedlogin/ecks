# 🚀 Launch System

The Launch System provides the framework's application startup and game-entry pipeline.

It manages the progression from initial application boot through front-end navigation and into the active game world.

The Launch System is responsible for:

* Application boot and initialization
* Front-end launch routing
* New game configuration
* World scene construction
* Active map resolution
* Launch-time system coordination
* Transition from launch flow into runtime world management

The system is intentionally divided into discrete stages, allowing each stage to own its specific responsibilities while the overall Launch System provides the entry architecture.

---

# 🧠 Launch Architecture

```text
                    Launch System

                          |
        -----------------------------------------
        |           |           |               |
    Bootloader  Title Screen  Start Menu   World Launch
        |           |           |               |
        ↓           ↓           ↓               ↓
   Application   Front-End   Game Setup    World Construction
    Startup       Routing                  & Manager Handoff
                                                |
                                                ↓
                                         WorldManager
                                                |
                           ---------------------|---------------------
                           |          |          |          |
                           ↓          ↓          ↓          ↓
                      PlayerManager Camera   Environment  Chunk
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
