# 🌎 World Systems README

## Overview

The World Systems framework provides the foundation for managing world loading, map structure, environmental presentation, interactive objects, and player navigation.

The system is designed around a **data-driven world architecture**, separating:

- Map data definitions
- Chunk loading and unloading
- Runtime world streaming
- Interactive world objects
- Environmental systems
- Player transportation

World systems are responsible for managing the game world state while remaining independent from gameplay systems such as quests, combat, and progression.

---

# 🧠 System Architecture

The World Systems are organized into independent systems working together:

```text
                         Map Resource
                              |
                              ↓
                         Map Scene
                              |
                              ↓
                     World Management Layer
                              |
        ┌──────────────┬──────────────┬──────────────┐
        ↓              ↓              ↓              ↓

     Chunks       Environment     Destroyables   Teleporters
        |
        ↓
 Chunk Manager
        |
        ↓
 Chunk Loader
```

