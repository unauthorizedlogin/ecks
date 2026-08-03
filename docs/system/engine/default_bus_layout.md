# 🔊 Audio Bus Layout System

## Overview

The `default_bus_layout.tres` file defines the game's audio mixer architecture.

It establishes the audio bus hierarchy used to route, group, process, and balance all runtime audio categories.

The bus layout provides:

- Master volume control
- Music routing
- Sound effect routing
- Environmental audio separation
- Weather audio processing
- Voice/dialogue separation
- Effect processing chains
- Independent volume balancing

The layout is loaded by Godot's audio server and acts as the foundation for the Audio System.

---

# Audio Routing Architecture

```

Master
│
├── Music
│   ├── Background
│   ├── Battle
│   ├── Cutscene
│   ├── Dungeon
│   ├── Town
│   ├── Jingle
│   └── Credits
│
├── Sound
│   ├── PlayerSFX
│   │   ├── Player Footsteps
│   │   ├── Player Weapon
│   │   └── Player Ability
│   │
│   ├── EnemySFX
│   │   ├── Enemy Footsteps
│   │   ├── Enemy Weapon
│   │   └── Enemy Ability
│   │
│   ├── UI
│   │
│   └── EnvironmentSFX
│       ├── Weather
│       │   ├── Rain
│       │   ├── Wind
│       │   └── Thunder
│       │
│       ├── Water
│       │   ├── Waves
│       │   ├── Waterfall
│       │   └── River
│       │
│       ├── Ambience
│       │   ├── Wildlife
│       │   ├── Insects
│       │   ├── Fire
│       │   ├── Villagers
│       │   ├── CaveDrips
│       │   └── Darkness
│       │
│       ├── NaturalEvents
│       │   ├── Avalanches
│       │   ├── Quakes
│       │   └── Volcanoes
│       │
│       └── Doors/Chests
│
└── VO
├── PlayerVO
├── NPCVO
└── EnemyVO

```

---

# Master Bus

## Master

The root audio output channel.

Responsibilities:

- Final volume control
- Global mute
- Final audio limiting

Effects:

| Effect | Purpose |
|-|-|
| Limiter | Prevents clipping and protects output levels |

---

# Music System

## Music Bus

Parent bus for all musical tracks.

Default volume:

```

-5.93 dB

```

Children:

| Bus | Purpose |
|-|-|
| Background | Exploration/world music |
| Battle | Combat music |
| Cutscene | Narrative sequences |
| Dungeon | Dungeon themes |
| Town | Safe zone music |
| Jingle | Short musical stingers |
| Credits | End-game music |

Music categories can be dynamically faded or swapped without affecting other audio systems.

---

# Sound Effects System

## Sound Bus

Parent bus for gameplay audio.

Children:

- Player effects
- Enemy effects
- UI
- Environment

---

# Player Sound Effects

## PlayerSFX

Handles all player-generated audio.

Children:

| Bus | Purpose |
|-|-|
| Player Footsteps | Movement sounds |
| Player Weapon | Attacks and weapon interactions |
| Player Ability | Skill and spell effects |

Example:

```

Player attack
↓
Player Weapon
↓
PlayerSFX
↓
Sound
↓
Master

```

---

# Enemy Sound Effects

## EnemySFX

Handles NPC/enemy generated gameplay sounds.

Children:

| Bus | Purpose |
|-|-|
| Enemy Footsteps | Movement |
| Enemy Weapon | Attacks |
| Enemy Ability | Skills |

---

# Environment Audio System

## EnvironmentSFX

Parent channel for world-based sounds.

Categories:

- Weather
- Water
- Ambient life
- Natural events
- Interactable objects

---

# Weather Audio

## Weather

Applies global environmental processing.

Effect:

| Effect | Purpose |
|-|-|
| Low Pass Filter | Allows weather intensity/muffling effects |

Children:

| Bus | Purpose |
|-|-|
| Rain | Rainfall audio |
| Wind | Wind ambience |
| Thunder | Storm events |

Effects:

### Rain

```

Reverb

```

Adds environmental space.

### Wind

```

Chorus

```

Adds movement and atmospheric variation.

### Thunder

```

Compressor

```

Controls extreme volume spikes.

---

# Water Audio

## Water

Controls aquatic environmental sounds.

Children:

| Bus | Purpose |
|-|-|
| Waves | Shoreline and ocean sounds |
| Waterfall | Falling water |
| River | Flowing water |

---

# Ambience System

## Ambience

Controls location-based background sounds.

Children:

| Bus | Purpose |
|-|-|
| Wildlife | Animals and creatures |
| Insects | Small environmental sounds |
| Fire | Campfires and flames |
| Villagers | Crowd and town ambience |
| CaveDrips | Underground environments |
| Darkness | Horror/void environments |

Effects:

### Wildlife

```

Reverb

```

Creates environmental depth.

### Darkness

```

Reverb

```

Creates isolated/spatial atmosphere.

---

# Natural Events

## NaturalEvents

Large world event sounds.

Children:

| Bus | Purpose |
|-|-|
| Avalanches | Large terrain events |
| Quakes | Earth movement |
| Volcanoes | Volcanic events |

Effects:

### Quakes

```

Compressor
Limiter

````

Used to control extremely loud environmental events.

---

# Voice System

## VO

Dialogue and character vocal routing.

Children:

| Bus | Purpose |
|-|-|
| PlayerVO | Player voice |
| NPCVO | NPC dialogue |
| EnemyVO | Enemy vocalizations |

Allows independent control of:

- Dialogue volume
- Character voices
- Future localization voice packs

---

# Audio Effect Processing

## Available Effects

| Effect | Usage |
|-|-|
| Limiter | Prevent clipping |
| Compressor | Control dynamic range |
| LowPassFilter | Environmental filtering |
| Reverb | Space and atmosphere |
| Chorus | Movement and texture |

---

# Runtime Usage

Audio emitters should target the most specific bus available.

Example:

```gdscript
$AudioStreamPlayer.bus = "Player Weapon"
````

Instead of:

```gdscript
$AudioStreamPlayer.bus = "Sound"
```

Specific routing allows:

* Better mixing
* Individual volume controls
* Environmental effects
* Future audio settings support

---

# Future Integration

The Audio Bus Layout supports expansion into:

* AudioManager dynamic mixing
* Combat music transitions
* Weather intensity changes
* Region-based ambience
* Dialogue ducking
* Cinematic audio control
* Accessibility volume sliders

---

# Related Systems

* AudioManager
* AudioDefinition Resources
* Audio Database
* Audio Zone System
* Environment Audio System
* Dialogue Voice System
