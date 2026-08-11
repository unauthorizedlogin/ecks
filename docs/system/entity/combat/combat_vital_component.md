# ❤️ Vital Component Architecture

## Overview

The Vital Component manages the runtime state of an entity's health, mana, class resource, death state, regeneration, and resource costs.

It does not own or calculate entity statistics.

Instead, it reads maximum values from the entity's stat system and maintains the mutable runtime values associated with those statistics.

The component is responsible for:

* current HP, MP, and class resource
* damage and healing
* death and revival
* regeneration
* resource cost validation
* resource cost payment
* vital state signals
* synchronization with rebuilt stats
* damage team ownership

---

### 🧱 Runtime State

Each entity using a `VitalComponent` maintains mutable runtime values:

```text
VitalComponent
│
├── Current HP
├── Current MP
├── Current Class Resource
├── Dead State
│
├── Regeneration
│
└── Damage Team
```

Maximum values are not stored independently.

They are resolved from the entity's active stat configuration.

```text
Stat System
     │
     ├── HP
     ├── MP
     └── ClassResource
          │
          ↓
   VitalComponent
          │
          ├── Current HP
          ├── Current MP
          └── Current Resource
```

---

### 📊 Stat Integration

The Vital Component reads runtime statistics through its `stat_owner`.

It uses:

* `HP`
* `MP`
* `ClassResource`
* `HPRegen`
* `MPRegen`
* `ResourceRegen`

This keeps maximum values and regeneration values within the existing stat architecture.

The Vital Component does not calculate derived statistics.

---

### ❤️ Health Management

The component handles:

* damage
* healing
* maximum HP clamping
* death detection
* health change signals

Damage is applied through:

```gdscript
apply_damage(amount)
```

Healing is applied through:

```gdscript
heal(amount)
```

When HP reaches zero, the component transitions into the dead state.

---

### ☠️ Death & Revival

Death is tracked through:

```gdscript
dead
```

The component emits:

```gdscript
died(owner)
```

when the entity dies.

Revival is handled through the existing effect system:

```gdscript
apply_revive(effect)
```

Revival can restore:

* HP
* MP
* Class Resource

Values may be defined as either fixed amounts or percentages.

---

### 💙 Resource Management

The component manages the runtime values of:

```text
MP
ClassResource
```

Resources are clamped between:

```text
0 → Maximum
```

Changes emit their respective signals:

```gdscript
mp_changed(current, maximum)
resource_changed(current, maximum)
```

---

### 💸 Cost System

The Vital Component provides cost handling for `StatEffect` resources.

Supported costs:

* HP
* MP
* Class Resource

Costs can use either:

```text
Flat Amount
Percentage
```

Payment behavior is controlled through `StatEffect.CostPaymentMode`.

```text
NONE
REQUIRE_FULL
SPEND_AVAILABLE
```

HP costs are routed through the combat system as a `DamageRequest` with:

```text
DamageOrigin.HP_COST
```

This keeps HP cost processing inside the same damage pipeline used by combat.

---

### ♻️ Regeneration

Regeneration is processed through a tick-based timer.

```text
VitalComponent
      │
      ↓
 Regen Tick
      │
      ├── HPRegen
      ├── MPRegen
      └── ResourceRegen
```

Regeneration values are read from the active stat system.

HP regeneration also observes the configured post-damage delay.

---

### 📡 Signals

The component exposes signals for runtime vital state changes:

```text
health_changed
mp_changed
resource_changed
died
```

These allow other systems such as UI, combat feedback, and gameplay systems to react without directly managing vital state.

---

### ⚔️ Combat Integration

The Vital Component does not calculate combat damage or mitigation.

Combat resolution is handled by:

* Combat System
* Combat Resolver
* Damage Request
* Damage Result

The Vital Component receives the finalized damage amount and applies it to runtime HP.

```text
Attack
   │
   ↓
Combat System
   │
   ↓
Combat Resolver
   │
   ↓
Damage Result
   │
   ↓
VitalComponent
   │
   ↓
Current HP
```

---

### 🛡️ Damage Team

The component also provides the entity's:

```gdscript
damage_team
```

This is used by combat and ability targeting systems for:

* ally validation
* enemy validation
* friendly-fire rules
* targeting restrictions

The Vital Component therefore acts as the entity-facing source for combat team identity without owning combat resolution.

---

### 🔄 Stat Synchronization

When the stat system rebuilds an entity's statistics, the Vital Component synchronizes its runtime values against the new maximums.

Example:

```text
Stats Rebuilt
     │
     ↓
VitalComponent
     │
     ↓
Recalculate Maximums
     │
     ↓
Clamp Current Values
     │
     ↓
Emit Vital Signals
```

This prevents current HP from exceeding a newly reduced maximum.

---

### 💾 Runtime Initialization

The component initializes after the entity's stat configuration is ready.

Initialization waits for:

* entity stat readiness
* difficulty/world readiness
* valid stat ownership

Once initialized, the component resolves:

```text
Maximum HP
Maximum MP
Maximum Class Resource
```

and establishes the initial runtime state.

---

### 🧩 System Integration

The Vital Component integrates with:

* Stat System
* Stat Manager
* Combat System
* Effect System
* Difficulty System
* Player Manager
* Game Manager
* Event Manager

Its primary role is to provide a clean runtime state boundary between these systems and the entity's mutable vital values.

---

### 📁 Architecture

```text
Entity
│
├── Stat System
│   └── Maximum / Derived Values
│
└── VitalComponent
    │
    ├── Current HP
    ├── Current MP
    ├── Current Resource
    ├── Death State
    ├── Regeneration
    └── Damage Team
         │
         ├── Combat System
         ├── Effect System
         └── Ability System
```

---

### ✅ System Responsibilities

The Vital Component:

✅ Stores runtime vital state
✅ Handles damage and healing
✅ Handles death and revival
✅ Manages HP, MP, and class resource
✅ Processes regeneration
✅ Handles effect resource costs
✅ Provides damage team information
✅ Synchronizes with rebuilt stats
✅ Emits vital state signals

The Vital Component does **not**:

❌ Calculate combat damage
❌ Calculate mitigation
❌ Own entity statistics
❌ Resolve attacks
❌ Manage abilities
❌ Manage UI
❌ Define gameplay effects

Those responsibilities belong to:

* Stat System
* Combat System
* Effect System
* Ability System
* UI System
