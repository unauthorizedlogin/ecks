# 🗺️ World Launch Controller

## Overview

`launch_03world.gd` is the primary controller for the active game world scene.

It manages the complete world startup and rebuild lifecycle, including:

- Map resource resolution
- Dynamic map instantiation
- Player placement
- Camera initialization
- Environment loading
- Weather and visual effects
- UI initialization
- Chunk streaming startup
- World readiness signaling

The script acts as the bridge between global managers and the active world instance.

---

# 🌍 World Responsibilities

The World Launch Controller manages:

```

Bootloader
↓
launch_03world
↓
LevelResource Resolution
↓
Map Generation
↓
Player Placement
↓
Camera Setup
↓
Environment Setup
↓
Chunk Streaming
↓
World Ready

````

---

# Core Variables

## Active Map

```gdscript
var map: Map
````

Stores the currently loaded map instance.

The map is dynamically generated from:

```gdscript
LevelResource.scene
```

---

## Starting Map

```gdscript
@export var starting_map: PackedScene
```

Fallback map used when no active level ID has been assigned.

Priority:

```
GameManager.current_level_id
        ↓
LevelDatabase lookup
        ↓
LevelResource.scene
        ↓
PackedScene load

Fallback:
starting_map
```

---

# Environment Systems

The world controller caches environment presentation nodes.

## Weather

```gdscript
rain
snow
cloud
leaf
raylight
fog
```

Controlled through:

```gdscript
apply_environment()
```

---

## Audio

```gdscript
music
```

Environment resources can define:

```gdscript
ResourceEnvironment.music
```

The world automatically switches music based on the active environment.

---

## Visual Effects

```gdscript
color_correction
brightness_overlay
transition
```

Handles:

* Color grading
* Brightness control
* Screen transitions

---

# Camera System

References:

```gdscript
camera_grid
pivot
```

Responsibilities:

* Following the player
* Camera transitions
* Pivot synchronization

---

# World State System

The world lifecycle is tracked through:

```gdscript
enum WorldState
```

States:

```gdscript
BOOTING
LOADING
INITIALIZING
MAP_READY
PLAYER_READY
FULLY_READY
```

Final state:

```gdscript
WorldState.FULLY_READY
```

---

# World Ready Signal

```gdscript
signal world_ready
```

Emitted after:

* Map exists
* Player exists
* Camera target exists
* Environment is initialized
* Chunk systems are active

---

# 🚀 Boot Sequence

## `_ready()`

Main startup function.

Execution order:

```
Resolve Active Map
        ↓
Generate Map
        ↓
Initialize Camera
        ↓
Connect Signals
        ↓
Initialize UI
        ↓
Apply Settings
        ↓
Rebuild World
        ↓
World Ready
```

---

# Map Resolution

The controller checks:

```gdscript
GameManager.current_level_id
```

If available:

```
Level ID
   ↓
LevelDatabase
   ↓
LevelResource
   ↓
Scene Path
   ↓
PackedScene
```

The resulting scene becomes the active world map.

---

# Map Generation

Function:

```gdscript
generate_map()
```

Responsibilities:

* Remove previous map
* Instantiate new map
* Attach to world
* Connect environment events

Flow:

```
Existing Map
      ↓
queue_free()

New PackedScene
      ↓
instantiate()

New Map Instance
      ↓
Add Child
```

---

# Environment Pipeline

## apply_environment()

Updates world presentation from:

```gdscript
ResourceEnvironment
```

Controls:

## Weather

```gdscript
RAIN
SNOW
CLOUD
LEAF
FOG
RAY
```

---

## Music

If environment contains music:

```
Environment Resource
        ↓
Music Change
```

Otherwise:

```
Stop Music
```

---

## Color

Applies:

```gdscript
color_gradient
```

to:

```gdscript
ColorCorrection
```

---

# Bootloader Visual Handling

Function:

```gdscript
bootloader_setup()
```

Used when launching through the boot system.

Purpose:

Prevent duplicate brightness effects.

Behavior:

```
Bootloader Launch
        ↓
Disable Brightness Overlay
```

---

# World Rebuild System

## rebuild_world()

The primary world reconstruction pipeline.

Used for:

* World loading
* Save loading
* Teleports
* Map switching

---

# Rebuild Order

```
Disable World
        ↓
Reset Chunk State
        ↓
Reset Environment
        ↓
Acquire Player
        ↓
Move Player To Spawn
        ↓
Assign Camera Target
        ↓
Emit Player Ready
        ↓
Refresh Environment
        ↓
Initialize Chunk Manager
        ↓
Initialize UI
        ↓
Enable World
        ↓
Finalize Setup
```

---

# Player Initialization

The world controller requests:

```gdscript
GameManager.get_or_create_player()
```

Then:

```
Remove Player From Previous Parent
        ↓
Attach To Spawn Target
        ↓
Set Position
        ↓
Assign Camera Target
```

---

# Spawn Handling

Supports:

```gdscript
spawn_point_name
```

Priority:

```
Requested Spawn Point
        ↓
SpawnPoints Node
        ↓
Map Default
```

Also supports:

```gdscript
GameManager.pending_spawn_position
```

for teleport/save restoration.

---

# Chunk System Integration

## init_world_systems()

Starts world streaming.

Validates:

```gdscript
map
camera_grid
camera_grid.target
```

Then initializes:

```gdscript
ChunkManager.init_chunk_loader()
```

Configuration:

```gdscript
Chunk Size:
512x512

Load Radius:
3

Bounds:
-1,-1 → 2,2

LOD:
1,2,3
```

---

# Chunk Lifecycle

```
World Ready
      ↓
ChunkManager Initialized
      ↓
Player Position Checked
      ↓
Required Chunks Loaded
      ↓
Unused Chunks Removed
```

---

# UI Initialization

The world controller initializes UI:

```gdscript
UIManager.init_ui(self)
```

Called during:

* Initial boot
* World rebuild

Ensures UI references remain valid after map changes.

---

# Camera Animation Sync

Signal:

```gdscript
camera_grid.animation_finished
```

Connected to:

```gdscript
on_camera_animation_finished()
```

Updates:

```gdscript
pivot.position
```

after camera movement completes.

---

# World Finalization

## finalize_world_setup()

Final validation stage.

Checks:

```gdscript
map exists
camera exists
camera target exists
```

Then:

```
World State
      ↓
FULLY_READY

Emit:
world_ready
```

---

# Runtime Safety

The controller includes validation gates for:

* Missing maps
* Missing cameras
* Missing player targets
* Invalid level resources
* Failed scene loads
* Invalid world states

---

# Current Architecture

`launch_03world.gd` provides:

- ✅ Data-driven map loading
- ✅ Runtime map swapping
- ✅ Player persistence across worlds
- ✅ Camera lifecycle control
- ✅ Environment-driven visuals
- ✅ Weather integration
- ✅ Music switching
- ✅ UI reinitialization
- ✅ Chunk streaming startup
- ✅ World readiness signaling
- ✅ Save/load safe reconstruction

---

# Future Expansion

Potential extensions:

* World streaming transitions
* Multi-world persistent states
* Dynamic weather cycles
* Procedural map generation
* Multiplayer world ownership
* Advanced world events

```
