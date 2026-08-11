# 📈 Level Component Architecture

## Overview

The Level Component provides the runtime progression state for an entity's level and XP.

It acts as the **runtime-owned progression container**, storing the entity's current XP, level, and progression modifiers while delegating progression calculations to the Class and XP systems.

The component is responsible for:

* XP state
* Level state
* XP gain and removal
* Level-up detection
* Level growth events
* XP persistence
* Progression queries
* Progression signals

The component does **not** define XP curves or growth rules.

---

### 🧱 Runtime Progression State

The Level Component stores the current progression state:

```text
LevelComponent
│
├── Class ID
├── Current Level
├── Total XP
├── XP Multiplier
└── Loaded State
```

The `class_id` determines which progression data is used.

---

### 📈 XP Processing

XP can be added or removed through the component.

```gdscript
add_xp(amount)
remove_xp(amount)
```

XP gain is modified by the component's `xp_multiplier` before being added to the total XP.

```text
Base XP
   │
   ↓
XP Multiplier
   │
   ↓
Final XP
   │
   ↓
Total XP
```

The component then resolves the resulting level through the class's level curve.

---

### 📊 Level Resolution

The Level Component does not calculate progression formulas itself.

Instead, it queries the `LevelCurveResource` associated with the class.

```text
LevelComponent
      │
      ↓
ClassDatabase
      │
      ↓
LevelCurveResource
      │
      ↓
Resolve Level
```

This keeps progression rules separate from runtime progression state.

---

### 🆙 Level-Up Processing

When total XP reaches a new level, the component:

* Updates the current level
* Emits the `level_up` signal
* Resolves accumulated level growth
* Emits `level_growth_ready`
* Notifies the Quest System
* Emits a max-level signal when appropriate

```text
XP Added
   │
   ↓
Resolve Level
   │
   ↓
Level Increased
   │
   ├── level_up
   ├── Resolve Growth
   └── Quest Notification
```

Multiple levels can be processed when a single XP award crosses several level thresholds.

---

### 🌱 Level Growth

Level growth is resolved through the class's `LevelGrowthData`.

The component collects growth values for each level crossed and emits the resulting growth dictionary.

```text
Level Change
     │
     ↓
ClassDatabase
     │
     ↓
LevelGrowthData
     │
     ↓
Resolve Growth
     │
     ↓
level_growth_ready
```

The Level Component does not directly modify character stats.

---

### 📊 Progression Queries

The component provides runtime helpers for progression UI and other systems.

Available queries include:

```gdscript
get_current_level()
get_xp_into_current_level()
get_xp_required_for_next_level()
get_progress_ratio()
is_max_level()
```

These provide normalized progression information without requiring other systems to directly access the level curve.

---

### 💾 Save & Load

The Level Component exposes its progression state through:

```gdscript
get_save_data()
load_save_data(data)
```

Saved state includes:

* Current level
* Total XP
* XP multiplier

```text
LevelComponent
      │
      ↓
get_save_data()
      │
      ↓
Save System
      │
      ↓
load_save_data()
      │
      ↓
Restore Progression
```

---

### 📡 Progression Signals

The component exposes signals for progression events:

```text
xp_gained
xp_loaded
level_up
level_growth_ready
max_level_reached
```

These allow other systems to react to progression without being embedded inside the Level Component.

---

### 🔗 System Integration

The Level Component integrates with:

* Class Database
* Level Curve System
* Level Growth System
* XP Manager
* Quest System
* Save System
* Event Viewer

The component owns the **current progression state**, while the supporting systems provide progression definitions, XP routing, persistence, and gameplay responses.

---

### 🎯 Responsibility Boundary

The Level Component:

✅ Stores current XP
✅ Stores current level
✅ Applies XP multipliers
✅ Processes XP gain and removal
✅ Resolves level changes through the class curve
✅ Emits progression signals
✅ Resolves accumulated level growth
✅ Provides progression queries
✅ Provides save/load state

The Level Component does **not**:

❌ Define XP curves
❌ Define level growth rules
❌ Calculate progression formulas
❌ Manage quests
❌ Manage UI
❌ Perform combat logic
❌ Directly modify character stats

Those responsibilities remain with the appropriate progression and gameplay systems.
