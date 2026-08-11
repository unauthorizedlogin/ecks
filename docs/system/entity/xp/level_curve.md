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
