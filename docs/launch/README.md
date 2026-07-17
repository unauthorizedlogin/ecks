## 🚀 Launch System README

The Launch Flow System provides the complete startup pipeline responsible for transitioning the framework from application launch into an active gameplay session.

It manages the full entry sequence:

- Framework boot initialization
- Startup database loading
- User settings application
- Title screen routing
- New game configuration
- Player/class/difficulty selection
- World initialization
- Runtime system activation

The Launch Flow separates application startup from gameplay execution, ensuring all required systems are prepared before the player enters the world.

Architecture:

```text
Application Launch

        |
        ↓

   Bootloader

        |
        ↓

 Initialization

        |
        ↓

 Title Screen

        |
        ↓

 Start Menu

        |
        ↓

 World Initialization

        |
        ↓

 Gameplay Runtime
```

Documentation:

- [Launch System](docs/launch/launch_system.md)

---
