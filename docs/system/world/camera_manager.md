# 📷 Camera Manager

## Overview

The **Camera Manager** is the central authority for attaching and coordinating the active gameplay camera within the framework.

It provides a framework-level camera interface between the **Launch Flow**, the active world, and the player while keeping the camera implementation itself separate from gameplay systems.

The current implementation is designed around the framework's 2D RPG camera system, but the manager establishes a modular attachment layer intended to support additional camera implementations in the future.

This includes:

* 🎥 Active camera management
* 🌍 World camera attachment
* 🧍 Player camera attachment
* 🎯 Camera target assignment
* 🧭 Camera pivot synchronization
* 🔄 Camera reset and reattachment
* 🧩 Modular camera expansion

---

# 🧠 Core Responsibilities

The Camera Manager provides:

* Camera lifecycle initialization
* Active camera discovery
* World camera attachment
* Player targeting
* Camera activation
* Pivot synchronization
* Camera reference management
* Camera reset

The manager coordinates camera ownership but does not implement camera movement, zoom behavior, camera effects, or gameplay logic.

Those responsibilities belong to the active camera implementation.

---

# ⚙️ Initialization

```gdscript
func initialize()
```

Registers the Camera Manager as an active framework system.

Prevents duplicate initialization.

Output:

```text
📷 CameraManager initialized
```

---

# 🔎 Camera Availability

```gdscript
func has_camera()
```

Determines whether a valid camera implementation is currently attached.

This allows the Launch Flow and other systems to determine whether camera functionality is available without depending on a specific camera class.

---

# 🌍 World Attachment

```gdscript
func attach_world(world)
```

Attaches the active camera system to a loaded world.

The current 2D implementation expects the world to provide:

* `CameraGrid`
* `Pivot`

The manager:

1. Retrieves the camera implementation.
2. Retrieves the world pivot.
3. Activates the camera.
4. Connects camera lifecycle events.

```text
World
 ├── CameraGrid
 └── Pivot
        ↓
 CameraManager
```

This establishes the camera context before the player is attached.

---

# 🧍 Player Attachment

```gdscript
func attach_player(player)
```

Associates the active player with the currently attached camera.

The current camera implementation receives the player as its target and immediately synchronizes its position with the player.

```text
Player
   ↓
CameraManager
   ↓
CameraGrid
   ↓
Active Camera
```

The manager validates both the camera and player before attempting attachment.

---

# 🎯 Camera Targeting

Camera targeting is delegated to the active camera implementation.

The Camera Manager does not calculate:

* Follow speed
* Camera distance
* Camera smoothing
* Camera bounds
* Zoom
* Rotation
* Tracking behavior

Instead, it supplies the appropriate gameplay target to the camera.

This separation allows different camera implementations to define their own tracking behavior.

---

# 🧭 Pivot Synchronization

The current 2D camera implementation uses a world pivot alongside the camera.

When camera animation completes, the Camera Manager synchronizes the world pivot with the camera's final position.

```text
Camera Animation
       ↓
Animation Finished
       ↓
CameraManager
       ↓
World Pivot Updated
```

This keeps the world pivot aligned with the camera after camera movement or transitions.

---

# 📡 Camera Lifecycle Events

The manager listens for completion events from the active camera implementation.

Currently this is used to synchronize the world pivot after camera animation completes.

The manager validates the camera and pivot references before applying the update, preventing operations against freed world nodes.

---

# 🧩 Modular Camera Architecture

The Camera Manager intentionally separates **camera ownership** from **camera implementation**.

Current architecture:

```text
              Launch Flow
                   |
                   ↓
            CameraManager
                   |
                   ↓
          Current Camera
                   |
          ┌────────┴────────┐
          ↓                 ↓
       Player             World
       Target             Pivot
```

The current implementation uses a 2D `CameraGrid`, but the manager is designed to evolve toward a modular camera interface.

Future camera implementations may support different gameplay formats without requiring the Launch Flow to understand their internal behavior.

---

# 🌎 Future Camera Expansion

The long-term camera architecture is intended to allow different camera implementations to be supplied during the launch process.

Potential implementations include:

```text
CameraManager
      |
      ├── 2D RPG Camera
      ├── 2D Platformer Camera
      ├── 3D Third-Person Camera
      ├── 3D Top-Down Camera
      └── Other Camera Modules
```

This allows the framework to move beyond a camera architecture tied specifically to a 2D RPG.

The Launch Flow can eventually determine which camera implementation belongs to the active game or world and provide it to the Camera Manager.

---

# 🚀 Launch Flow Integration

The Camera Manager is initialized as part of the framework's centralized Launch Flow.

Its position within the launch architecture allows camera setup to become an infrastructure concern rather than something each individual world or player scene must implement independently.

```text
Launch Flow
    ↓
Initialize Systems
    ↓
Load World
    ↓
Attach Camera
    ↓
Attach Player
    ↓
Gameplay Ready
```

This creates a predictable camera lifecycle across different game configurations.

---

# 🔄 Reset

```gdscript
func reset()
```

Clears the active camera and world pivot references.

Used when the current world or camera context is being replaced.

This allows the manager to safely accept a new camera during a subsequent world or gameplay initialization.

---

# 🔗 System Relationships

```text
                  Launch Flow
                       |
                       ↓
                CameraManager
                       |
              ┌────────┴────────┐
              ↓                 ↓
          WorldManager      PlayerManager
              |                 |
              ↓                 ↓
            World            Player
              |                 |
              └───────┬─────────┘
                      ↓
                Camera Module
                      |
                      ↓
                 Active View
```

---

# 🧭 System Boundaries

The Camera Manager coordinates camera attachment but delegates specialized responsibilities.

| Responsibility      | System                |
| ------------------- | --------------------- |
| Launch sequencing   | Launch Flow           |
| World lifecycle     | World Manager         |
| Player lifecycle    | Player Manager        |
| Camera movement     | Camera implementation |
| Camera animation    | Camera implementation |
| Camera targeting    | Active camera         |
| Camera presentation | Camera implementation |

This keeps the Camera Manager independent from the behavior of any particular camera type.

---

# ✅ Design Rule

**CameraManager is the central authority for active camera attachment and lifecycle coordination.**

The manager should not contain camera-specific movement or presentation logic.

Camera implementations should provide their own behavior while the Camera Manager provides a consistent framework-level interface for connecting cameras to worlds, players, and the Launch Flow.

**The current implementation is 2D RPG-oriented, but the architecture is intentionally being established as a modular camera layer so the framework can support multiple game formats and camera models in the future.**

