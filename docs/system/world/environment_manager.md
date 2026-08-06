# 🌦️ Environment Manager

## Overview

The **Environment Manager** is the central authority for applying world environment presentation within the framework.

It translates environment resource data into the active world's weather, atmospheric effects, music, and visual color treatment.

The manager separates **environment configuration** from **environment presentation**. World data defines the desired environment, while the Environment Manager applies that configuration to the active world.

This includes:

* 🌧️ Rain
* ❄️ Snow
* ☁️ Clouds
* 🍃 Leaves
* ☀️ Raylight
* 🌫️ Fog
* 🎵 Environment music
* 🎨 Color correction

---

# 🧠 Core Responsibilities

The Environment Manager provides:

* Environment initialization
* World environment attachment
* Environment resource application
* Weather effect control
* Atmospheric effect control
* Environment music selection
* Color correction
* Environment refresh

The manager does not define environmental content itself.

Environment behavior is driven by `ResourceEnvironment` data.

---

# ⚙️ Initialization

```gdscript
func initialize()
```

Registers the Environment Manager as an active framework system.

Prevents duplicate initialization.

Output:

```text
🌦️ EnvironmentManager initialized
```

---

# 🌍 World Attachment

```gdscript
func attach_world(world, world_map)
```

Connects the manager to the environment components belonging to the active world.

The current world provides:

* Rain particles
* Snow particles
* Cloud particles
* Leaf particles
* Raylight particles
* Fog
* Music controller
* Color correction

The active `Map` reference is also retained so the manager can later determine which environment configuration applies to the current location.

```text
World
 ├── CameraGrid
 │    ├── Rain
 │    ├── Snow
 │    ├── Cloud
 │    ├── Leaf
 │    └── Raylight
 │
 ├── Pivot
 │    └── Fog
 │
 ├── Music
 │
 └── ScreenFxLayer
      └── ColorCorrection
```

---

# 🌦️ Environment Resource Pipeline

Environmental presentation is driven by a `ResourceEnvironment`.

```text
ResourceEnvironment
        ↓
EnvironmentManager
        ↓
Active World Environment
```

The resource defines which environmental conditions should be active.

The manager translates those conditions into the corresponding world presentation systems.

---

# 🌧️ Weather System

Weather conditions are represented through the environment resource's meteorological configuration.

Supported effects include:

* Rain
* Snow
* Clouds
* Leaves
* Raylight
* Fog

Each environmental condition independently controls its corresponding presentation layer.

Example:

```text
Environment:
Rain + Cloud + Fog
        ↓
EnvironmentManager
        ↓
Rain    = Active
Cloud   = Active
Fog     = Active
Snow    = Disabled
Leaf    = Disabled
Raylight = Disabled
```

This allows environments to combine multiple atmospheric conditions without requiring separate environment logic for each combination.

---

# 🌫️ Atmospheric Effects

Fog is managed as part of the environment presentation layer.

Unlike the particle-based weather effects, fog uses the world's atmospheric UI layer.

The Environment Manager enables or disables fog based on the environment resource.

---

# 🎵 Environment Music

Environmental resources may define an associated music track.

When an environment specifies music:

```text
ResourceEnvironment
        ↓
EnvironmentManager
        ↓
World Music Controller
        ↓
Selected Track
```

If no environment music is configured, the current environment music is stopped.

This allows music to follow world environment configuration rather than being hardcoded into individual maps.

---

# 🎨 Color Correction

Environmental color treatment is also data-driven.

The environment resource provides a color gradient which is assigned to the world's color-correction layer.

This allows different environments to alter the visual tone of the world without requiring unique rendering logic.

Examples include:

* Warm environments
* Cold environments
* Dark environments
* Atmospheric regions
* Weather-specific color treatment

---

# 🔄 Environment Refresh

```gdscript
func refresh_environment()
```

Re-evaluates the environment currently associated with the active map.

The manager queries the map's environment area and retrieves its `ResourceEnvironment`.

```text
Active Map
    ↓
Environment Area
    ↓
ResourceEnvironment
    ↓
EnvironmentManager
    ↓
World Presentation
```

This allows environmental presentation to be refreshed after map changes or other world state changes without rebuilding the environment system.

---

# 🗺️ Map Integration

The Environment Manager maintains a reference to the active `Map`.

The map determines the applicable environment configuration through its environment area.

This establishes a separation between:

**Map**

> Determines which environment applies.

**Environment Resource**

> Defines what the environment contains.

**Environment Manager**

> Applies the environment to the active world.

---

# 🔗 System Relationships

```text
                    WorldManager
                         |
                         ↓
                EnvironmentManager
                         |
              ┌──────────┴──────────┐
              ↓                     ↓
             Map            ResourceEnvironment
              |                     |
       Environment Area             |
              └──────────┬──────────┘
                         ↓
                EnvironmentManager
                         |
        ┌────────────────┼────────────────┐
        ↓                ↓                ↓
     Weather          Music         Color Correction
        |                |                |
        ↓                ↓                ↓
   Particles           Audio          Screen FX
```

---

# 🧭 System Boundaries

The Environment Manager coordinates environmental presentation but delegates specialized behavior.

| Responsibility         | System                |
| ---------------------- | --------------------- |
| World lifecycle        | World Manager         |
| Map selection          | World / Map System    |
| Environment definition | `ResourceEnvironment` |
| Weather presentation   | Environment Manager   |
| Music playback         | World Music System    |
| Visual post-processing | World Screen FX       |
| Environmental location | Map Environment Area  |

This keeps environmental configuration independent from the systems that render or play it.

---

# 🌱 Data-Driven Environment Architecture

The environment system intentionally separates **content definition** from **runtime presentation**.

```text
Environment Data
      ↓
ResourceEnvironment
      ↓
EnvironmentManager
      ↓
World Presentation
```

This allows environment definitions to be generated, stored, reused, and assigned to different maps without duplicating environmental implementation.

---

# 🚀 Launch Flow Integration

The Environment Manager is initialized as part of the framework's centralized Launch Flow and attaches itself to the active world as that world becomes available.

This establishes environmental presentation as framework infrastructure rather than requiring every world scene to independently configure weather, music, and visual effects.

---

# ✅ Design Rule

**EnvironmentManager is the central authority for applying environment configuration to the active world.**

Maps and environment resources define **what environment should exist**. The Environment Manager determines **how that environment is applied** to the active world's weather, atmosphere, music, and visual presentation.

Future environmental systems should extend this data-driven pipeline rather than embedding environment-specific configuration directly into individual world scenes.
