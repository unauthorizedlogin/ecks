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

