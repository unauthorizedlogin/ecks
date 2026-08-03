# ⚙️ Engine System

## Overview

The **Engine Systems layer** provides the foundational runtime infrastructure that powers the Ecks Framework.

These systems operate independently from gameplay content, providing reusable framework-level functionality that supports every project built on Ecks.

The Engine layer is responsible for:

- Framework startup and initialization
- Global runtime state management
- Save and persistence systems
- Localization infrastructure
- Scene transitions
- Core engine services

---

# 🧠 Engine Architecture

The Engine layer provides the foundation that all gameplay systems build upon.

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

Engine systems establish the runtime environment before gameplay systems become active.

---

# 🔗 Engine System Documentation

The following systems make up the Engine layer.

| System | Purpose | Documentation |
|---|---|---|
| 🚀 Boot Orchestrator   | Framework startup, initialization sequencing, and system readiness management  | [Boot Orchestrator](./boot/boot_orchestrator.md)   |
| 🧠 Game Manager        | Runtime game flow, session state, world coordination, and system communication | [Game Manager](./gamestate/game_manager.md)             |
| 🌐 Localization System | Runtime language management and translation infrastructure                     | [Localization System](./localization/localization.md)      |
| 🔑 Requirements        | Centralized gameplay requirement validation for game systems                   | [Requirements](./requirements/requirements_manager.md)     |
| 💾 Save System         | Persistent game state management and serialization pipeline                    | [Save System](./save/save_manager.md)              |
| 🌗 Transition Manager  | Visual scene transitions, fades, and loading presentation                      | [Transition Manager](./transition/transition_manager.md) |
