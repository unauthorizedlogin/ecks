# ⚔️ Combat Resolver Architecture

## Overview

The Combat Resolver provides the centralized damage resolution layer for combat interactions.

It receives a `CombatContext`, validates whether the attack can affect the defender, then resolves damage through critical hit calculation, resistance mitigation, and defense mitigation.

The resolver produces a `DamageResult` rather than directly managing the broader combat lifecycle.

---

### 🧱 Combat Resolution Architecture

```text
CombatContext
      │
      ↓
CombatResolver
      │
      ├── Hit Validation
      ├── Base Damage
      ├── Critical Hit
      ├── Resistance
      └── Defense
              │
              ↓
         DamageResult
```

---

### 🎯 Hit Validation

Before damage is calculated, the resolver performs a universal targeting check.

The resolver validates:

* Attacker
* Defender
* Attacker team
* Defender team
* Friendly-fire rules

Allied targets are rejected when friendly fire is disabled.

```text
Attack Request
      │
      ↓
  Can Hit?
   │    │
  No    Yes
   │     │
   ↓     ↓
Miss   Damage
```

Invalid attacks return a `DamageResult` with:

```text
final_damage = 0
missed = true
was_valid_hit = false
```

---

### 💥 Base Damage

Base damage is resolved from the `CombatContext`.

If the context provides a specific base damage value, that value is used.

Otherwise, the attacker's `Damage` stat becomes the base value.

```text
CombatContext.base_damage
        │
        ├── > 0 → Use Context Damage
        │
        └── Otherwise → Attacker Damage
```

---

### 🎯 Critical Hit Resolution

Critical chance is derived from the attacker's `CritHit` stat.

Critical damage is derived from:

```text
CritDmg
```

The resolver performs a random critical check and applies the configured critical multiplier when successful.

```text
CritHit
   ↓
Critical Roll
   │
   ├── No → Normal Damage
   │
   └── Yes
        ↓
     CritDmg
        ↓
   Modified Damage
```

The result records whether the attack was critical.

---

### 🛡️ Resistance Mitigation

Damage resistance is selected based on the incoming `DamageType`.

Supported resistance mappings include:

* Arcane
* Dark
* Light
* Earth
* Ice
* Fire
* Lightning
* Physical
* Poison
* Water
* Wind
* All Resistance

Resistance is capped at `100%` during resolution.

```text
Damage Type
     ↓
Matching Resistance
     ↓
Resistance Cap
     ↓
Damage Reduction
```

The resistance modifier is applied before defense mitigation.

---

### 🛡️ Defense Mitigation

After resistance is applied, the defender's `Armor` stat is subtracted from the remaining damage.

```text
Mitigated Damage
=
Resistance-Adjusted Damage
-
Armor
```

Damage cannot fall below `1` for a valid hit.

---

### 📊 Damage Resolution Pipeline

```text
CombatContext
      │
      ↓
Target Validation
      │
      ├── Invalid → Miss
      │
      ↓
Base Damage
      │
      ↓
Critical Check
      │
      ↓
Damage Type Resistance
      │
      ↓
Armor Mitigation
      │
      ↓
Minimum Damage
      │
      ↓
DamageResult
```

---

### 📦 Damage Result

The resolver returns a `DamageResult` containing the outcome of the attack.

The result records:

* Final damage
* Critical state
* Miss state
* Valid-hit state

This keeps combat resolution separate from the systems responsible for actually applying the resulting damage.

---

### 🔗 System Integration

The Combat Resolver relies on:

* `CombatContext`
* `DamageResult`
* `StatBlock`
* `StatEffect.DamageType`
* Attacker stats
* Defender stats
* Damage Team rules

The resulting `DamageResult` can then be consumed by the broader combat pipeline.

```text
Combat Request
      │
      ↓
CombatContext
      │
      ↓
CombatResolver
      │
      ↓
DamageResult
      │
      ↓
Combat / Vital Systems
```

---

### ⚖️ Damage Mitigation Order

The resolver uses a defined mitigation order:

1. **Hit validation**
2. **Base damage resolution**
3. **Critical hit modification**
4. **Resistance mitigation**
5. **Armor mitigation**
6. **Minimum damage enforcement**
7. **Damage result**

This establishes a consistent calculation pipeline for attacks.

---

### 📦 Responsibilities

The Combat Resolver:

✅ Validates combat targets
✅ Enforces friendly-fire rules
✅ Resolves base damage
✅ Calculates critical hits
✅ Applies critical damage
✅ Resolves damage-type resistance
✅ Applies armor mitigation
✅ Enforces minimum damage
✅ Produces `DamageResult`

The Combat Resolver does **not**:

❌ Apply damage directly
❌ Manage player or enemy health
❌ Manage abilities
❌ Define damage effects
❌ Manage combat UI
❌ Manage inventory or equipment

Those responsibilities belong to:

* Combat System
* Vital Component
* Ability System
* Effect System
* Equipment System
* UI Systems

---

### Summary

The Combat Resolver is the centralized **damage calculation and validation layer** for combat.

It takes a `CombatContext`, determines whether the attack is valid, resolves critical hits and damage-type resistance, applies armor mitigation, and returns a standardized `DamageResult`.

This keeps damage calculation deterministic and centralized while allowing the systems that initiate attacks and apply damage to remain separate.
