# ⚔️ Attack Component Architecture

## Overview

The Attack Component provides the runtime auto-attack loop for an entity.

It manages:

* attack target
* attack timing
* target validation
* attack range validation
* auto-attack execution
* attack blocking
* target clearing

The component does not define attack abilities or resolve combat damage.

Instead, it uses the entity's configured auto-attack ability and routes execution through the Ability System.

---

### 🎯 Target Management

The component maintains a single active target:

```gdscript
target: Node
```

Targets are validated before an attack can occur.

Validation checks:

* Target exists
* Target is still valid
* Target is a `Node2D`
* Target has a `VitalComponent`
* Target is not dead
* Entity has a valid auto-attack ability
* Target is within ability range

Invalid targets are automatically cleared.

---

### ⏱️ Attack Timing

The component maintains an internal attack timer.

```text
Target
  │
  ↓
Validate Target
  │
  ↓
Attack Timer
  │
  ↓
Timer Ready
  │
  ↓
Auto Attack
```

After a successful attack, the timer is reset using the calculated attack interval.

---

### ⚔️ Auto-Attack Execution

The component does not directly execute the attack.

It retrieves the entity's configured auto-attack:

```gdscript
get_auto_attack()
```

and passes it to:

```gdscript
AbilityManager.auto_attack()
```

This keeps attack execution inside the Ability System.

```text
AttackComponent
      │
      ↓
Entity Auto-Attack
      │
      ↓
AbilityManager
      │
      ↓
Ability Runtime
      │
      ↓
Effects / Combat
```

---

### 📏 Range Validation

Attack range is determined by the configured auto-attack ability.

The component checks:

```text
Target Distance
      │
      ├── Below min_range → Invalid
      │
      ├── Within range → Valid
      │
      └── Above max_range → Invalid
```

When the target is outside the valid range, the target is cleared.

---

### ⚡ Attack Speed

Attack timing is derived from the auto-attack ability and the entity's `AtkSpd` stat.

The component resolves the final attack interval from:

```text
Ability Attack Time
        +
Entity AtkSpd
```

This keeps attack speed as a stat-driven runtime modifier rather than a hardcoded component value.

---

### 🚫 Attack Blocking

The component can temporarily block automatic attacks using:

```gdscript
attack_blocked
```

A failed ability requirement blocks further automatic attempts until the target is reset.

Successful target assignment clears the blocked state.

---

### 📡 Ability Result Handling

The Attack Component responds to `AbilityManager.CastResult`.

```text
SUCCESS
    → Reset attack timer

FAILED_REQUIREMENTS
    → Block attacks

FAILED_RANGE
    → Clear target

FAILED_COOLDOWN
    → Reset attack timer

INVALID
    → Clear target
```

This allows the Attack Component to remain a lightweight controller while the Ability System owns ability validation.

---

### ☠️ Target Death Handling

When a target exposes the `died` signal, the Attack Component connects to it.

```text
Target Dies
    │
    ↓
died signal
    │
    ↓
clear_target()
```

This prevents the attack loop from continuing to reference dead targets.

---

### 🧩 System Integration

The Attack Component integrates with:

* Ability System
* Ability Database
* Vital Component
* Stat System
* Combat System

Its primary relationship is:

```text
AttackComponent
      │
      ├── Target Validation
      │
      ├── Attack Timing
      │
      ↓
AbilityManager
      │
      ↓
AbilityDefinition
      │
      ↓
Combat / Effect Systems
```

---

### 📁 Component Structure

```text
AttackComponent
│
├── Target
├── Attack Timer
├── Attack Blocking
│
├── Target Validation
│
├── Range Validation
│
├── Attack Interval
│
└── Auto-Attack Execution
        │
        ↓
   AbilityManager
```

---

### ✅ System Responsibilities

The Attack Component:

✅ Maintains the current attack target
✅ Validates attack targets
✅ Validates attack range
✅ Manages attack timing
✅ Calculates the attack interval
✅ Reads `AtkSpd`
✅ Executes auto-attacks through AbilityManager
✅ Handles ability cast results
✅ Clears dead or invalid targets

The Attack Component does **not**:

❌ Define abilities
❌ Apply effects
❌ Resolve damage
❌ Calculate mitigation
❌ Manage combat results
❌ Manage targeting UI

Those responsibilities belong to:

* Ability System
* Effect System
* Combat System
* UI System
