# ⚔️ Ability Runtime Architecture

## Overview

The Ability Runtime provides the runtime execution layer for individual abilities.

While `AbilityDefinition` contains the static configuration of an ability, `AbilityRuntime` manages the **active execution state** of that ability while it is being used.

The runtime is responsible for:

* ability execution
* cast timing
* channeling
* toggled execution
* aura processing
* interruption
* target tracking
* effect application
* runtime execution signals

`AbilityRuntime` is created for an individual ability execution and does not store permanent ability definitions.

---

### 🧱 Runtime Architecture

Each runtime instance connects an ability definition with its caster and target.

```text
AbilityDefinition
        │
        ↓
   AbilityRuntime
        │
   ┌────┼──────────────┐
   ↓    ↓              ↓
Caster Target       Effects
                    │
                    ↓
              EffectManager
```

The runtime provides the execution state while `AbilityDefinition` remains the source of configuration.

---

### 🎯 Runtime State

Each runtime tracks:

* ability definition
* caster
* target
* active state
* interruption state
* aura targets

Runtime instances are therefore temporary execution objects rather than permanent gameplay data.

---

### 🚀 Ability Startup

Abilities are initialized through:

```gdscript
setup(ability, caster, target)
```

The runtime then begins execution through:

```gdscript
start()
```

The execution path is determined by the ability's configured category and cast behavior.

```text
AbilityRuntime
      │
      ↓
   start()
      │
      ├── Aura
      │
      └── Cast Behavior
            ├── Instant
            ├── Cast Time
            ├── Channel
            └── Toggle
```

---

### ⚡ Instant Execution

Instant abilities execute immediately.

```text
start()
  ↓
_execute()
  ↓
finish()
```

The configured effects are passed to the `EffectManager`.

---

### ⏳ Cast-Time Execution

Cast-time abilities delay execution according to their configured cast duration.

```text
Start
  ↓
Casting Signal
  ↓
Wait Cast Time
  ↓
Check Interruption
  ↓
Execute Effects
  ↓
Finish
```

If the runtime is interrupted during the cast, the ability does not execute.

---

### 🔄 Channel Execution

Channel abilities execute repeatedly for the configured channel duration.

```text
Start
  ↓
Channeling Signal
  ↓
Execute
  ↓
Wait Tick
  ↓
Execute
  ↓
...
  ↓
Finish
```

Each channel iteration emits a progress signal.

```gdscript
tick(runtime, progress)
```

This allows UI or other systems to observe channel progress without owning the channel logic.

---

### 🔁 Toggle Execution

Toggle abilities execute through the runtime without a cast delay.

The runtime executes the configured effects and completes the execution cycle.

Persistent toggle behavior can therefore be handled by the systems controlling the ability state while the runtime remains responsible for execution.

---

### 🌐 Aura Execution

Aura abilities use a continuous runtime loop.

```text
Aura Runtime
     │
     ↓
Scan World
     │
     ↓
Find Valid Targets
     │
     ↓
Check Radius
     │
     ↓
Check Target Filter
     │
     ↓
Apply Missing Effects
     │
     ↓
Remove Effects From
Targets Outside Aura
     │
     └──────→ Repeat
```

Aura processing runs while the runtime remains active.

---

### 🎯 Aura Targeting

Aura targets are resolved using the ability's targeting configuration.

Supported targeting relationships include:

* Self
* Any
* Allies
* Enemies

Target validation uses the entity's `ResourceDamageTeam` relationship when team-based filtering is required.

This keeps aura targeting compatible with the existing combat team architecture.

---

### 📍 Aura Radius

Aura effects are only applied to valid entities within the configured ability radius.

The runtime evaluates:

```gdscript
caster.global_position.distance_to(
    target.global_position
)
```

Targets outside the configured radius are removed from the active aura effect set.

---

### 🧩 Aura Effect Tracking

Every aura effect receives a unique runtime source identifier.

```text
aura_<caster>_<ability>_<target>_<effect>
```

This allows the runtime to distinguish individual aura applications.

Example:

```text
Aura
 ├── Target A
 │    ├── Movement Effect
 │    └── Defense Effect
 │
 └── Target B
      ├── Movement Effect
      └── Defense Effect
```

When a target leaves the aura, its associated effects can be removed specifically through their source ID.

---

### ✨ Aura Application

Newly affected targets generate a combat event through:

```gdscript
_emit_aura_applied()
```

This provides observable feedback when an entity first enters an active aura.

Existing targets are not repeatedly logged every aura update.

---

### 💥 Effect Execution

The runtime does not implement effect behavior itself.

Instead, it resolves configured effect IDs through the Effect Database.

```text
AbilityDefinition
      │
      ↓
Effect IDs
      │
      ↓
EffectDatabase
      │
      ↓
StatEffect
      │
      ↓
EffectManager
```

This allows abilities to reuse the same effect definitions across different abilities and execution types.

---

### 🖐️ Trigger-Aware Execution

Effects can define their own trigger behavior.

For example:

```text
Ability
 ├── Immediate Effect
 └── OnHit Effect
```

The runtime routes `OnHit` effects through:

```gdscript
EffectManager.apply_on_hit()
```

while normal effects are routed through:

```gdscript
EffectManager.add_effect()
```

This keeps ability execution separate from the behavior of the effects themselves.

---

### 🛑 Interruption

Any active runtime can be interrupted through:

```gdscript
interrupt()
```

Interruption:

* marks the runtime interrupted
* clears active aura effects
* emits the interruption signal

```text
Interrupt
   ↓
is_interrupted = true
   ↓
Clear Aura
   ↓
Emit interrupted
```

Interrupted cast and channel executions therefore stop before completing their execution cycle.

---

### 🧹 Runtime Cleanup

When execution completes:

```gdscript
finish()
```

The runtime:

* marks itself inactive
* clears active aura effects
* emits the `finished` signal

Aura cleanup is therefore performed both when an ability finishes normally and when it is interrupted.

---

### 📡 Runtime Signals

The runtime exposes execution events through signals:

```gdscript
finished(runtime)
interrupted(runtime)
tick(runtime, progress)
casting(runtime)
channeling(runtime)
```

These allow other systems to observe ability execution without directly controlling the runtime.

Potential consumers include:

* Ability UI
* Casting indicators
* Channel progress bars
* Combat feedback
* Animation systems
* Gameplay event systems

---

### 🔗 System Integration

The Ability Runtime connects several existing systems:

```text
AbilityDefinition
        │
        ↓
AbilityRuntime
   │    │    │
   │    │    └── EventManager
   │    │
   │    └────── EffectDatabase
   │                  │
   │                  ↓
   │            StatEffect
   │                  │
   └──────────────────↓
                 EffectManager
```

Aura targeting additionally integrates with:

```text
ResourceDamageTeam
```

for ally and enemy filtering.

---

### 📦 Responsibilities

The Ability Runtime:

✅ Executes configured abilities
✅ Handles cast timing
✅ Handles channel execution
✅ Handles interruption
✅ Tracks runtime state
✅ Resolves ability effects
✅ Routes effects to EffectManager
✅ Maintains active aura targets
✅ Handles aura radius and filtering
✅ Cleans up aura effects
✅ Emits execution signals

The Ability Runtime does **not**:

❌ Define ability configuration
❌ Define effect behavior
❌ Manage permanent ability data
❌ Calculate character stats
❌ Own combat resolution
❌ Manage UI

Those responsibilities belong to:

* Ability Definition System
* Effect System
* Stat System
* Combat System
* UI Systems

---

### 🔄 Execution Summary

```text
Ability Requested
       │
       ↓
Create AbilityRuntime
       │
       ↓
Setup Caster / Target
       │
       ↓
Start Runtime
       │
       ├── Instant ──────→ Execute → Finish
       │
       ├── Cast Time ────→ Wait → Execute → Finish
       │
       ├── Channel ──────→ Tick → Execute → Finish
       │
       ├── Toggle ───────→ Execute → Finish
       │
       └── Aura ─────────→ Track Targets
                              │
                              ↓
                         Apply Effects
                              │
                              ↓
                         Remove Invalid
                              │
                              ↓
                           Cleanup
```

---

### Summary

The Ability Runtime provides the **execution state and lifecycle for individual ability activations**.

It separates ability configuration from runtime behavior while using the existing Effect, Combat, and Damage Team systems for gameplay functionality.

This allows abilities to share a common execution layer while supporting instant casts, cast times, channels, toggles, and persistent aura behavior.
