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

