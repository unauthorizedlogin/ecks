# 🌍 World Manager

## Overview

The **World Manager** is the central authority for world state and level transitions within the framework.

It manages the loading, changing, and restoration of game environments while maintaining the current world context.

The World Manager owns **where the player exists**, while the Game Manager owns **what state the game is in**.

This includes:

* 🗺️ Level loading
* 🚪 Level transitions
* 🌍 World state management
* 📍 Spawn point handling
* 📦 World restoration
* 🧩 Chunk state restoration
* 💾 World serialization
* 🔄 Environment refresh

---

# 🧠 Core Responsibilities

The World Manager provides:

* World lifecycle management
* Level resolution
* Scene loading
* Active world replacement
* World state tracking
* Spawn coordination
* Save restoration support

The World Manager controls the world container, not gameplay systems within that world.

---

# ⚙️ Initialization

```gdscript
func initialize()
```

Registers the World Manager as an active framework system.

Prevents duplicate initialization.

Output:

```text
🌍 WorldManager initialized
```

---

# 🗺️ World State

The World Manager maintains the current world context.

Tracked information includes:

* Current level index
* Current level identifier
* Level data reference
* Pending spawn point
* Pending spawn position
* Current save world data

This state represents the loaded environment rather than the overall game session.

---

# 🔄 Level Loading Pipeline

The World Manager handles full world creation when loading a new level.

```text
Level Request
       ↓
Resolve Level Data
       ↓
Set Loading State
       ↓
Unload Current World
       ↓
Instantiate New World
       ↓
Restore World State
       ↓
Bootstrap Player
       ↓
Activate Gameplay
```

---

# 📚 Level Resolution

Levels are resolved through the framework's level data system.

Resolution priority:

```text
LevelData
     ↓
Level Entry
     ↓
Packed Scene
```

If no matching level entry exists, the manager falls back to the default world scene.

This allows development environments and incomplete data sets to remain functional.

---

# 🌍 World Scene Management

When loading a new world, the manager handles:

* Current scene cleanup
* New scene instantiation
* Scene tree registration
* World activation

The previous world is removed before the new world becomes active.

---

# 🧩 World Stabilization

After creating a new world, the manager allows the scene to complete initialization before continuing.

This provides time for:

* Map generation
* Node initialization
* Environment setup
* World dependencies

before gameplay systems are activated.

---

# 🧍 Player Integration

The World Manager coordinates with the Player Manager after the world is ready.

Flow:

```text
World Loaded
      ↓
PlayerManager
      ↓
Bootstrap Player
      ↓
Gameplay Ready
```

The World Manager does not create or configure the player.

It only ensures the player lifecycle begins after the correct world exists.

---

# 🚪 Level Transition System

Level transitions occur without replacing the entire game session.

The manager supports changing levels while maintaining:

* Player session
* World state
* Gameplay systems
* Save context

Transition flow:

```text
Level Change Request
          ↓
Validate Level ID
          ↓
Resolve Scene
          ↓
Reset World Systems
          ↓
Generate New Map
          ↓
Rebuild World
          ↓
Restore Gameplay
```

---

# 🔒 Level Validation

Before transitioning, the manager validates:

* Level identifier exists
* Identifier is valid
* Scene path resolves correctly
* Active world supports rebuilding

Invalid transitions are rejected before affecting the current world.

---

# 🧩 Chunk Integration

The World Manager coordinates with the Chunk Manager during world transitions.

Before rebuilding a new world:

* Existing chunk state is reset
* New chunk state is initialized
* Saved chunk data is restored when available

This allows procedural or streamed environments to transition safely.

---

# 📦 Save Restoration

When loading a saved world, the manager restores stored world state.

Supported restoration includes:

* Current level
* Current level identifier
* Chunk state

World restoration occurs before gameplay resumes.

---

# 🌱 Environment Refresh

After loading a world, environment systems may be refreshed.

Examples:

* Weather
* Lighting
* Environmental effects
* Map presentation

This allows world-specific systems to update after a transition.

---

# 💾 World Serialization

The World Manager provides world state persistence.

Currently serialized data includes:

```text
Current Level
Current Level ID
```

Additional world systems can expand this data.

Future support includes:

* World modifiers
* Persistent objects
* Environmental states
* Region data

---

# 🔗 System Relationships

```text
                 Game Manager
                      |
                      ↓
               World Manager
                      |
        ┌─────────────┼─────────────┐
        ↓             ↓             ↓
 Level Database   Chunk Manager  Save Manager
        |             |             |
        ↓             ↓             ↓
   Level Scene   World State   Restore Data
                      |
                      ↓
                Player Manager
                      |
                      ↓
                 Active Player
```

---

# 🧭 System Boundaries

The World Manager coordinates world state but delegates specialized responsibilities.

| Responsibility    | System         |
| ----------------- | -------------- |
| Global game state | Game Manager   |
| Player lifecycle  | Player Manager |
| Saving            | Save Manager   |
| Chunk streaming   | Chunk Manager  |
| Level definitions | Level Database |
| UI transitions    | UI Manager     |
| Combat            | Combat Manager |

This keeps world management focused on environments and transitions rather than gameplay logic.

---

# ✅ Design Rule

**WorldManager is the single authority for world state and level transitions.**

No gameplay system should directly load levels, replace world scenes, or manage world restoration.

All environment changes should flow through the World Manager, ensuring consistent transitions, save restoration, and predictable world lifecycle management.
