# 📈 XP Manager

## Overview

The **XP Manager** is the centralized experience-point routing layer within the framework.

It provides a single runtime authority for awarding and removing XP while applying character-level XP modifiers before the resulting amount is passed to the target's `LevelComponent`.

The manager separates **XP distribution** from **level progression**.

The XP Manager determines **how much XP is granted or removed**.

The `LevelComponent` remains responsible for **storing XP and processing level progression**.

XP progression configuration is provided through:

* 📈 `LevelCurveResource`
* 🌱 `LevelGrowthData`
* 🧍 Player `XPGain` stat

---

# 🧠 Core Responsibilities

The XP Manager provides:

* XP manager initialization
* XP award routing
* XP gain modifier application
* XP removal routing
* Maximum-level protection
* XP award signals
* XP removal signals
* Level component initialization
* Debug XP inspection

The manager does not directly manage:

* Current XP storage
* Total XP storage
* Level advancement
* Level-up calculations
* Level growth application
* XP curve calculations

Those responsibilities remain within `LevelComponent` and the progression resources it uses.

---

# ⚙️ Initialization

```gdscript
func initialize()
```

Registers the XP Manager as an active framework system.

Prevents duplicate initialization.

Output:

```text
📈 XPManager initialized
```

---

# 📚 Progression Resources

The XP Manager exposes the progression resources used when binding a player's level component.

```gdscript
@export var level_curve: LevelCurveResource
@export var level_growth: LevelGrowthData
```

These resources define the progression configuration used by `LevelComponent`.

```text
LevelCurveResource
        +
LevelGrowthData
        ↓
   XPManager
        ↓
LevelComponent
```

The XP Manager does not calculate the level curve itself.

---

# 📈 XP Grant

```gdscript
func grant_xp(
    target_component: LevelComponent,
    amount: int
)
```

Routes an XP award to the specified `LevelComponent`.

The grant pipeline:

```text
Requested XP
     ↓
Validate Target
     ↓
Validate Amount
     ↓
Check Maximum Level
     ↓
Apply XPGain Modifier
     ↓
LevelComponent.add_xp()
     ↓
xp_awarded Signal
```

This provides a centralized point for XP modifiers to affect gameplay XP.

---

# 🧮 XP Gain Modifier

```gdscript
func _apply_xp_gain(amount: int) -> int
```

The XP Manager checks the active player's `XPGain` stat when awarding XP.

The modifier is treated as a percentage.

```text
Final XP
=
Base XP × (1 + XPGain / 100)
```

Example:

```text
Base XP:      100
XPGain:        50%
Final XP:     150
```

A value of:

```text
XPGain = 0
```

means no XP modification.

This allows XP bonuses to participate in the standard stat system rather than requiring separate XP multiplier logic throughout gameplay systems.

---

# 🧍 Player XP Integration

The XP Manager retrieves the active player through `PlayerManager`.

```gdscript
var current_player := PlayerManager.get_player()
```

The player's `XPGain` stat is then used to modify the requested XP amount.

```text
PlayerManager
      ↓
Player
      ↓
StatBlock
      ↓
XPGain
      ↓
XPManager
      ↓
Modified XP
```

If no player is available, or the player does not provide an `XPGain` value, the original XP amount is used.

This allows XP awards to remain functional for non-player or context-independent targets.

---

# 🛡️ Maximum-Level Protection

XP awards are ignored when the target `LevelComponent` has already reached its maximum level.

```gdscript
if target_component.is_max_level():
    return
```

This prevents unnecessary XP processing after progression has reached its configured limit.

---

# 📉 XP Removal

```gdscript
func remove_xp(
    target_component: LevelComponent,
    amount: int
)
```

Routes XP removal directly to the target's `LevelComponent`.

Unlike XP gains, XP removal does not apply the player's `XPGain` modifier.

```text
Requested XP Loss
        ↓
Validate Target
        ↓
Validate Amount
        ↓
LevelComponent.remove_xp()
        ↓
xp_removed Signal
```

XP loss is also reported through the Event Viewer.

---

# 🔔 XP Signals

The XP Manager provides two signals for systems that need to observe XP changes.

### XP Awarded

```gdscript
signal xp_awarded(target: Node, amount: int)
```

Emitted after XP has successfully been awarded.

### XP Removed

```gdscript
signal xp_removed(target: Node, amount: int)
```

Emitted after XP has successfully been removed.

This allows UI, progression tracking, analytics, achievements, or other systems to observe XP changes without coupling themselves to the XP implementation.

```text
XPManager
    |
    ├── xp_awarded
    |
    └── xp_removed
```

---

# 🧠 Level Component Binding

```gdscript
func bind_player_level_component(player: Node)
```

Binds the player's `LevelComponent` to the progression resources managed by the XP Manager.

```gdscript
comp.initialize(
    level_curve,
    level_growth
)
```

This establishes the progression configuration used by the player's level component.

```text
XPManager
   |
   ├── LevelCurveResource
   └── LevelGrowthData
            ↓
      LevelComponent
```

The Level Component then owns the runtime progression state.

---

# 📊 Debug / Inspection API

The XP Manager provides lightweight inspection functions for runtime debugging.

```gdscript
func debug_get_level(
    target_component: LevelComponent
) -> int
```

Returns the target's current level.

```gdscript
func debug_get_xp(
    target_component: LevelComponent
) -> int
```

Returns the target's stored total XP.

These functions provide read-only inspection without exposing progression logic through the manager.

---

# 🔗 System Relationships

```text
                    XPManager
                        |
          ┌─────────────┼─────────────┐
          ↓             ↓             ↓
 LevelCurveResource  LevelGrowthData  XPGain
          |             |             |
          └─────────────┼─────────────┘
                        ↓
                 LevelComponent
                        |
              ┌─────────┴─────────┐
              ↓                   ↓
          XP Storage          Level Progression
```

During gameplay:

```text
Gameplay System
      ↓
  XPManager
      ↓
XPGain Modifier
      ↓
LevelComponent
      ↓
XP / Level State
```

---

# 🧭 System Boundaries

| Responsibility       | System                     |
| -------------------- | -------------------------- |
| XP routing           | XP Manager                 |
| XP gain modifiers    | XP Manager + `XPGain` stat |
| XP storage           | `LevelComponent`           |
| Total XP state       | `LevelComponent`           |
| Current level        | `LevelComponent`           |
| Level curve          | `LevelCurveResource`       |
| Level growth         | `LevelGrowthData`          |
| Player lookup        | Player Manager             |
| XP event observation | XP Manager signals         |
| XP display/logging   | UI / Event Manager         |

The XP Manager should remain the routing layer rather than becoming the owner of progression state.

---

# 🌱 Data-Driven XP Architecture

The XP system separates progression configuration, runtime state, and XP routing.

```text
Progression Data
 ├── LevelCurveResource
 └── LevelGrowthData
          ↓
      XPManager
          ↓
   LevelComponent
          ↓
   Runtime XP State
```

XP modifiers are integrated through the existing stat architecture:

```text
Player StatBlock
      ↓
    XPGain
      ↓
   XPManager
      ↓
 Modified XP Award
```

This allows difficulty systems, equipment, effects, and other stat sources to modify XP gain through the normal stat pipeline.

---

# 🚀 Launch Flow Integration

The XP Manager is initialized as part of the framework's centralized Launch Flow.

Player progression resources are bound to the player's `LevelComponent` as part of player initialization.

```text
Launch Flow
    ↓
XPManager
    ↓
Progression Resources
    ↓
PlayerManager
    ↓
LevelComponent
    ↓
Gameplay XP Awards
```

This establishes XP progression as framework infrastructure rather than requiring individual gameplay systems to implement their own XP calculations.

---

# 🔮 Future Expansion

The XP Manager provides a centralized extension point for future progression features.

Potential future integrations include:

* XP multipliers
* XP penalties
* XP bonuses from difficulty
* Rested XP
* XP modifiers from effects
* XP modifiers from equipment
* XP source categorization
* XP tracking and analytics
* Alternative progression curves
* Multiple progression tracks

These systems can extend the XP routing layer without requiring individual gameplay systems to directly manipulate the player's level state.

---

# ✅ Design Rule

**XPManager is the centralized authority for routing XP into and out of `LevelComponent`.**

`LevelCurveResource` and `LevelGrowthData` define **how progression is configured**.

`StatBlock` defines **how XP gain can be modified**.

`XPManager` determines **how much XP is ultimately awarded or removed**.

`LevelComponent` owns **the resulting XP state and level progression**.

Gameplay systems should route XP through `XPManager` rather than directly modifying the player's progression state.
