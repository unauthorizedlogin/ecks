# ⚡ Ability System

The Ability System provides the centralized framework for defining, granting, validating, and executing gameplay abilities.

Abilities are data-driven and can be provided through classes or other gameplay sources such as equipment. The system separates ability definitions from runtime execution while the Ability Manager coordinates activation and state.

The system is responsible for:

* Ability definitions and configuration
* Ability ownership and granting
* Ability validation
* Ability casting and activation
* Cooldowns
* Passive abilities
* Triggered abilities
* Auto-attacks
* Auras
* Runtime ability execution
* Ability lifecycle events

---

# 🧠 Ability Architecture

```text
                    Ability System

                         |
                  Ability Database
                         |
                         ↓
                Ability Definition
                         |
                         ↓
                  Ability Manager
                         |
                  ----------------
                  |              |
                  ↓              ↓
            AbilityRuntime   Validation
                  |
        -------------------------
        |          |            |
      Cast      Channel       Effects
```

The `AbilityManager` coordinates ability activation and ownership, while `AbilityRuntime` handles the execution lifecycle of an active ability.

---

# 🔗 Ability System Documentation

| Component            | Purpose                                                                                  | Documentation                               |
| -------------------- | ---------------------------------------------------------------------------------------- | ------------------------------------------- |
| ⚡ Ability Definition | Defines ability configuration, activation, targeting, categories, triggers, and behavior | [Ability Definition](ability_definition.md) |
| 🧠 Ability Manager   | Central ability ownership, validation, casting, cooldowns, and runtime coordination      | [Ability Manager](ability_manager.md)       |
| ▶️ Ability Runtime   | Executes active abilities and manages their runtime lifecycle                            | [Ability Runtime](ability_runtime.md)       |

---

# 🎯 Ability Activation

The system supports multiple activation paths:

* Manual casting
* Auto-attacks
* Triggered abilities
* Passive abilities
* Auras

Abilities can also be granted dynamically through sources such as equipment.

---

# 🔗 System Integration

The Ability System integrates with:

* Class System
* Character System
* Player System
* Equipment / Item System
* Effect System
* Requirements System
* Combat System
* Stats System

Classes provide abilities through their configuration, while other systems can grant or trigger abilities during gameplay.

---

# 📌 Summary

The Ability System provides a centralized, data-driven architecture for gameplay abilities.

`AbilityDefinition` describes the ability, `AbilityManager` controls access and activation, and `AbilityRuntime` handles execution, allowing abilities to support multiple activation methods while remaining independent from individual gameplay systems.
