# 🌦️ Environment System Architecture

## Overview

The Environment System provides a fully data-driven framework for creating dynamic world regions with custom weather, atmosphere, lighting, audio, and camera behavior.

Instead of manually scripting environmental changes into each map, creators define reusable **Environment Resources** and place configurable environment zones throughout their world.

The system is designed around a simple workflow:

1. Create or select an environment preset
2. Drop an Environment Area into a map
3. Add environment shapes to define affected regions
4. Assign the desired environment resource
5. The world automatically applies the correct environmental state when the player enters

This allows large worlds to contain unique biomes, weather regions, ambience zones, and atmospheric transitions without custom scripting.

---

# 🏗️ Environment System Architecture

The system is divided into several modular layers:

```

Environment Area
│
├── Environment Shape
│       |
│       └── Resource Environment
│
├── Weather System
│       |
│       └── Meteo Effects
│
├── Music System
│       |
│       └── Ambient Tracks
│
├── Visual Systems
│       |
│       ├── Fog
│       ├── Lighting
│       └── Color Grading
│
└── Camera Systems
|
└── Grid Based Camera Control

```

Each system responds to environment changes independently, allowing creators to expand the framework without modifying existing maps.

---

# 🌎 ResourceEnvironment

## Data-Driven Environment Definition

The core of the system is the `ResourceEnvironment`.

This resource contains all information required to define a world region's atmosphere.

Environment resources control:

- Weather effects
- Ambient music
- Color gradients
- Environmental presets

Resources can be reused across multiple maps and zones.

Examples:

### Forest Environment

```

Weather:

* Rain
* Leaves

Music:

* Forest Ambient

Color:

* Green atmospheric gradient

```

### Snow Region

```

Weather:

* Snow
* Fog

Music:

* Frozen ambience

Color:

* Cold blue gradient

```

### Dungeon Environment

```

Weather:

* Fog

Music:

* Dark ambience

Color:

* Desaturated lighting

```

---

# 🌦️ Weather Integration

The environment system supports multiple atmospheric effects through configurable weather types.

Available environment effects include:

- Rain
- Snow
- Fog
- Clouds
- Falling Leaves
- Light Rays

Multiple effects can be combined inside a single environment.

Example:

```

Mountain Storm

*

Rain
+
Fog
+
Clouds
+
Storm Music

```

This allows complex environments to be created without additional scripting.

---

# 🧩 Environment Areas

## Zone-Based Environmental Triggers

Environment Areas define regions where environmental changes occur.

Creators place an Environment Area inside a map and define its affected space using Environment Shapes.

When the player enters a shape:

```

Player
|
v
Environment Shape
|
v
Resource Environment
|
v
Environment Systems Update

```

The area broadcasts the new environment state to connected systems.

---

# 🔷 Environment Shapes

Environment Shapes define the exact boundaries of environmental zones.

They are designed for creator workflow:

- Place a shape
- Resize it
- Assign a resource
- Done

Supported uses:

- Forest boundaries
- Dungeon interiors
- Weather pockets
- Regional ambience
- Indoor/outdoor transitions

Each shape can have its own environment resource.

Example:

```

Large Map

Forest Zone
└── Rain Environment

Village Zone
└── Clear Weather Environment

Cave Zone
└── Fog Environment

```

---

# 🛠️ Editor Integration

Environment Shapes include editor tooling to improve creator workflow.

Features:

- Custom editor icon
- Live resource preview
- Debug labels
- Environment information display
- Resource change updates

Creators can see assigned environment information directly inside the editor without entering play mode.

Example:

```

Forest Rain
rain/leaves
forest_theme.ogg

```

---

# 🎨 Particle-Based Environment Effects

A major part of the Environment System is the configurable particle framework.

Weather effects are created through reusable particle configurations rather than hardcoded effects.

Creators can configure:

- Particle textures
- Animation frames
- Emission behavior
- Movement patterns
- Density
- Speed
- Direction
- Area coverage

The engine provides ready-to-use presets while still allowing complete customization.

Examples:

### Rain Preset

```

Texture:
Rain Drop

Behavior:
Vertical fall

Settings:
High density
Fast movement
Large coverage

```

### Snow Preset

```

Texture:
Snowflake

Behavior:
Slow drift

Settings:
Low gravity
Random movement
Wide spread

```

### Leaf Preset

```

Texture:
Leaves

Behavior:
Wind movement

Settings:
Rotation
Random velocity
Seasonal colors

```

---

# 🎶 Environment Music System

Environmental regions can control ambient music automatically.

The music system supports:

- Track switching
- Smooth transitions
- Fade in/out
- Preventing duplicate playback

Example:

```

Entering Forest

Current:
Town Theme

Transition:

Fade Out
|
v
Forest Ambient
|
v
Fade In

```

This allows seamless world transitions without manual music triggers.

---

# 🌫️ Fog System

The fog system provides atmospheric overlays through shader-based effects.

Used for:

- Mist
- Dungeon atmosphere
- Toxic areas
- Magical zones
- Weather effects

Features:

- Smooth fade transitions
- Shader controlled opacity
- Runtime activation
- Zone-based control

Example:

```

Outside Cave

Fog:
0%

Enter Cave

Fog:
100%

```

---

# 🎥 Camera Grid System

The environment framework includes a grid-based camera system designed for structured world layouts.

The camera supports:

- Grid snapping
- Smooth transitions
- Cell-based movement
- Editor alignment
- Teleport support

Useful for:

- Large segmented maps
- Room-based worlds
- Screen transition games
- Tactical layouts

---

# 🗺️ Map Integration

Maps contain a base environment reference allowing all maps to use the same environment pipeline.

The base map scene provides:

- Environment area access
- World integration
- Screen alignment references
- Shared system hooks

Because maps inherit from the base map scene, framework-wide environment improvements can be applied globally.

---

# 🧑‍🎨 Creator Workflow

Creating an environmental region:

## Step 1

Create or select an environment preset.

```

ResourceEnvironment

```

---

## Step 2

Add an Environment Area.

```

Map
└── EnvironmentArea

```

---

## Step 3

Add Environment Shapes.

```

EnvironmentArea
|
├── ForestShape
|
├── CaveShape
|
└── RiverShape

```

---

## Step 4

Assign environment resources.

```

ForestShape
|
└── ForestRainEnvironment

```

---

## Result

The environment automatically changes when the player enters each zone.

---

# ✨ Features

- Fully data-driven environment system
- Resource-based presets
- Zone-based environmental switching
- Weather combinations
- Particle-driven effects
- Ambient music transitions
- Fog overlays
- Color grading support
- Editor visualization tools
- Reusable environment definitions
- No scripting required for standard zones
- Mass-producible world regions

---

# Summary

The Environment System provides a scalable framework for creating immersive world regions through reusable data assets.

Creators do not need to manually script weather, ambience, or atmospheric transitions.

Instead, they define environments once, place configurable zones into their maps, and the framework handles the transitions automatically.

This allows entire worlds to be built from reusable environmental building blocks while keeping every region customizable.
```
