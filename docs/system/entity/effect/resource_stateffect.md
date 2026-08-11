# StatEffect Resource

`StatEffect` is the framework's primary **data definition for gameplay effects**.

It defines what an effect does, what stat or combat value it interacts with, when it executes, how long it persists, how it stacks, and whether it requires resource payment.

`StatEffect` is intentionally data-driven. Runtime systems such as `EffectManager`, `CombatManager`, `VitalComponent`, and `StatManager` consume these definitions to perform the actual gameplay operations.

---

## Architecture Role

`StatEffect` acts as the configuration layer between authored effect data and runtime effect processing.

```text
StatEffect Resource
        │
        ├── Effect Type
        ├── Target / Trigger
        ├── Stat / Amount
        ├── Damage Configuration
        ├── Duration / Ticks
        ├── Stack Rules
        └── Cost Rules
                │
                ▼
        Effect Manager
                │
        ├── Stat Manager
        ├── Vital Component
        └── Combat Manager
```

The resource does not maintain active-effect state. Runtime state is owned by the systems applying the effect.

---

# Identity

Each effect has basic identification and organization data:

```gdscript
@export var effect_id: String
@export var display_name: String
@export_multiline var description: String
```

`effect_id` provides the stable identifier used by databases, generators, abilities, items, and other systems.

`display_name` provides the localized presentation name.

`folder` and `subfolder` are used by the resource generation and organization pipeline.

---

# Effect Types

`EffectType` determines the fundamental operation performed by the effect.

```text
NONE
STAT_MOD
OVERRIDE
HEAL
DAMAGE
REVIVE
DOT
HOT
KNOCKBACK
```

### STAT_MOD

Modifies an existing stat by an amount.

Supports both flat and percentage-based modification.

### OVERRIDE

Directly replaces the target stat value with the configured amount.

### HEAL

Restores a target value, primarily HP, with support for percentage-based healing.

### DAMAGE

Creates a damage operation that is resolved through the combat pipeline.

### REVIVE

Restores a dead target using the configured revive values.

### DOT / HOT

Represent damage-over-time and healing-over-time effects.

Their runtime ticking is handled outside the resource.

### KNOCKBACK

Provides directional force data for runtime knockback processing.

---

# Stat Modification

Effects can target a broad set of character statistics through `stat_name`.

The supported values include:

* Vitals — HP, MP, ClassResource
* Regeneration — HPRegen, MPRegen, ResourceRegen
* Primary / attribute stats
* Damage and critical statistics
* Defensive statistics
* Resistance statistics
* Attack and movement statistics
* Reward modifiers such as GoldFind, MagicFind, and XPGain

This allows the same effect architecture to modify character statistics regardless of the source.

For example:

```text
Difficulty
Equipment
Buff
Debuff
Ability
Passive
Environment
        │
        ▼
    StatEffect
        │
        ▼
   StatManager
```

As additional character modifiers become part of the `StatBlock`, they can be exposed to the same effect pipeline without requiring a separate modifier architecture.

---

# Amount Resolution

Effects support both flat and percentage amounts.

```gdscript
@export var amount: float = 0.0
@export var amount_is_percent: bool = false
```

`resolve_amount()` converts the configured amount into its runtime value.

For percentage effects:

```text
resolved amount =
base value × (effect amount / 100)
```

This allows a single effect definition to represent both:

```text
+25 Damage
```

and:

```text
+25% Damage
```

---

# Damage Configuration

Damage effects contain additional information used by the combat pipeline.

### Damage Type

```text
ARCANE
DARK
EARTH
FIRE
ICE
LIGHT
LIGHTNING
PHYSICAL
POISON
WATER
WIND
```

The damage type determines which resistance is used during combat resolution.

### Damage Scaling

```text
NONE
EFFECT_DAMAGE
ENTITY_DAMAGE
ENTITY_PLUS_EFFECT
ENTITY_TIMES_EFFECT
```

This allows damage to originate from:

* The effect's configured amount
* The attacker's Damage stat
* A combination of entity and effect values
* Multiplication between entity damage and effect scaling

`resolve_damage_amount()` converts the effect definition into the initial damage value used by the combat system.

---

# Execution

Effects define when and where they execute through:

```gdscript
trigger_type
target_type
tags
```

Supported trigger types include:

```text
Immediate
OnEquip
OnHit
OnCrit
Passive
```

Target types include:

```text
None
Self
Target
Area
Allies
Enemies
```

The resource describes the intended execution behavior, while the systems responsible for the event determine when the effect is actually processed.

---

# Duration and Ticking

Persistent effects can define:

```gdscript
@export var duration: float = 0.0
@export var tick_interval: float = 1.0
```

`duration` defines how long an active effect remains relevant.

`tick_interval` provides the processing interval for effects such as:

* DOT
* HOT
* Other future periodic effects

The resource stores the configuration; runtime effect tracking and ticking remain the responsibility of the effect runtime systems.

---

# Stacking

Effects can define their own stacking rules:

```gdscript
@export var stack_group: String
@export var stack_behavior: StackBehavior
@export var duration_behavior: DurationBehavior
@export var max_stacks: int
```

`stack_group` identifies effects that should interact with one another.

Stack behavior determines whether a new application:

* Adds a stack
* Replaces the existing stack
* Is ignored
* Uses normal non-stacking behavior

Duration behavior independently determines whether the existing duration is:

* Kept
* Refreshed
* Added to
* Replaced

The actual resolution is handled by `EffectStackHandler`.

---

# Special Behaviors

Some effect types require additional configuration.

### Knockback

```gdscript
knockback_force
direction
```

Provides the force and optional direction used when applying knockback.

### Revive

```gdscript
revive_hp
revive_hp_percent

revive_mp
revive_mp_percent

revive_resource
revive_resource_percent
```

Allows revival effects to restore fixed or percentage-based values for HP, MP, and class resources.

---

# Resource Costs

Effects can also represent resource-payment requirements.

```gdscript
@export var is_cost_effect: bool
@export var cost_payment_mode: CostPaymentMode
```

Supported payment modes:

```text
NONE
REQUIRE_FULL
SPEND_AVAILABLE
```

Costs can be applied to:

* HP
* MP
* ClassResource

Each supports flat or percentage-based values.

`VitalComponent` performs the runtime validation and payment, while `StatEffect` defines the cost requirements.

HP costs are routed through the combat damage pipeline so that HP expenditure remains consistent with other damage operations.

---

# Runtime Helpers

Although primarily a data resource, `StatEffect` provides small static helpers for interpreting its configuration.

### `apply_stat_effect()`

Applies supported stat-based effect types to a `StatBlock` or compatible runtime target.

### `resolve_amount()`

Converts a configured flat or percentage amount into a resolved value.

### `resolve_damage_amount()`

Determines the initial damage amount according to the configured `DamageScaling`.

### `get_effect_type()`

Returns the configured `EffectType`.

### `get_display_name()`

Returns the localized display name, falling back to a formatted `effect_id` when no display name is provided.

---

# System Boundaries

### StatEffect owns

* Effect definition
* Effect classification
* Target and trigger configuration
* Stat selection
* Amount configuration
* Damage configuration
* Duration configuration
* Stack configuration
* Special behavior configuration
* Resource cost configuration

### StatEffect does not own

* Active-effect lifecycle
* Effect registration
* Effect ticking
* Combat mitigation
* Resistance calculations
* Character stat ownership
* UI
* Effect database management

Those responsibilities belong to the runtime systems consuming the resource.

---

# Summary

`StatEffect` is the framework's **unified effect definition resource**.

It provides one data structure capable of representing stat modifications, healing, damage, persistent effects, revival, knockback, costs, and future effect behaviors while allowing runtime systems to process those definitions through their appropriate pipelines.

```text
StatEffect
    │
    ├── StatManager     → stat modification
    ├── VitalComponent  → vitals / costs / healing / revive
    ├── CombatManager   → damage
    └── EffectManager   → runtime effect lifecycle
```

This keeps effect authoring centralized while allowing the actual gameplay behavior to remain distributed across the systems responsible for each operation.
