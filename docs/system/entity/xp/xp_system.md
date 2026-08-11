# 📈 XP & Level Progression System

The XP System provides the centralized progression framework responsible for managing character experience, level advancement, stat growth, and progression state.

The system is designed around a **data-driven progression pipeline**, separating:

* XP calculation and routing
* Level curve definitions
* Level growth definitions
* Runtime progression state
* Save/load persistence

This allows classes, characters, and future progression systems to define their own growth paths without requiring hardcoded leveling logic.

---

# 🧠 System Architecture

The XP System is divided into three primary layers:

```
XPManager
    |
    ↓
PlayerLevelComponent
    |
    ├── LevelCurveResource
    |
    └── LevelGrowthData
```

---

# 📈 XPManager

`XPManager` acts as the central XP routing layer.

Responsibilities:

* Award XP
* Remove XP
* Validate XP requests
* Route XP into the owning level component
* Provide progression inspection tools

The manager does **not**:

* Calculate level formulas
* Handle stat growth
* Manage UI
* Own player progression state

Those responsibilities belong to the level component and progression databases.

---

## ✨ XP Award Flow

Example:

```
Combat / Quest / Event
        |
        ↓
XPManager.grant_xp()
        |
        ↓
PlayerLevelComponent.add_xp()
        |
        ↓
Level Curve Resolution
        |
        ↓
Level Up Processing
        |
        ↓
Stat Growth Application
```

---

# 🧬 PlayerLevelComponent

The `PlayerLevelComponent` is the runtime owner of player progression state.

Responsibilities:

* Store current XP
* Store current level
* Process XP gains
* Detect level increases
* Apply level growth
* Emit progression signals
* Provide save/load data

The component intentionally does **not**:

* Know about quests
* Know about combat
* Calculate XP curves
* Manage UI

It only manages progression state.

---

# 📦 Runtime Progression Data

Stored values:

```gdscript
current_level
current_total_xp
xp_multiplier
```

Supports future modifiers such as:

* Difficulty scaling
* Equipment bonuses
* Temporary buffs
* Global XP bonuses

---

# 💾 Save System Integration

XP progression is fully save compatible.

Saved data:

```gdscript
{
    "current_level": current_level,
    "current_total_xp": current_total_xp,
    "xp_multiplier": xp_multiplier
}
```

On loading:

```
SaveData
    |
    ↓
PlayerLevelComponent
    |
    ↓
Restore XP
    |
    ↓
Recalculate Level
```

---

# Current Implementation Summary

✅ Central XP routing manager
✅ Runtime player progression component
✅ Data-driven level curves
✅ Class-based XP progression
✅ Class-based stat growth
✅ Level-up event pipeline
✅ Quest integration
✅ Save/load persistence
✅ Future XP modifier support

---

# Future Expansion

Designed to support:

* Enemy XP rewards
* Quest XP scaling
* Difficulty-based XP modifiers
* Rested XP systems
* Experience bonuses
* Party XP sharing
* Alternative progression systems
* Prestige / Ascension mechanics

The XP System establishes a scalable RPG progression foundation where leveling, growth, and rewards remain data-driven instead of being tied directly to gameplay code.
