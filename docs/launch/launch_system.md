## 🚀 Launch Flow System

The Launch Flow System provides the complete game startup pipeline, responsible for transitioning from application boot → title screen → new game setup → world initialization.

The system is designed as a **multi-stage launch pipeline**, separating:

* Initial boot configuration
* Title screen routing
* New game creation
* Player/class/difficulty selection
* World scene initialization
* Runtime system activation

This creates a flexible entry framework that supports both a traditional game flow and direct injection/testing workflows.

---

### ⚠️ Current Development Status

The Launch Flow System is currently considered **functional infrastructure but still undergoing refinement**.

The pipeline and scene routing architecture are implemented, but additional systems may continue to be integrated into the launch sequence as development progresses.

Future expansion may include additional startup validation, loading stages, save initialization, and further runtime preparation steps.

---

# 🧩 Bootloader

The Bootloader is the first runtime entry point.

Responsibilities:

* Sets initial game state
* Loads required startup databases
* Initializes core definitions
* Applies user settings
* Handles initial scene routing
* Controls transition effects

The bootloader supports two launch modes:

### Title Screen

Loads the standard player-facing entry flow.

### Injector Mode

Skips menus and directly loads the game world for testing or development workflows.

---

# 🎬 Title Screen Flow

The Title Screen manages the first player-facing interaction layer.

Responsibilities:

* Handles start input
* Routes into the selected launch mode
* Supports normal menu flow
* Supports direct world injection

The system is designed so the title screen can be expanded with:

* Continue game
* Save selection
* Options
* Credits
* Additional front-end menus

---

# 🎮 New Game Initialization

The Start Menu handles creation of a new game session.

Before starting, the player selects:

* Player name
* Character class
* Difficulty

Validation ensures required selections are completed before beginning the game.

Once confirmed, the selected values are passed into the game runtime:

```
Player Name
Class ID
Difficulty ID
```

These become the initial session parameters used by downstream systems.

---

# 🌍 World Launch System

The World scene acts as the runtime environment initializer.

Responsibilities:

* Loads the active level
* Generates the map
* Initializes player placement
* Sets up camera tracking
* Initializes UI
* Applies environmental systems
* Initializes chunk loading
* Finalizes world readiness

The world pipeline follows a staged initialization process:

```
BOOTING
 ↓
LOADING
 ↓
INITIALIZING
 ↓
MAP_READY
 ↓
PLAYER_READY
 ↓
FULLY_READY
```

---

# 🗺️ Dynamic Level Loading

The world system supports dynamic map resolution through level data.

The active level is determined through the runtime level database, allowing:

* Different starting maps
* Multiple worlds
* Future save-based loading
* Alternate game modes

---

# 🌦️ Environment Integration

World initialization supports environmental data including:

* Weather effects
* Fog
* Lighting effects
* Color correction
* Music changes

Environmental resources control the presentation layer of each area.

---

# 🎥 Player & Camera Initialization

The world rebuild process handles:

* Player creation/retrieval
* Spawn point resolution
* Player placement
* Camera targeting
* Player-ready signaling

This allows the player object to persist across world transitions while the environment is rebuilt around it.

---

# 🧱 World System Initialization

The world startup pipeline initializes:

* Chunk loading
* Dynamic world streaming
* UI systems
* Environment state
* Runtime world state

Chunk loading is reset and rebuilt per world instance to prevent stale runtime state.

---

# 🔄 Scene Transition Support

The launch system includes transition handling for clean scene changes.

Features:

* Fade-out before loading
* Scene replacement
* Optional bootloader hooks
* Fade-in after initialization

Scenes can expose:

```gdscript
bootloader_setup()
```

for custom startup behavior.

---

# 🧠 System Integration

The Launch Flow currently connects with:

* GameManager
* SettingsManager
* TransitionManager
* QuestManager
* QuestBehaviorMatrix
* UIManager
* LevelDatabase
* ChunkManager
* DifficultyDatabase

This creates the foundation for future additions such as:

* Save loading
* Character selection
* Multiplayer initialization
* Mod loading
* Additional startup validation

---

# 📌 Summary

The Launch Flow System provides the centralized startup architecture responsible for moving the game from initial boot into a fully initialized playable world.

It separates front-end navigation, new game configuration, and world construction into independent stages, allowing future expansion without restructuring the game's entry pipeline.
