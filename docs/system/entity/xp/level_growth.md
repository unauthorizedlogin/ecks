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
