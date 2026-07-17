````md
# ⚙️ Engine Systems

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

````
