# 🧩 Entity Systems

The Entity Systems layer provides the foundation for all gameplay entities within the framework.

These systems define, control, and modify every runtime entity, including players, NPCs, enemies, creatures, and future entity types.

The layer is responsible for:

- Entity identity
- Classes and archetypes
- Statistics and progression
- Combat
- Effects and modifiers
- Difficulty scaling
- AI behaviors
- Runtime controllers

---

# 🧠 Entity Architecture

```text
                    Entity Systems

                          |
    ---------------------------------------------------
    |        |         |        |         |           |
 Character  Class    Stats   Combat   Ability     NPC
Controller

                          |
                          ↓

                    Effect System

                  (Shared Modifier Pipeline)

                          |
          --------------------------------------
          |          |         |               |
      Abilities   Items   Difficulty   Gameplay Systems

                          |
                          ↓

                    XP / Behaviors
```

---

# 🔗 Entity System Documentation

The following systems make up the Entity layer.

| System | Purpose | Documentation |
|---|---|---|
| ⚡ Ability System | Defines and manages gameplay abilities and their runtime execution | [Ability System](docs/entity/ability_system.md) |
| 🐾 Behavior System | Modular AI behaviors and reusable entity logic | [Behavior System](docs/entity/behaviors.md) |
| 🧍 Character Controller System | Runtime control layer for player, NPC, and enemy entities | [Character Controller System](docs/entity/character_controllers.md) |
| 🧬 Class System | Entity classes, archetypes, and gameplay rules | [Class System](docs/entity/class_system.md) |
| ⚔️ Combat System | Centralized combat processing and damage resolution | [Combat System](docs/entity/combat_system.md) |
| 🎚️ Difficulty System | Data-driven gameplay difficulty scaling | [Difficulty System](docs/entity/difficulty_system.md) |
| ✨ Effect System | Gameplay modifiers, buffs, debuffs, and stat effects | [Effect System](docs/entity/effect_system.md) |
| 👤 NPC System | NPC identities and runtime references | [NPC System](docs/entity/npc_system.md) |
| 🧍 Player Manager | Player lifecycle management, bootstrap flow, runtime references, and state handling | [Player Manager](docs/entity/player_manager.md) |
| 📊 Stats System | Runtime statistics, calculations, and modifiers | [Stats System](docs/entity/stats_system.md) |
| 📈 XP System | Experience, leveling, and character progression | [XP System](docs/entity/xp_system.md) |

---

# Summary

The Entity Systems layer provides a unified foundation for gameplay entities through shared data-driven architecture and modular runtime systems.

Each subsystem specializes in a specific aspect of entity management while integrating through common databases, managers, and gameplay pipelines.
