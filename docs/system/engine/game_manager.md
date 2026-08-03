# 🧠 Game Manager — Global Game Flow Authority

## Overview

The **Game Manager** is the central authority for global game flow and session state within the framework.

It owns the current game state, coordinates high-level game transitions, manages session information, and serves as the bridge between the menu flow and active gameplay.

The Game Manager does **not** manage world generation, player lifecycle, combat, or save serialization. Those responsibilities belong to their dedicated systems.

This includes:

* 🎮 Global game state
* 🚀 New game initialization
* 🗺️ Scene transitions
* ⏸️ Pause and resume flow
* ☠️ Game over handling
* 🎬 Credits flow
* ⏱️ Session tracking
* 🧪 Debug session controls

---

# 🧠 Core Responsibilities

The Game Manager provides:

* Global state management
* Session initialization
* Scene transition coordination
* Pause and resume control
* Session statistics
* Gameplay timing
* High-level game flow events

The Game Manager represents the current state of the game—not the state of the player or world.

---

# ⚙️ Initialization

```gdscript
func initialize()
```

Initializes the internal transition timer used during scene changes.

Registers the Game Manager as an active gameplay system.

Output:

```text
🧠 GameManager initialized
```

---

# 🎮 Game State System

The manager maintains the current global game state.

Supported states include:

* MENU
* PLAYING
* PAUSED
* CUTSCENE
* LOADING

Every state transition records both the previous and current state, allowing systems to react appropriately when gameplay changes.

---

# 🔄 State Transition Pipeline

All state changes follow a centralized flow.

```text
Requested State
        ↓
Validate Change
        ↓
Update Previous State
        ↓
Update Current State
        ↓
Broadcast State Event
```

This ensures every system receives consistent state notifications.

---

# 🚀 New Game Flow

Starting a new game initializes a fresh gameplay session.

Session initialization includes:

* Player identity
* Selected class
* Difficulty
* Session statistics
* Playtime
* Death counter

Once the session has been prepared, control is handed to the World Manager to load the starting level.

```text
Start New Game
        ↓
Initialize Session
        ↓
Set Loading State
        ↓
Transition
        ↓
WorldManager
        ↓
Load Starting Level
```

---

# 🗺️ Scene Transition System

The Game Manager coordinates high-level scene transitions.

Responsibilities include:

* Preventing duplicate transitions
* Transition timing
* Scene change requests
* Transition events
* Fade synchronization

Scene loading is intentionally buffered through an internal timer to allow transition effects to complete before changing scenes.

---

# ⏸️ Pause System

Gameplay may be paused only while actively playing.

When paused, the manager:

* Changes the game state
* Pauses the SceneTree
* Opens the pause interface

Resuming gameplay restores the previous gameplay flow and closes the pause interface.

---

# ☠️ Game Flow Events

The manager coordinates major gameplay transitions beyond normal play.

Supported flows include:

* Return to menu
* Game over
* Credits

These flows are responsible for coordinating state changes and transition effects before handing control to the appropriate UI or scene.

---

# 📊 Session Tracking

The Game Manager owns runtime session information.

Tracked session data includes:

* Current level
* Current level identifier
* Selected class
* Difficulty
* Player name
* Total playtime
* Death count

This information represents the current play session rather than persistent character progression.

---

# ⏱️ Playtime Tracking

Playtime is accumulated only while the game is actively being played.

States such as:

* Menu
* Pause
* Loading
* Cutscenes

do not contribute toward gameplay time.

---

# 💀 Death Tracking

The Game Manager records player deaths during the active session.

Each registered death:

* Increments the session counter
* Broadcasts a gameplay event through the Event Manager

This provides a centralized session statistic without coupling death tracking to combat systems.

---

# 🔔 System Events

The manager broadcasts lifecycle events whenever major game flow changes occur.

Supported events include:

* Game state changed
* Scene loading
* Scene loaded

These signals allow UI, audio, world systems, and other managers to react without direct dependencies.

---

# 🧪 Debug Controls

Optional debug functionality allows controlled manipulation of the current session.

Supported operations include:

* Enable debug mode
* Skip levels
* Force game states

These tools are isolated behind the debug flag to prevent accidental use during normal gameplay.

---

# 🔗 System Relationships

```text
              Launch Flow
                   |
                   ↓
             Game Manager
                   |
      ┌────────────┼────────────┐
      ↓            ↓            ↓
Transition     WorldManager   UIManager
 Manager            |             |
      ↓             |             |
 Scene Changes      |        Pause/Game Over
                    |
                    ↓
              Active Gameplay
                    |
                    ↓
              Session Tracking
```

---

# 🧭 System Boundaries

The Game Manager intentionally delegates specialized responsibilities to dedicated systems.

| Responsibility    | System          |
| ----------------- | --------------- |
| World loading     | World Manager   |
| Player lifecycle  | Player Manager  |
| Save loading      | Save Manager    |
| Combat            | Combat Manager  |
| Ability execution | Ability Manager |
| Effects           | Effect Manager  |
| UI windows        | UI Manager      |

This separation keeps the Game Manager focused exclusively on coordinating global game flow.

---

# ✅ Design Rule

**GameManager is the single authority for global game state and session flow.**

It should coordinate gameplay transitions and maintain session state, but it should never own gameplay systems such as player management, world management, combat, saves, or effects.

All engine systems should react to the Game Manager's state rather than attempting to control global game flow themselves.
