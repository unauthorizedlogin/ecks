# 🧬 Entity Systems

The **Entity Systems layer** provides the framework foundation for all gameplay entities within Ecks.

This layer contains the systems responsible for defining, controlling, and modifying any entity that exists in the game world.

Entity systems are not limited to the player.

They support:

- Player characters
- NPCs
- Enemies
- Creatures
- Future entity types

These systems define:

- Entity identity
- Classes and archetypes
- Stats and attributes
- Combat capabilities
- Effects and modifiers
- Difficulty scaling
- Experience progression
- AI behaviors

---

# 🧠 Entity Architecture

Entity systems provide the foundation for all actors within the framework.

Architecture:

```text
                    Entity Systems

                          |
        -----------------------------------------
        |          |          |          |
      Actor     Class      Stats     Effects
        |
        |
   -------------------------------
   |              |              |
 Combat       Difficulty       XP

        |
        ↓

    Behaviors

        |
        ↓

      NPCs
```

## 🐾 Behavior System

The **Behavior System** provides modular AI behavior components for entities within the Ecks Framework.

It provides reusable behavior modules that can be attached to NPCs, enemies, companions, and world entities without requiring entity-specific logic.

Features:

- Modular entity behavior components
- Follow and target tracking behaviors
- Patrol path navigation
- Area-based target detection
- Configurable Inspector-driven behavior rules
- Reusable AI expansion framework

Architecture:

```text
Entity

    |
    ↓

Behavior Components

    |
    +----------------+
    |                |
    ↓                ↓

Movement       Detection

    |
    ↓

Entity Response
```

Documentation:

- [Behavior System](docs/entity/behaviors.md)

---

## 🧍 Character Controllers

The **Character Controller System** provides the runtime entity control layer for all controllable and simulated characters within the Ecks Framework.

Controllers act as the bridge between entity data and gameplay systems by managing:

- Entity movement
- Runtime stat assignment
- Animation state
- Behavior integration
- Combat communication
- Interaction systems
- Progression systems

Features:

- Player controller support
- NPC character controllers
- Enemy controller foundations
- Modular movement handling
- Behavior-driven entity control
- Shared entity interfaces
- Component-based system communication

Architecture:

```text
Entity Data

      |
      ↓

Character Controller

      |
 ┌────┼────┬────┬────┐
 ↓    ↓    ↓    ↓    ↓

Stats Combat Effects Behaviors Interaction

      |
      ↓

 Runtime Entity
```

Documentation:

- [Character Control System](docs/entity/character_controllers.md)

---

## 🧬 Class System

The **Class System** provides the identity and rule framework for entities within the Ecks Framework.

It separates numerical character progression from gameplay restrictions by dividing class data into two dedicated layers:

- **StatBlock** → Class attributes, growth, combat values, and resources
- **ClassRuleResource** → Class behavior, restrictions, equipment rules, and identity

This allows classes to share stat structures while maintaining unique gameplay rules.

Features:

- Data-driven class definitions
- Independent stat and rule management
- Class-based equipment restrictions
- Weapon and armor permissions
- Starting equipment configuration
- Combat team assignment
- Class tags and identity rules
- Future subclass expansion support

Architecture:

```text
              Class Database

                    |
        ---------------------------
        |                         |
        ↓                         ↓

    StatBlock          ClassRuleResource

    Stats              Rules
    Growth             Equipment
    Resources          Restrictions
    Combat Values      Identity

        |
        ↓

    Entity Runtime
```

Documentation:

- [Class System](docs/entity/class_system.md)

---

## ⚔️ Combat System

The **Combat System** provides the centralized combat authority for all entity interactions within the Ecks Framework.

All combat requests flow through a unified pipeline where damage is validated, resolved, applied, and broadcast to connected systems.

The system separates combat calculations from entity state, allowing players, NPCs, enemies, equipment, and abilities to share the same combat foundation.

Features:

- Centralized damage processing
- Combat request and result pipeline
- Damage validation
- Critical hit calculations
- Defensive mitigation
- Combat team relationships
- Vital state management
- Effect and equipment integration
- Quest kill tracking
- Combat event broadcasting

Architecture:

```text
              Combat Request

                    |
                    ↓

             Combat Manager

                    |
                    ↓

            Combat Resolver

                    |
                    ↓

             Damage Result

                    |
        -----------------------
        |                     |
        ↓                     ↓

 VitalComponent        Combat Events

        |
        ↓

 Entity State
```

Documentation:

- [Combat System](docs/entity/combat_system.md)

---

## 🎚️ Difficulty System

The **Difficulty System** provides a centralized, data-driven framework for scaling gameplay challenge across the Ecks Framework.

Difficulty does not directly modify gameplay systems. Instead, it uses the **Effect System** as the authoritative modification pipeline, allowing difficulty settings to apply the same stat-based changes used by equipment, buffs, debuffs, and other runtime effects.

Features:

- Data-driven difficulty definitions
- Player stat modifiers
- Enemy stat modifiers
- Effect System integration
- Future reward scaling
- Future economy scaling
- Future spawn scaling
- Future boss scaling
- Future progression adjustments

Status:

⚠️ Currently under development.

Implemented:

Difficulty resource structure
Player and enemy effect definitions
Effect System integration path

In Progress:

Runtime difficulty application
Entity stat scaling
Expanded reward and economy modifiers

Architecture:

```text
            Difficulty Definition

                    |
                    ↓

             Stat Effects

                    |
                    ↓

             Effect System

                    |
                    ↓

          Runtime Entity Stats

                    |
        -------------------------
        |                       |
        ↓                       ↓

      Combat              Progression
```

Documentation:

- [Difficulty System](docs/entity/difficulty_system.md)

---

## ✨ Effect System

The **Effect System** provides the universal gameplay modification pipeline for the Ecks Framework.

All gameplay modifiers are represented as data-driven `StatEffect` resources and processed through the centralized `EffectManager`.

This allows every system that modifies gameplay state to share the same pipeline:

- Equipment bonuses
- Consumable effects
- Buffs and debuffs
- Damage over Time
- Healing over Time
- Revival effects
- Class passives
- Difficulty scaling
- World modifiers

Architecture:

```text
Effect Source

      |
      ↓

 StatEffect Resource

      |
      ↓

 EffectManager

      |
      ↓

+-------------+-------------+
|             |             |
↓             ↓             ↓

StatManager CombatManager VitalComponent

      |
      ↓

Runtime Gameplay Changes
```

Documentation:

- [Effect System](docs/entity/effect_system.md)

---

## 👤 NPC System

The **NPC System** provides the centralized identity and runtime data layer for all non-player characters within the Ecks Framework.

NPCs are defined through data-driven `NPCData` resources and accessed through the `NPCDatabase`, allowing multiple gameplay systems to reference NPCs without depending on scene instances.

Current integrations include:

- Dialogue systems
- Quest objectives
- Shop interactions
- World interactions
- Future reputation and faction systems

Architecture:

```text
NPC Scene

      |
      ↓

    npc_id

      |
      ↓

 NPCDatabase

      |
      ↓

 NPCData Resource

      |
      +-------------+-------------+
      |             |             |
      ↓             ↓             ↓

 Dialogue       Quests        Shops
```

Documentation:

- [NPC System](docs/entity/npc_system.md)

---

## 📊 Stats System

The **Stats System** provides the centralized character attribute and calculation framework for the Ecks Framework.

It separates raw stat data, runtime calculations, and modifier sources through a layered architecture built around `StatBlock` and `StatManager`.

The system supports:

- Base attributes
- Derived statistics
- Equipment modifiers
- Effect modifiers
- Level scaling
- Formula-driven calculations
- Combat ratings
- Resistance calculations
- Runtime stat rebuilding

Architecture:

```text
Class / Entity Data

        |
        ↓

    StatBlock

        |
        ↓

   StatManager

        |
        +----------------+
        |                |
        ↓                ↓

Equipment Layers    Effect Layers

        |
        ↓

Derived Calculations

        |
        ↓

Runtime Entity Stats

        |
        ↓

Combat / UI / Gameplay
```

Documentation:

- [Stats System](docs/entity/stats_system.md)

---

## 📈 XP & Level Progression System

The XP System provides the centralized progression framework for character experience, leveling, and stat growth.

It separates progression responsibilities into dedicated systems:

- `XPManager` → XP routing authority
- `PlayerLevelComponent` → Runtime progression state
- `LevelCurveResource` → XP requirements and level thresholds
- `LevelGrowthData` → Class-based stat growth

Features:

- Data-driven XP progression
- Level curve resources
- Class-specific growth paths
- Runtime level tracking
- Level-up event pipeline
- Stat growth integration
- Quest progression support
- Save/load persistence
- Future XP modifier support

Architecture:

```text
Gameplay Events

      |
      ↓

 XPManager

      |
      ↓

PlayerLevelComponent

      |
      +----------------+
      |                |
      ↓                ↓

Level Curve      Level Growth

      |
      ↓

 Runtime Stats
```

Documentation:

- [XP System](docs/entity/xp_system.md)

---
