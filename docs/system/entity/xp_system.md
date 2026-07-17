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

# 🎯 Level Curve System

`LevelCurveResource` defines the XP requirements for a progression path.

Level curves are:

* Data-driven
* Resource based
* Generated through external pipelines
* Runtime safe

The curve stores:

```gdscript
total_xp_by_level
xp_to_next_by_level
max_level
```

Example:

```
Level 1
0 XP

Level 2
100 XP

Level 3
350 XP

Level 4
800 XP
```

---

# 🔎 Level Resolution

The system can resolve a level directly from total XP:

```gdscript
get_level_from_total_xp(total_xp)
```

Example:

```
Total XP: 420

↓

Level Curve

↓

Player Level: 3
```

This allows save files to restore progression reliably.

---

# 📊 Progress Tracking

The XP system provides progression helpers:

### Current Level Progress

```gdscript
get_progress_ratio()
```

Returns:

```
0.0 → 1.0
```

Used for:

* XP bars
* HUD displays
* Progression menus

---

### XP Into Current Level

```gdscript
get_xp_into_current_level()
```

Returns the XP earned within the current level range.

---

### XP Required

```gdscript
get_xp_required_for_next_level()
```

Returns the remaining XP needed to advance.

---

# 📈 Level Growth System

Level progression is separated from XP curves through `LevelGrowthData`.

This allows each class to have unique progression paths.

Example:

```gdscript
Level 5 Warrior Growth

Vital +20
Strength +5
Defense +3
```

---

## Level Growth Flow

```
Level Up
   |
   ↓
LevelGrowthData Lookup
   |
   ↓
Stat Growth Dictionary
   |
   ↓
StatBlock Update
   |
   ↓
Runtime Stat Rebuild
```

---

# 🧬 Class-Based Progression

The XP system supports class-specific progression.

Each class can define:

* Unique XP curves
* Unique max levels
* Unique stat growth
* Unique progression identity

Example:

```
Warrior
 ├── Warrior XP Curve
 └── Warrior Growth Data


Mage
 ├── Mage XP Curve
 └── Mage Growth Data
```

---

# 🔔 Progression Signals

The system exposes progression events:

### XP Gained

```gdscript
xp_gained(amount)
```

Used by:

* HUD updates
* Event logging
* Future rewards

---

### Level Up

```gdscript
level_up(old_level, new_level)
```

Used by:

* Progression systems
* Quest objectives
* UI updates

---

### Level Growth Ready

```gdscript
level_growth_ready(growth)
```

Used to apply stat increases.

---

### Max Level Reached

```gdscript
max_level_reached()
```

Prevents progression beyond the configured cap.

---

# 📜 Quest Integration

The XP system communicates level milestones to the Quest System.

Supports objectives such as:

* Reach level X
* Level progression milestones

Example:

```gdscript
ObjectiveType.LEVEL_UP
```

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
