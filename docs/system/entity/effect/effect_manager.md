# ✨ Effect Manager

## Overview

The **Effect Manager** is the central runtime authority for applying, maintaining, ticking, stacking, and removing `StatEffect` instances.

It translates effect definitions into runtime behavior and coordinates with the systems responsible for vitals, stats, combat, and effect stacking.

The manager supports both **instant effects** and **persistent active effects**.

This includes:

* ❤️ Healing
* ⚔️ Damage
* 💀 Revive
* 💨 Knockback
* 📊 Stat modification
* 🔒 Stat overrides
* 🔥 Damage over time
* 💚 Healing over time
* 📚 Effect stacking
* ⏱️ Effect duration
* 💰 Effect costs
* 🧹 Effect cleanup

The Effect Manager does not define effect content itself.

Effect behavior is driven by `StatEffect` resources.

---

# 🧠 Core Responsibilities

The Effect Manager provides:

* Effect initialization
* Target resolution
* Effect trigger routing
* Effect cost processing
* Instant effect execution
* Active effect registration
* Effect stacking and duration handling
* StatManager integration
* DamageManager/CombatManager integration
* DOT/HOT ticking
* Effect removal
* Effect cleanup

The manager acts as the runtime bridge between **effect data** and the systems that execute the resulting behavior.

---

# ⚙️ Initialization

```gdscript
func initialize()
```

Registers the Effect Manager as an active framework system.

Prevents duplicate initialization.

Output:

```text
✨ EffectManager initialized
```

---

# 🎯 Target Resolution

The Effect Manager accepts multiple target representations through its public API.

Targets can resolve directly to:

* `VitalComponent`
* `StatBlock`
* `Node`

Vital resolution:

```gdscript
func _resolve_vitals(target)
```

Stat block resolution:

```gdscript
func _resolve_stat_block(target)
```

The manager therefore does not require callers to manually locate a target's internal components before applying an effect.

```text
Effect Target
      ↓
EffectManager
      ├── VitalComponent
      │
      └── StatBlock
```

This provides a consistent target resolution layer for all effect execution paths.

---

# 🎯 Effect Trigger Pipeline

Effects enter the manager through trigger-specific APIs.

```text
OnEquip
   ↓
apply_on_equip()

Immediate
   ↓
apply_on_consume()

OnHit
   ↓
apply_on_hit()

OnCrit
   ↓
apply_on_crit()

Passive
   ↓
apply_passive()
```

Each trigger validates the effect's configured `trigger_type` before forwarding the effect to the core application pipeline.

---

# 🧠 Core Effect Application

```gdscript
func add_effect(
    effect: StatEffect,
    target: Variant,
    source: Node = null,
    override_source_id: String = ""
)
```

`add_effect()` is the central entry point for runtime effect application.

The pipeline evaluates effects in the following order:

```text
StatEffect
    ↓
Cost Effect?
    ├── Yes → Process Cost
    │
    └── No
         ↓
Instant Effect?
    ├── Yes → Execute Immediately
    │
    └── No
         ↓
Existing Effect?
    ├── Yes → Stack / Duration Handling
    │
    └── No
         ↓
Register Active Effect
```

This separates immediate effects from persistent effects while keeping all effect execution within a single runtime authority.

---

# 💰 Effect Cost Processing

Effects marked as cost effects are processed before normal effect execution.

```gdscript
func _process_effect_cost(
    effect: StatEffect,
    target: Variant,
    source: Node
)
```

The manager determines the payer using:

```text
Source
  ↓
if unavailable
  ↓
Target
```

The resolved `VitalComponent` then processes the configured effect cost.

If the cost cannot be paid, the effect is not applied.

```text
Effect Cost
     ↓
Resolve Payer
     ↓
VitalComponent
     ↓
Try Apply Costs
     ↓
Success ─────→ Continue
Failure ─────→ Effect Cancelled
```

---

# ⚡ Instant Effects

Instant effects are executed immediately rather than registered as persistent active effects.

Supported instant effect types include:

* `HEAL`
* `DAMAGE`
* `REVIVE`
* `KNOCKBACK`

---

# ❤️ Healing

Healing resolves the target's `VitalComponent` and restores Health.

Healing may be configured as either:

**Flat**

```text
Amount = 100
```

or:

**Percentage**

```text
Amount = 10%
```

Percentage healing is calculated from the target's maximum HP.

```text
StatEffect
    ↓
EffectManager
    ↓
VitalComponent
    ↓
Heal
```

Healing events are also written to the combat event log.

---

# ⚔️ Damage Integration

Damage effects are routed through the centralized `CombatManager`.

```text
StatEffect
    ↓
EffectManager
    ↓
DamageRequest
    ↓
CombatManager
    ↓
CombatResolver
    ↓
VitalComponent
```

The Effect Manager resolves the source and target `StatBlock` instances before calculating the requested effect damage.

The resulting `DamageRequest` includes:

* Source
* Target
* Calculated amount
* Damage type
* Damage origin
* Attack-effect trigger state

Effect-generated direct damage is classified as a skill-origin damage request and does not trigger equipment attack effects.

This keeps damage calculation and mitigation centralized within the combat pipeline.

---

# 💀 Revive

Revive effects restore a dead target.

The effect can independently define restoration values for:

* HP
* MP
* Class Resource

Each value supports:

* Flat restoration
* Percentage restoration
* Full restoration when configured below zero

```text
Revive Effect
      ↓
VitalComponent
      ↓
Dead → Alive
      ↓
HP / MP / Resource restored
```

The corresponding vital-change signals are emitted after restoration.

---

# 💨 Knockback

Knockback effects directly invoke the target's knockback behavior.

The effect provides:

* Direction
* Knockback force

The direction is normalized before being applied.

Knockback therefore remains an effect-driven behavior while the target remains responsible for its own movement response.

---

# 📚 Active Effects

Effects that are not instant effects can be registered into:

```gdscript
active_effects
```

Each active effect stores runtime state including:

```text
id
effect
target
remaining
tick_timer
stacks
source_id
source
```

This allows the manager to track each active effect independently.

```text
StatEffect Resource
        ↓
Runtime Active Effect
        ↓
┌──────────────────────┐
│ Effect Definition    │
│ Target               │
│ Source               │
│ Duration             │
│ Tick Timer           │
│ Stack Count          │
│ Source ID            │
└──────────────────────┘
```

---

# 📚 Effect Stacking

Existing effects are evaluated through `EffectStackHandler`.

```gdscript
func _handle_existing_effect(
    effect: StatEffect,
    target: Variant
)
```

The manager first determines whether the effect supports stack or duration behavior.

Matching active effects are then passed to the stack handler.

```text
New Effect
    ↓
Find Matching Active Effect
    ↓
EffectStackHandler
    ↓
Stack / Duration Result
    ↓
Update Runtime State
    ↓
Reapply Stat Source
```

When stack state changes, the manager reapplies the effect's stat contribution using the updated stack count.

This keeps stacking behavior separate from the Effect Manager's runtime lifecycle management.

---

# 📊 StatManager Integration

The Effect Manager provides the runtime bridge between `StatEffect` and `StatManager`.

Only stat-affecting effect types are forwarded to the StatManager.

Supported types include:

* `STAT_MOD`
* `OVERRIDE`

For `STAT_MOD`, effects may provide either:

* Flat modification
* Percentage modification

Stack counts are incorporated into the resulting modifier.

```text
StatEffect
    ↓
EffectManager
    ↓
StatManager.set_effect_source()
    ↓
StatBlock
```

Example:

```text
+10 Strength
× 3 stacks
      ↓
+30 Strength
```

Percentage effects are similarly multiplied by the active stack count.

The Effect Manager does not calculate the final derived stat values. It only registers the effect source with `StatManager`.

---

# ⏱️ Active Effect Tick System

The manager processes active effects every frame.

```gdscript
func _process(delta: float)
```

Effects with the following types use the tick system:

* `DOT`
* `HOT`

The tick timer is processed before effect duration.

```text
Active Effect
      ↓
Tick Timer
      ↓
Tick Triggered?
   ├── Yes → Apply Tick
   └── No
      ↓
Duration Timer
      ↓
Expired?
   ├── Yes → Remove Effect
   └── No → Continue
```

Multiple ticks can occur during a single frame when necessary because the manager processes the tick timer in a loop until the timer has advanced back into its valid interval.

---

# 🔥 Damage Over Time

DOT effects calculate damage through the same centralized combat pipeline as direct damage.

```text
Active DOT
    ↓
Resolve Source StatBlock
    ↓
Resolve Target StatBlock
    ↓
Resolve Damage Amount
    ↓
DamageRequest
    ↓
CombatManager
    ↓
CombatResolver
    ↓
Target VitalComponent
```

DOT requests use:

```gdscript
DamageRequest.DamageOrigin.DOT
```

This allows the combat system to distinguish damage-over-time from attacks, skills, reflected damage, and other damage origins.

---

# 💚 Healing Over Time

HOT effects restore Health on each configured tick.

Percentage-based HOT effects calculate their healing amount from the target's maximum HP.

```text
Active HOT
    ↓
Tick Interval
    ↓
Resolve Amount
    ↓
VitalComponent.heal()
```

Each healing tick is also recorded through the combat event log.

---

# 🧹 Effect Removal

```gdscript
func _remove_effect(index: int)
```

When an active effect expires or is otherwise removed, the manager removes its associated stat source from `StatManager`.

```text
Active Effect
      ↓
Effect Expired
      ↓
StatManager.remove_effect_source()
      ↓
Remove Runtime Effect
```

This prevents persistent stat modifiers from remaining after their originating effect has ended.

---

# 🧹 Forced Removal

```gdscript
func force_remove_by_source(source_id: String)
```

Allows a specific effect source to be removed immediately.

The manager searches active effects for the matching source ID, removes its associated StatManager source, and removes the runtime effect.

This provides an explicit cleanup path for externally controlled effect lifecycles.

---

# 🧹 Target Effect Cleanup

```gdscript
func clear_all_effects(target: Variant)
```

Removes every active effect associated with a specific target.

Each matching effect has its StatManager source removed before the runtime entry is deleted.

```text
Target
  ↓
Find Active Effects
  ↓
Remove Stat Sources
  ↓
Remove Active Effects
```

This is useful when a target is reset, removed, or otherwise needs all active effects cleared.

---

# 🔗 System Relationships

```text
                    StatEffect
                        |
                        ↓
                  EffectManager
                        |
        ┌───────────────┼────────────────┐
        ↓               ↓                ↓
   VitalComponent   StatManager     CombatManager
        |               |                |
        ↓               ↓                ↓
   HP / MP /       Stat Sources      Damage
   Resource        & Modifiers       Resolution
        |
        ↓
   Target Runtime

EffectManager
      |
      ↓
EffectStackHandler
      |
      ↓
Stack / Duration State
```

---

# 🧭 System Boundaries

| Responsibility              | System                             |
| --------------------------- | ---------------------------------- |
| Effect definition           | `StatEffect`                       |
| Effect runtime lifecycle    | Effect Manager                     |
| Target component resolution | Effect Manager                     |
| Effect stacking             | `EffectStackHandler`               |
| Vital modification          | `VitalComponent`                   |
| Stat modification           | `StatManager`                      |
| Damage calculation          | `CombatManager` / `CombatResolver` |
| Damage application          | `VitalComponent`                   |
| Effect source cleanup       | Effect Manager + `StatManager`     |
| Combat/event logging        | `EventManager`                     |

The Effect Manager coordinates these systems without taking ownership of their underlying responsibilities.

---

# 🌱 Data-Driven Effect Architecture

The effect system separates **effect definition** from **runtime execution**.

```text
StatEffect Resource
        ↓
EffectManager
        ↓
Runtime Effect Behavior
        ↓
Specialized System
```

This allows the same `StatEffect` resource architecture to support immediate, persistent, stat-based, combat-based, and timed behaviors without embedding effect-specific logic into individual items, abilities, or actors.

---

# 🚀 Framework Integration

The Effect Manager is initialized as part of the framework's centralized launch flow.

Other systems submit effects through its public API rather than directly manipulating active effect state.

Examples include:

```text
Equipment
    ↓
apply_on_equip()

Consumables
    ↓
apply_on_consume()

Combat
    ↓
apply_on_hit()
apply_on_crit()

Abilities
    ↓
add_effect()

Passive Systems
    ↓
apply_passive()
```

This establishes the Effect Manager as the centralized runtime entry point for effect execution.

---

# ✅ Design Rule

**EffectManager is the central runtime authority for applying and maintaining `StatEffect` behavior.**

`StatEffect` defines **what an effect is**.

EffectManager determines **when and how that effect enters runtime**.

Specialized systems remain responsible for their own domains:

* `VitalComponent` owns vitals.
* `StatManager` owns stat sources and stat calculation.
* `CombatManager` owns damage resolution.
* `EffectStackHandler` owns stacking and duration rules.

Future effect types should extend this centralized pipeline rather than introducing independent effect execution paths inside items, abilities, actors, or individual gameplay systems.
