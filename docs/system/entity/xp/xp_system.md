# 📈 XP & Level Progression System

The XP System provides the centralized framework for character experience, leveling, progression state, and level-based growth.

The system separates XP routing, runtime progression, and progression data so different classes and characters can use data-driven leveling paths.

The system is responsible for:

* XP awarding and removal
* Level progression
* Level-based stat growth
* XP modifiers
* Progression state
* Save/load persistence

---

# 🧠 XP Architecture

```text
                    XP System

                         |
                    XPManager
                         |
                         ↓
               PlayerLevelComponent
                         |
             -------------------------
             |                       |
        Level Curve             Level Growth
          Resource                  Data
```

`XPManager` routes experience into the appropriate progression component, while `PlayerLevelComponent` owns the character's runtime progression state.

Level curves and growth data define how progression is calculated.

---

# 🔗 XP System Documentation

| Component                 | Purpose                                                               | Documentation                                       |
| ------------------------- | --------------------------------------------------------------------- | --------------------------------------------------- |
| 📈 XP Manager             | Central XP routing, awarding, and progression access                  | [XP Manager](xp_manager.md)                         |
| 🧍 Player Level Component | Runtime XP, level state, level-up processing, and progression signals | [Player Level Component](player_level_component.md) |
| 📊 Level Curve            | Defines XP requirements and level progression                         | [Level Curve](level_curve.md)              |
| 📈 Level Growth           | Defines stat growth associated with progression                       | [Level Growth](level_growth.md)            |

---

# 🔗 System Integration

The XP System integrates with:

* Character System
* Class System
* Stats System
* Quest System
* Combat System
* Save System
* Difficulty System

Gameplay systems provide XP to the XP System, while the progression system handles the resulting level and growth changes.

---

# 💾 Save Integration

Runtime progression state is persisted through the Save System, including:

* Current level
* Total XP
* XP modifiers

Progression state is restored when the character is loaded.

---

# 📌 Summary

The XP System provides the framework's centralized experience and level progression pipeline.

It separates **XP routing, runtime progression state, and progression data**, allowing leveling and character growth to remain reusable and data-driven across the framework.
