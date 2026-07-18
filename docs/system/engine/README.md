# ⚙️ Engine System

The **Engine Systems layer** provides the foundational runtime infrastructure that powers the Ecks Framework.

These systems are responsible for:

- Framework startup and initialization
- Global runtime state management
- Save and persistence systems
- Localization infrastructure
- Scene transitions
- Core engine services

Engine systems operate independently from gameplay content, providing reusable framework-level functionality that supports every project built on Ecks.

---

# 🧠 Engine Architecture

The engine layer provides the foundation that all gameplay systems build upon.

Architecture:

```text
                 Engine Systems

                       |
        --------------------------------
        |              |               |
 BootManager     GameManager     SaveManager
        |
        |
 LocalizationManager
        |
 TransitionManager
````

---

# 🚀 Boot Orchestrator

The **Boot Orchestrator** is the central initialization authority for the Ecks Framework.

It controls how framework systems enter runtime by managing:

* Startup sequencing
* Dependency ordering
* System initialization
* Database loading
* Player creation
* System validation
* Boot completion state

Instead of allowing autoloads and managers to initialize independently, all major framework systems enter through a controlled initialization pipeline.

Architecture:

```text
Application Start
        ↓
 BootManager
        ↓
 Initialization Phases
        ↓
 Framework Systems Ready
        ↓
 Gameplay Available
```

Documentation:

- [Boot Orchestrator](boot_orchestrator.md)

---

# 🎮 Game Manager

The **Game Manager** is the central authority for runtime game flow.

It coordinates the lifecycle of a play session by managing:

* Game states
* Scene transitions
* World loading
* Player persistence
* Pause and game-over flow
* Session tracking
* Save integration
* Debug controls

Rather than owning gameplay logic, GameManager acts as the bridge between framework systems:

```text
Boot
 ↓
Game Session
 ↓
World
 ↓
Player
 ↓
Save System
 ↓
UI / Transitions
```

Documentation:

- [Game Manager](game_manager.md)

---

## 🌐 Localization System

The Localization System provides the multilingual foundation for the Ecks Framework.

It separates localization into two layers:

| System | Responsibility |
|---|---|
| LanguageManager | Runtime locale control and translation loading |
| CSV → PO Generator | Creator workflow for generating translation files |

The system supports:

- 23 languages
- English-key translation lookup
- Runtime language switching
- Persistent language settings
- CSV-based translation management
- Automated PO generation

Documentation:

- [Localization System](localization.md)

---

## 💾 Save System

The **Save System** provides persistent game state management for the Ecks Framework.

It separates save authority from gameplay data ownership by allowing individual systems to serialize their own data while `SaveManager` controls the overall persistence pipeline.

Features:

- Resource-based save files
- Expandable save slot system
- Autosave support
- Quicksave support
- Manual save slots
- Runtime state serialization
- Player persistence
- World persistence
- Chunk persistence
- Save version migration

Architecture:

```text
Gameplay Systems

        |
        ↓

   SaveManager

        |
        ↓

   SaveData

        |
        ↓

user://saves/
```

Documentation:

- [Save Manager](save_manager.md)

---

## 🌗 Transition Manager

The **Transition Manager** provides the visual transition layer for the Ecks Framework.

It separates transition presentation from gameplay logic by allowing systems such as `GameManager` and `SaveManager` to request visual transitions without owning fade effects themselves.

Features:

- Centralized screen fade system
- Scene transition visuals
- Configurable fade timing
- Fade in / fade out support
- Callback-based transition sequences
- CanvasLayer-based overlay rendering
- Loading and gameplay transition support

Architecture:

```text
GameManager / Systems

        |
        ↓

TransitionManager

        |
        ↓

 Fade Overlay

        |
        ↓

 Player Screen
```

Documentation:

- [Transition Manager](transition_manager.md)
