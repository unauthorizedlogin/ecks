# ⚔️ Combat Data Architecture

## Overview

The Combat Data layer provides the lightweight data structures used to move combat information through the combat pipeline.

The system is divided into three primary data containers:

* `CombatContext` — Runtime combat participants and their resolved combat data
* `DamageRequest` — Damage being requested
* `DamageResult` — Finalized damage outcome

These classes contain **combat data only**.

They do not perform damage calculations, apply damage, manage abilities, or control combat flow.

---

### 🧱 Combat Data Architecture

```text
Combat Data
│
├── CombatContext
│   └── Attacker / Defender State
│
├── DamageRequest
│   └── Incoming Damage Definition
│
└── DamageResult
    └── Resolved Damage Outcome
```

The data flows through the combat pipeline:

```text
Combat Action
      │
      ↓
DamageRequest
      │
      ↓
CombatContext
      │
      ↓
CombatResolver
      │
      ↓
DamageResult
```

---

## 🧠 CombatContext

`CombatContext` represents the current combat relationship between an attacker and defender.

It contains:

* Attacker
* Defender
* Attacker `StatBlock`
* Defender `StatBlock`
* Base damage
* Damage type
* Attacker damage team
* Defender damage team

The context provides the resolver with the information required to evaluate an attack.

---

### 🔨 Context Construction

Contexts can be constructed directly from combat entities:

```gdscript
CombatContext.build_from_nodes(attacker, defender)
```

The builder resolves:

```text
Attacker
   │
   └── VitalComponent
          ├── StatBlock
          └── Damage Team

Defender
   │
   └── VitalComponent
          ├── StatBlock
          └── Damage Team
```

This prevents individual combat systems from having to manually assemble the attacker's and defender's combat state.

---

## 💥 DamageRequest

`DamageRequest` describes a request to deal damage.

It is intentionally generic so the same structure can be used by different damage sources.

Supported origins include:

```text
ATTACK
SKILL
DOT
HP_COST
REFLECT
ENVIRONMENT
DEBUG
```

This allows melee attacks, projectiles, skills, damage-over-time effects, environmental damage, and other sources to enter the same damage pipeline.

---

### 🎯 Damage Request Data

A request can contain:

* Source
* Target
* Damage amount
* Damage type
* Damage origin
* Attack-effect behavior
* Critical-hit rules
* Guaranteed critical state
* Tags
* Skill ID
* Projectile ID

Example:

```text
DamageRequest
│
├── Source
├── Target
├── Amount
├── Damage Type
├── Origin
├── Critical Rules
├── Tags
├── Skill ID
└── Projectile ID
```

The request describes **what is being requested**, not what the final damage will be.

---

## 🎯 Critical Request Configuration

`DamageRequest` provides two critical-hit controls:

```text
can_crit
guaranteed_crit
```

This allows damage sources to control whether critical calculation is permitted and whether a critical hit is forced.

The actual critical calculation remains the responsibility of the combat resolution layer.

---

## 🏷️ Damage Tags

Requests can include arbitrary tags:

```gdscript
var tags: Array[String] = []
```

Tags provide additional metadata that can be consumed by combat systems without requiring new fields for every possible damage source.

---

## 📊 DamageResult

`DamageResult` represents the finalized outcome of a damage request.

It is returned after the combat pipeline has resolved the incoming damage.

The result contains:

* Source
* Target
* Original damage
* Final damage
* Damage type
* Critical state
* Killing-blow state
* Block state
* Dodge state
* Miss state
* Valid-hit state

---

### 📈 Damage Transformation

The result preserves both the original and finalized values.

```text
Original Damage
      │
      ↓
Combat Resolution
      │
      ├── Critical
      ├── Resistance
      ├── Defense
      ├── Block
      └── Dodge
      │
      ↓
Final Damage
```

This allows combat systems and combat logs to distinguish between the requested damage and the actual resolved result.

---

## 🔄 Combat Data Flow

The complete data flow is:

```text
┌─────────────────┐
│ Combat Action   │
└────────┬────────┘
         ↓
┌─────────────────┐
│ DamageRequest   │
│                 │
│ Source          │
│ Target          │
│ Amount          │
│ Damage Type     │
│ Origin          │
│ Crit Rules      │
└────────┬────────┘
         ↓
┌─────────────────┐
│ CombatContext   │
│                 │
│ Attacker        │
│ Defender        │
│ Stats           │
│ Damage Teams    │
└────────┬────────┘
         ↓
┌─────────────────┐
│ CombatResolver  │
└────────┬────────┘
         ↓
┌─────────────────┐
│ DamageResult    │
│                 │
│ Original Damage │
│ Final Damage    │
│ Crit            │
│ Block           │
│ Dodge           │
│ Miss            │
│ Killing Blow    │
└─────────────────┘
```

---

## 🔗 Combat System Integration

The Combat Data layer connects the systems responsible for initiating, resolving, and applying combat.

```text
Ability / Attack / Projectile / Effect
                 │
                 ↓
          DamageRequest
                 │
                 ↓
          Combat Pipeline
                 │
          ┌──────┴──────┐
          ↓             ↓
   CombatContext   CombatResolver
          │             │
          └──────┬──────┘
                 ↓
           DamageResult
                 │
                 ↓
          Damage Application
```

This allows different combat sources to use the same underlying data structures.

---

## 🧩 Universal Damage Support

The `DamageRequest` structure is intentionally source-agnostic.

The same data container supports:

* ⚔️ Melee attacks
* 🏹 Projectiles
* 💥 Area effects
* 🔮 Skills
* ☠️ Damage over time
* 🩸 HP costs
* 🔄 Reflected damage
* 🌎 Environmental damage
* 🐞 Debug damage

This keeps individual gameplay systems from requiring their own damage-request implementations.

---

## 📦 Responsibilities

### CombatContext

✅ Stores attacker and defender state
✅ Resolves combat stats from entities
✅ Resolves damage teams
✅ Provides combat participants to the resolver

### DamageRequest

✅ Describes incoming damage
✅ Identifies damage source
✅ Defines damage origin
✅ Defines damage type
✅ Defines critical rules
✅ Stores optional combat metadata

### DamageResult

✅ Stores finalized damage
✅ Preserves original damage
✅ Records critical state
✅ Records block/dodge state
✅ Records miss state
✅ Records killing-blow state

The Combat Data layer does **not**:

❌ Calculate damage
❌ Apply damage
❌ Resolve resistance
❌ Resolve armor
❌ Execute abilities
❌ Manage combat flow
❌ Manage health directly

Those responsibilities belong to:

* Combat Manager
* Combat Resolver
* Effect System
* Ability System
* Vital Components

---

## Summary

The Combat Data layer provides the standardized **data contracts for combat processing**.

`DamageRequest` describes the incoming damage, `CombatContext` provides the combatants and their resolved combat state, and `DamageResult` records the finalized outcome.

Together they allow attacks, skills, projectiles, DoTs, and other damage sources to share the same combat pipeline without embedding combat logic inside the data containers.
