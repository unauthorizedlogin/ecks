# ⚔️ Combat System

The Combat System provides the centralized framework for resolving combat interactions between gameplay entities.

Combat requests are processed through a shared pipeline that handles validation, damage resolution, damage application, and combat events.

The system is responsible for:

* Combat request processing
* Target and team validation
* Damage calculation and mitigation
* Critical hit resolution
* Damage application
* Combat results and events
* Death and kill tracking
* Combat effect integration

---

# 🧠 Combat Architecture

```text
                    Combat System

                         |
                  CombatManager
                         |
                -------------------
                |                 |
         CombatResolver      Combat Data
                |           Structures
                ↓                 |
          DamageResult            |
                |                 |
        ---------------------------
        |            |            |
   VitalComponent  Effects    Combat Events
```

The `CombatManager` coordinates the combat pipeline, while `CombatResolver` handles combat calculations and specialized components manage runtime entity state.

---

# 🔗 Combat System Documentation

| Component          | Purpose                                                                         | Documentation                         |
| ------------------ | ------------------------------------------------------------------------------- | ------------------------------------- |
| ⚔️ Combat Manager  | Central combat routing, request processing, and event coordination              | [Combat Manager](combat_manager.md)   |
| 🧮 Combat Resolver | Combat validation, damage calculation, criticals, and mitigation                | [Combat Resolver](combat_resolver.md) |
| 📦 Combat Data     | Combat requests, contexts, and results used throughout the pipeline             | [Combat Data](combat_data.md)         |
| ❤️ Vital Component | Runtime health, resources, damage application, healing, death, and regeneration | [Vital Component](vital_component.md) |
| 🛡️ Damage Teams   | Defines combat relationships between entity teams                               | [Damage Teams](resource_damage_team.md) |

---

# 🔗 System Integration

The Combat System integrates with:

* Character System
* Stats System
* Ability System
* Effect System
* Equipment System
* Quest System
* Event System
* Game State Systems

Combat handles the **resolution of combat interactions**, while connected systems own their respective gameplay state and presentation.

---

# 📌 Summary

The Combat System provides a centralized and event-driven combat pipeline.

Combat requests are validated and resolved through shared combat logic, with dedicated components handling entity state, combat relationships, and combat results.

This keeps combat behavior consistent across players, enemies, abilities, equipment, and other gameplay sources while allowing each system to remain independently responsible for its own domain.
