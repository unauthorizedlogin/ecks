# 💥 Destroyable System

## Overview

The Destroyable System provides a reusable framework for interactive world objects that can receive damage, react to hits, and be removed from the environment.

Destroyables are built as modular gameplay objects rather than one-off scripts, allowing creators to quickly create breakable props such as:

- Crates
- Barrels
- Environmental objects
- Destructible scenery
- Breakable containers
- Trap objects
- Interactive world hazards

The system separates object behavior, health management, damage handling, hit detection, and visual effects into reusable components.

This allows new destroyable objects to be created by combining existing resources and components rather than writing custom logic for every object.

---

# 🏗️ Destroyable Architecture

The system is divided into several reusable layers:

```

Destroyable Object
│
├── ResourceLife
│   └── Health / Death State Management
│
├── HitboxDestroyable
│   └── Receives Damage Events
│
├── DamageObject
│   └── Defines Damage Data
│
├── DamageArea
│   └── Applies Damage From Sources
│
└── ParticleEffect
└── Visual Feedback

```

Each component has a dedicated responsibility, allowing creators to mix and match functionality.

---

# 💥 Destroyable Objects

Destroyable objects inherit from the base destroyable framework and provide:

- Health tracking
- Damage reception
- Knockback reactions
- Hit feedback
- Particle effects
- Death handling
- Automatic cleanup

Objects are designed to be reusable world assets that can be placed throughout maps without additional scripting.

Examples:

- Breakable crates
- Explosive barrels
- Environmental debris
- Dungeon props
- Interactive scenery

---

# ❤️ ResourceLife

## Data-Driven Health Resource

Destroyables use a reusable `ResourceLife` resource to define their durability.

The resource controls:

- Maximum health
- Current health
- Damage handling
- Healing
- Death state
- Revive support

The same life resource pattern can be reused across different gameplay objects.

Features include:

- Damage signals
- Healing signals
- Death events
- Revival support
- Configurable damage behavior

This keeps health logic independent from individual objects.

---

# ⚔️ DamageObject

## Damage Data Resource

Damage is handled through a dedicated data resource rather than hardcoded values.

DamageObject defines:

- Damage amount
- Push / knockback force

This allows creators to create reusable damage profiles.

Examples:

- Small impact damage
- Explosion damage
- Trap damage
- Environmental hazards

Damage sources can share the same resource definitions across the game.

---

# 🛡️ Hitbox System

## HitboxDestroyable

Destroyable objects use a dedicated hitbox component to receive damage.

Responsibilities:

- Detect incoming damage
- Validate damage data
- Emit damage events
- Connect damage sources to objects

The hitbox layer keeps damage detection separate from object behavior.

This allows the same damage system to interact with:

- Players
- Enemies
- Destroyables
- Environmental objects

without requiring unique implementations.

---

# ☠️ Damage Area System

## DamageArea

Damage areas provide reusable damage sources.

Common uses:

- Explosive objects
- Environmental hazards
- Area attacks
- Trap zones

Features:

- Collision filtering
- Team validation
- Damage resource support
- Area-based damage application

Damage areas can prevent unwanted interactions through team checks.

---

# ✨ Particle Effect System

Destroyables support configurable GPU particle effects through reusable resources.

Particle resources define:

- Texture
- Animation frames
- Particle material settings

Creators can create different visual effects without modifying scripts.

Examples:

- Wood fragments
- Metal sparks
- Stone debris
- Magical effects

Particle resources update dynamically in the editor through resource change signals.

---

# 🎯 Combat Integration

Destroyables integrate directly with the damage pipeline.

Damage flow:

```

Damage Source
|
v
DamageArea
|
v
HitboxDestroyable
|
v
Destroyable Object
|
v
ResourceLife
|
v
Death Event

```

This allows destroyables to naturally participate in the same combat ecosystem as other gameplay objects.

---

# 🧩 Creator Workflow

Creating a destroyable object requires:

1. Create an object using the destroyable base scene
2. Assign a ResourceLife
3. Configure hitbox settings
4. Assign optional particle effects
5. Configure damage interactions

No additional scripting is required for standard breakable objects.

---

# 🔧 Features

- Fully component-based design
- Data-driven health management
- Reusable damage resources
- Shared damage pipeline
- Knockback support
- Hit flash effects
- Shake reactions
- GPU particle support
- Team-based damage filtering
- Editor-friendly resources
- Runtime cleanup handling

---

# 🎮 Example Uses

### Breakable Props

- Crates
- Barrels
- Furniture
- Dungeon objects

### Environmental Gameplay

- Explosive containers
- Destructible walls
- Hazard objects
- Interactive scenery

### Combat Objects

- Trap targets
- Damageable environmental objects
- World combat interactions

---

# Summary

The Destroyable System provides a reusable foundation for breakable and interactive world objects.

By separating health, damage, hit detection, and effects into modular components, creators can rapidly build large numbers of destructible objects while keeping gameplay behavior consistent across the entire framework.
