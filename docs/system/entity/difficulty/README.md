# 🎚️ Difficulty System

The Difficulty System provides a centralized, data-driven framework for defining gameplay difficulty and applying difficulty-based modifiers.

Difficulty definitions provide effects that are processed through the existing Effect and Stat systems rather than directly modifying runtime statistics.

The system is responsible for:

* Difficulty definitions
* Player modifiers
* Enemy modifiers
* Difficulty-based stat scaling
* Effect System integration
* Future difficulty-based gameplay scaling

---

# 🧠 Difficulty Architecture

```text
                  Difficulty System

                         |
                Difficulty Definition
                         |
              -----------------------
              |                     |
        Player Effects        Enemy Effects
              |                     |
              └──────────┬──────────┘
                         ↓
                   Effect System
                         ↓
                    Stats System
```

Difficulty acts as a **configuration layer**, while the Effect and Stat systems perform the runtime modification.

---

# 🔗 Difficulty System Documentation

| Component                 | Purpose                                                               | Documentation                                     |
| ------------------------- | --------------------------------------------------------------------- | ------------------------------------------------- |
| 🎚️ Difficulty Definition | Defines difficulty identity and difficulty-based player/enemy effects | [Difficulty Definition](resource_difficulty.md) |

---

# 🔗 System Integration

The Difficulty System integrates with:

* Effect System
* Stats System
* Character System
* Player System
* Enemy System
* Combat System

Difficulty modifiers can affect any statistics supported by the existing `StatEffect` pipeline.

---

# 🚀 Current Implementation

* Data-driven difficulty definitions
* Player stat effects
* Enemy stat effects
* Runtime StatBlock modification
* Effect System integration

---

# 🔮 Future Expansion

Potential difficulty integrations include:

* Loot and reward scaling
* Economy modifiers
* Death penalties
* Enemy spawning
* Boss scaling
* Quest and progression scaling

---

# 📌 Summary

The Difficulty System provides an **effect-driven difficulty scaling layer**.

Difficulty defines the modifiers, while existing gameplay systems handle their application and runtime behavior. This keeps difficulty configuration data-driven and allows additional difficulty scaling to be added without introducing difficulty-specific logic throughout the framework.
