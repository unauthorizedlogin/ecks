# ✨ Effect System

The Effect System provides the centralized framework for defining, applying, and managing gameplay effects.

Effects are data-driven through `StatEffect` resources and processed through `EffectManager`, allowing combat, equipment, abilities, consumables, difficulty, and other gameplay systems to use the same effect pipeline.

The system provides:

* Effect definitions
* Effect application
* Stat modifiers and overrides
* Temporary and persistent effects
* Damage and healing over time
* Healing and revival
* Buffs and debuffs
* Auras and passives
* Effect costs and cleanup

---

# 🧠 Effect Architecture

```text
                    Effect System

                          |
                 ┌────────┴────────┐
                 ↓                 ↓
             StatEffect       EffectManager
             Resources            |
                                   ↓
                         ┌─────────┼─────────┐
                         ↓         ↓         ↓
                    StatManager  Combat   VitalComponent
                         |         |         |
                    Modifiers   Damage   Heal / Revive
```

`StatEffect` defines **what an effect does**.

`EffectManager` controls **how effects are applied and managed**.

Other systems provide effects and consume their results.

---

# 🔗 Effect System Documentation

The following documents cover the major parts of the Effect System.

| System                 | Purpose                                                                                               | Documentation                                 |
| ---------------------- | ----------------------------------------------------------------------------------------------------- | --------------------------------------------- |
| ✨ Effect Manager       | Central runtime authority for applying, tracking, and removing effects                                | [Effect Manager](effect_manager.md)           |
| 📦 StatEffect Resource | Defines the data and configuration for gameplay effects                                               | [StatEffect Resource](resource_stateffect.md) |
| 🧩 Stack Handler      | The Effect Stack Handler manages **stacking and duration behavior** for active `StatEffect` instances | [Effect Stacks](effect_stack_handler.md)       |
| 🎯 Effect Triggers     | Defines when effects activate, including immediate, equip, hit, crit, over-time, and passive triggers | [Effect Triggers](effect_triggers.md)         |
| 🔄 Effect Pipeline     | Defines how effects move from their source through runtime application and resolution                 | [Effect Pipeline](effect_pipeline.md)         |
| ⏱️ Effect Lifecycle    | Covers active effects, duration, ticking, expiration, removal, and cleanup                            | [Effect Lifecycle](effect_lifecycle.md)       |

---

# 🔗 System Integration

The Effect System integrates with:

* ⚔️ Combat System
* 📊 Stats System
* ❤️ Vital Systems
* ⚡ Ability System
* 🧍 Character Systems
* 🛡️ Equipment System
* 🧪 Consumable System
* 🎚️ Difficulty System
* 🌍 World Systems

These systems provide or consume effects while `EffectManager` remains the central runtime authority.

---

# 📌 Design Rule

**`StatEffect` defines the effect. `EffectManager` owns the effect pipeline.**

Gameplay systems should not directly modify stats, apply persistent modifiers, or implement their own effect lifecycle outside of this architecture.

The Effect System provides the shared modification layer used throughout the framework.
