# 🎮 Game Manager — Global Game Flow Authority

`GameManager` is the **central authority for runtime game flow**.

It manages the lifecycle of a play session by controlling:

* Game states
* Scene and level transitions
* Player persistence
* World loading
* Pause/game-over flow
* Session tracking
* Save/load integration
* Debug controls

`GameManager` does not define gameplay systems. Instead, it coordinates the systems responsible for gameplay.

It acts as the bridge between:

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

---

# 🧠 Core Responsibility

The Game Manager answers:

> "What state is the game currently in, what world should be active, and how should the game transition between states?"

It provides a single authority for:

* entering gameplay
* leaving gameplay
* loading worlds
* pausing
* resuming
* ending runs
* restoring sessions

---

# 📘 Game State Management

The Game Manager maintains the global runtime state through:

```gdscript
enum GameState {
    MENU,
    PLAYING,
    PAUSED,
    CUTSCENE,
    LOADING
}
```

Current state:

```gdscript
state
```

Previous state:

```gdscript
previous_state
```

Example flow:

```text
MENU
 ↓
LOADING
 ↓
PLAYING
 ↓
PAUSED
 ↓
PLAYING
 ↓
GAME OVER
```

---

# 🔔 State Change Broadcasting

State transitions emit:

```gdscript
signal game_state_changed(old_state, new_state)
```

Allows systems to react without directly depending on GameManager logic.

Examples:

```text
GameManager
      |
      |
      ↓

UI Manager
  Pause Menu

Audio Manager
  Music State

Combat System
  Enable / Disable

Input Manager
  Lock Controls
```

---

# 🚀 New Game Initialization

Starting a new game is handled through:

```gdscript
start_new_game()
```

Responsibilities:

* Set player name
* Select starting class
* Set difficulty
* Reset deaths
* Reset playtime
* Clear save-loading state
* Transition into first level

Example:

```gdscript
start_new_game(
    "Hero",
    "difficulty_normal",
    "warrior"
)
```

Flow:

```text
New Game
    |
    ↓
Reset Session Data
    |
    ↓
Set LOADING
    |
    ↓
Fade Out
    |
    ↓
Load Starting World
    |
    ↓
PLAYING
```

---

# 🌍 Scene Loading Authority

GameManager owns scene transitions.

Scene requests are protected by:

```gdscript
is_transitioning
```

This prevents:

* duplicate loads
* multiple scene changes
* transition conflicts

---

# 🎬 Transition Pipeline

All major scene changes follow:

```text
Request Scene
      |
      ↓
Check Transition Lock
      |
      ↓
Emit loading_scene
      |
      ↓
TransitionManager.fade_out()
      |
      ↓
Load Scene
      |
      ↓
Emit scene_loaded
      |
      ↓
TransitionManager.fade_in()
```

Signals:

```gdscript
loading_scene(path)

scene_loaded(path)
```

allow transition UI and loading screens to react.

---

# 🗺️ Level Loading System

The Game Manager supports two level workflows.

---

# 📊 Legacy Level Loading

Using:

```gdscript
load_level(level:int)
```

Levels are resolved through:

```gdscript
LevelData
```

Structure:

```text
LevelData
    |
    |
    +── LevelEntry
          |
          ├── Level Number
          ├── Title
          └── PackedScene
```

Example:

```text
Level 1
 |
 └── Forest Valley

Level 2
 |
 └── Ancient Ruins
```

---

# 🗺️ Data-Driven Level IDs

Modern transitions use:

```gdscript
change_level(level_id)
```

Levels are resolved through:

```gdscript
LevelDatabase
```

Flow:

```text
Level ID
    |
    ↓
Level Database
    |
    ↓
Scene Path
    |
    ↓
PackedScene
    |
    ↓
World Generation
```

Example:

```gdscript
change_level(
    "forest_region",
    "north_gate"
)
```

---

# 🌎 World Replacement Pipeline

When loading a new world:

```text
Current World
      |
      ↓
Free Existing Scene
      |
      ↓
Instantiate New World
      |
      ↓
Attach To Scene Tree
      |
      ↓
Rebuild Environment
      |
      ↓
Restore Player
      |
      ↓
Restore Chunk State
      |
      ↓
Enable Gameplay
```

---

# 🧍 Player Injection System

GameManager owns player persistence between worlds.

The player is injected into the active world after loading.

Search priority:

```text
SpawnPoints
      ↓
TileMap
      ↓
World Root
```

Flow:

```text
Player Instance
        |
        ↓
GameManager
        |
        ↓
New World
        |
        ↓
Spawn Position
```

This allows:

* persistent player instances
* seamless level transitions
* save restoration

---

# 📦 Save System Integration

GameManager provides world/session data required by SaveManager.

Tracked data:

```gdscript
current_level
current_level_id
player_position
chunk_state
```

---

# 💾 World Serialization

World state is collected through:

```gdscript
serialize_world()
```

Currently tracks:

```text
Chunks
 |
 └── Destroyed State
```

Example:

```json
{
    "chunk_01": {
        "destroyed": true
    }
}
```

---

# 🔄 World Restoration

Saved world data is restored through:

```gdscript
deserialize_world()
```

Restores:

* destroyed objects
* chunk changes
* world persistence

---

# 🧱 Chunk System Integration

GameManager coordinates with:

```gdscript
ChunkManager
```

During loading:

```text
Save Data
    |
    ↓
Chunk State
    |
    ↓
ChunkManager.deserialize_chunk_state()
    |
    ↓
World Restored
```

This allows persistent procedural worlds.

---

# ⏸️ Pause System

Pause control is centralized:

```gdscript
pause_game()
resume_game()
```

Pause flow:

```text
PLAYING
   |
   ↓
PAUSED
   |
   ↓
Freeze Game Tree
   |
   ↓
Open Pause UI
```

Uses:

```gdscript
get_tree().paused
```

---

# ☠️ Game Over Flow

Game over handling:

```gdscript
go_to_game_over()
```

Pipeline:

```text
Player Death
      |
      ↓
Pause State
      |
      ↓
Fade Out
      |
      ↓
Open Game Over UI
```

---

# 🏆 Credits Flow

Ending the game:

```gdscript
go_to_credits()
```

Pipeline:

```text
Gameplay
    |
    ↓
LOADING
    |
    ↓
Credits Scene
```

---

# ⏱️ Session Tracking

GameManager tracks:

```gdscript
total_playtime
```

Only increases while:

```gdscript
GameState.PLAYING
```

Example:

```text
PLAYING
 + Time Added

PAUSED
 + No Time Added

MENU
 + No Time Added
```

---

# 💀 Death Tracking

Player deaths are tracked globally:

```gdscript
deaths
```

Updated through:

```gdscript
register_death()
```

Used for:

* statistics
* achievements
* difficulty tracking
* analytics

---

# 🧪 Debug Authority

GameManager provides development tools:

```gdscript
enable_debug_mode()

skip_to_level()

force_state()
```

Debug tools allow:

* instant level testing
* state testing
* rapid iteration

---

# 🔗 System Relationships

Final architecture:

```text
                    BootManager
                        |
                        ↓

                 GameManager
                        |
        +---------------+---------------+
        |               |               |
        ↓               ↓               ↓

 TransitionManager  SaveManager   LevelDatabase

        |
        ↓

     World Scene
        |
        +-------------+
        |             |
        ↓             ↓

 ChunkManager     Player

        |
        ↓

      UIManager
```

---

# Framework Role

The Game Manager provides:

✅ centralized game state authority
✅ safe scene transitions
✅ data-driven level loading
✅ persistent player handling
✅ save/load coordination
✅ world restoration
✅ pause and game-over flow
✅ session statistics
✅ debug tooling

The Game Manager prevents individual systems from controlling global flow independently.

Instead, every major runtime transition passes through a single authority, creating a predictable foundation for large-scale RPG architecture.
