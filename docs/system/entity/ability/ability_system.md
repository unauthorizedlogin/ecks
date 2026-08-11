# ⚡ Ability Manager

## Overview

The **Ability Manager** is the central authority for all ability execution within the framework.

It is responsible for validating ability usage, managing cooldowns, coordinating runtime execution, and driving the casting lifecycle.

The manager does **not** determine what an ability actually does. Instead, abilities are defined entirely through data and ultimately resolve into one or more `StatEffect` resources processed by the **Effect Manager**.

This includes:

* ⚔️ Offensive abilities
* 🛡 Defensive abilities
* 💚 Support abilities
* 🧰 Utility abilities
* ✨ Passive abilities
* 🔄 Toggle abilities
* ⏳ Cast-time abilities
* 📡 Channeled abilities

---

# 🧠 Core Responsibilities

The Ability Manager provides:

* Ability validation
* Requirement checking
* Cooldown management
* Runtime creation
* Ability lifecycle events
* Cast interruption
* Runtime cleanup

All gameplay abilities flow through the same execution pipeline regardless of their source.

---

# ⚙️ Initialization

```gdscript
func initialize()
```

Registers the Ability Manager as an active gameplay system.

Prevents duplicate initialization.

Output:

```
⚡ AbilityManager initialized
```

---

# 🎯 Ability Request Pipeline

Every ability begins with a cast request.

```
Ability Request
       ↓
Ability Database
       ↓
Requirement Validation
       ↓
Cooldown Validation
       ↓
Create Runtime
       ↓
Execute Ability
       ↓
Effect Manager
```

The manager serves as the gateway between gameplay input and runtime execution.

---

# ✅ Ability Validation

Before an ability can execute, the manager verifies that all requirements have been satisfied.

Supported validation includes:

* Class requirements
* Level requirements
* Stat requirements
* Quest completion
* Item ownership

If any requirement fails, the request is rejected before runtime creation.

---

# 📋 Requirement System

Ability requirements are evaluated individually using dedicated validation logic.

Supported requirement types include:

* None
* Class
* Level
* Stat
* Quest
* Item

This allows abilities to be gated by character progression, equipment, or world progression without embedding game logic inside the abilities themselves.

---

# ⏱️ Cooldown System

Cooldowns are tracked per caster and per ability.

Each cooldown entry is uniquely associated with:

```
Caster
      +
Ability
```

This allows multiple entities to use the same ability independently while maintaining their own cooldown timers.

Abilities without a cooldown bypass this system entirely.

---

# ⚙️ Runtime Management

Rather than executing abilities directly, the manager creates an **Ability Runtime** instance for every successful cast.

The runtime is responsible for:

* Cast timing
* Channel timing
* Effect execution
* Completion
* Interruption

The manager tracks all active runtimes until they complete or are interrupted.

---

# 📡 Ability Lifecycle Events

The manager broadcasts ability state changes throughout execution.

Supported lifecycle events include:

* Ability started
* Casting
* Channeling
* Finished
* Interrupted

These events allow animation, UI, audio, combat logging, and other systems to react without becoming tightly coupled to ability execution.

---

# 🚫 Ability Interruptions

Active abilities may be interrupted before completion.

Interrupting an ability immediately ends its runtime and prevents any remaining execution from occurring.

This supports gameplay such as:

* Movement interruption
* Crowd control
* Stuns
* Silence
* Manual cancellation

---

# 🔗 Ability Execution Pipeline

The Ability Manager does not apply gameplay effects directly.

Instead, execution follows a layered pipeline.

```
Ability Definition
        ↓
Ability Runtime
        ↓
Effect Manager
        ↓
Stat Effects
        ↓
Combat / Stats / Vitals
```

This separation keeps ability execution independent from combat calculations and stat modification.

---

# 🔄 Active Ability Tracking

Currently executing abilities are tracked through:

```gdscript
active_abilities
```

Each runtime maintains:

* Ability definition
* Caster
* Target
* Execution state
* Cast progress
* Channel progress
* Interruption state

This allows multiple abilities from multiple entities to execute simultaneously.

---

# 🔗 System Relationships

```
             Player Input
                  |
                  ↓
          AbilityManager
                  |
        ┌─────────┴─────────┐
        ↓                   ↓
 Requirement Check     Cooldown Check
        |                   |
        └─────────┬─────────┘
                  ↓
           Ability Runtime
                  |
                  ↓
          Ability Definition
                  |
                  ↓
           Effect Manager
                  |
      ┌───────────┼───────────┐
      ↓           ↓           ↓
 Combat      StatManager   VitalComponent
```

---

# ✅ Design Rule

**AbilityManager is the single authority for ability execution.**

No gameplay system should bypass the manager to execute abilities directly.

All abilities—whether active, passive, toggle, instant, cast-time, or channeled—should be represented as `AbilityDefinition` resources, validated by the Ability Manager, executed through an `AbilityRuntime`, and resolved into gameplay effects through the **Effect Manager**.
