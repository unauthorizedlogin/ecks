# 📊 Stat Manager

## Overview

The **Stat Manager** is the central authority for rebuilding runtime statistics from layered stat sources.

It separates persistent equipment modifiers from temporary effect modifiers, combines those layers with base and level-derived values, and produces the final runtime values stored on a `StatBlock`.

The manager also coordinates derived-stat calculation through `StatCalculator`.

```text
Base Stats
    +
Level Snapshot
    +
Equipment Sources
    +
Effect Sources
    ↓
StatManager
    ↓
Final Stat Values
    ↓
StatCalculator
    ↓
Derived Stats
```

---

# 🧠 Core Responsibilities

The Stat Manager provides:

* Stat initialization
* Equipment modifier management
* Effect modifier management
* Layered modifier combination
* Stat rebuild requests
* Deferred rebuild processing
* Base stat restoration
* Level stat application
* Equipment snapshots
* Final stat calculation
* Derived stat calculation
* Stat rebuild signaling

The manager does not define base stat data or derived-stat formulas itself.

Base values are owned by `StatBlock`, while derived-stat formulas are owned by `StatCalculator`.

---

# ⚙️ Initialization

```gdscript
func initialize()
```

Registers the Stat Manager as an active framework system.

Prevents duplicate initialization.

Output:

```text
📊 StatManager initialized
```

---

# 🧩 Layered Stat Architecture

The Stat Manager maintains two independent runtime modifier layers:

```text
equipment_sources
effect_sources
```

### Equipment Layer

Contains persistent modifiers originating from equipped items.

```text
Equipment
    ↓
StatManager
    ↓
equipment_sources
```

### Effect Layer

Contains runtime modifiers originating from active effects.

```text
StatEffect
    ↓
EffectManager
    ↓
effect_sources
```

The layers remain independently managed while being combined during stat rebuilding.

---

# 🧱 Stat Source Structure

Both modifier layers are organized by the target `StatBlock` instance.

```text
StatBlock Instance ID
        ↓
┌──────────────────────────────┐
│ Source ID                    │
│   └── Stat Modifiers         │
└──────────────────────────────┘
```

This allows multiple independent sources to modify the same target without overwriting one another.

A source may provide:

```text
flat
percent
override
```

---

# ⚔️ Equipment Layer

```gdscript
func set_equipment_source(
    source_id: String,
    modifiers: Dictionary,
    target: StatBlock
)
```

Registers or replaces an equipment modifier source for a target.

Equipment sources are stored separately from temporary effects and trigger a stat rebuild whenever they change.

---

## 🧹 Equipment Source Removal

```gdscript
func remove_equipment_source(
    source_id: String,
    target: StatBlock
)
```

Removes a specific equipment source.

If no equipment sources remain for the target, its equipment source container is removed.

---

## 🧹 Clear Equipment

```gdscript
func clear_equipment(target: StatBlock)
```

Removes all equipment sources belonging to a target and requests a stat rebuild.

This provides a complete equipment-layer reset without affecting active effects.

---

# ⚡ Effect Layer

```gdscript
func set_effect_source(
    source_id: String,
    modifiers: Dictionary,
    target: StatBlock
)
```

Registers or replaces a runtime effect modifier source.

Effect sources are maintained independently from equipment sources.

```text
EffectManager
      ↓
set_effect_source()
      ↓
effect_sources
      ↓
request_rebuild()
```

This allows temporary buffs, debuffs, overrides, and other runtime stat effects to modify a `StatBlock` without altering its base data.

---

# 🧹 Effect Source Removal

```gdscript
func remove_effect_source(
    source_id: String,
    target: StatBlock
)
```

Removes one runtime effect source and requests a rebuild.

This is used when an active effect expires or is forcibly removed.

---

# 🧹 Clear Effects

```gdscript
func clear_effects(target: StatBlock)
```

Removes every effect source associated with a target.

Equipment sources remain untouched.

---

# 🧮 Modifier Combination

```gdscript
func get_combined_modifiers(
    stat_block: StatBlock
) -> Dictionary
```

Combines the equipment and effect layers into a single modifier set.

```text
Equipment Sources
       ↓
       ├─────────┐
       ↓         │
Effect Sources  │
       ↓         │
       └────┬────┘
            ↓
   Combined Modifiers
```

Each stat produces a normalized modifier structure:

```text
{
    "flat": 0.0,
    "percent": 0.0,
    "override": null
}
```

---

# ⚡ Effect-Only Modifier Query

```gdscript
func get_effect_modifiers(
    stat_block: StatBlock
) -> Dictionary
```

Returns only modifiers originating from the effect layer.

This allows systems to inspect runtime effect contributions without including equipment modifiers.

---

# 🔀 Modifier Merging

```gdscript
func _merge_into(
    final: Dictionary,
    source: Dictionary
)
```

Normalizes individual source entries into the combined modifier structure.

Supported formats include:

* Legacy numeric modifiers
* Flat modifiers
* Percentage modifiers
* Override modifiers

Legacy numeric entries are interpreted as flat modifiers.

---

# 🔒 Stat Overrides

An override takes precedence over normal flat and percentage calculations.

```text
Override Present
      ↓
Use Override Value
      ↓
Skip Normal Modifier Calculation
```

This allows effects or equipment sources to explicitly define a final stat value rather than modifying the existing value.

---

# 🔄 Rebuild System

```gdscript
func request_rebuild(
    stat_block: StatBlock
)
```

Requests that a target `StatBlock` be rebuilt.

The manager prevents duplicate entries in the rebuild queue.

```text
Modifier Changed
      ↓
request_rebuild()
      ↓
Rebuild Queue
      ↓
Deferred Processing
```

Rebuilds are deferred through the engine rather than being performed immediately by every source update.

This allows multiple modifier changes to enter the queue before the rebuild pipeline executes.

---

# 🔁 Rebuild Queue

```gdscript
func _process_rebuild_queue()
```

Processes queued `StatBlock` instances.

The queue tracks each block independently and rebuilds each queued block once per processing cycle.

```text
Rebuild Requests
      ↓
┌─────────────────┐
│ StatBlock A     │
│ StatBlock B     │
│ StatBlock C     │
└─────────────────┘
      ↓
rebuild_stats()
```

The manager uses internal rebuild state to prevent recursive rebuild processing.

---

# 🧮 Core Stat Rebuild Pipeline

```gdscript
func rebuild_stats(
    blocks: Array[StatBlock]
)
```

The rebuild pipeline reconstructs runtime stats from their authoritative layers.

```text
StatBlock
    ↓
Reset Base
    ↓
Apply Level Snapshot
    ↓
Build Equipment Snapshot
    ↓
Combine Equipment + Effects
    ↓
Apply Modifiers
    ↓
Calculate Derived Stats
    ↓
Emit stats_rebuilt
```

---

# 🔄 Base Stat Reset

Every rebuild begins by restoring the `StatBlock` to its base values.

```text
Base Stats
    ↓
StatBlock Runtime Values
```

This ensures previous modifier results are not accumulated across rebuilds.

---

# 📈 Level Snapshot

The manager then applies the target's `level_snapshot`.

```text
Base Value
    +
Level Snapshot
    ↓
Pre-Modifier Value
```

Level progression therefore remains separate from equipment and effect sources.

---

# 🧩 Equipment Snapshot

During rebuilding, the manager generates an `equipment_snapshot` on the `StatBlock`.

This provides a direct representation of the equipment layer's contribution.

```text
Equipment Sources
      ↓
equipment_snapshot
```

Flat equipment modifiers are accumulated while equipment overrides replace the corresponding snapshot value.

---

# 🧮 Final Modifier Calculation

For normal modifiers, the manager calculates:

```text
Final =
(
    Base
    + Level
    + Flat Modifiers
)
×
(1 + Percent Modifiers)
```

For overrides:

```text
Final = Override
```

The resulting value is written directly to the corresponding `StatBlock` property.

```text
Base Stats
    +
Level
    +
Flat
    ↓
Subtotal
    ×
Percent
    ↓
Final Runtime Stat
```

---

# 📊 Derived Statistics

After primary statistics have been rebuilt, the manager delegates derived-stat calculation to `StatCalculator`.

```gdscript
StatCalculator.calculate_derived_stats(stat_block)
```

The Stat Manager therefore establishes the correct primary-stat state before derived statistics are calculated.

```text
Primary Stats
      ↓
StatManager
      ↓
StatCalculator
      ↓
Derived Stats
```

The Stat Manager does not contain the formulas for derived statistics.

---

# 🔔 Stat Rebuild Signal

```gdscript
signal stats_rebuilt(stat_block)
```

The signal is emitted after a `StatBlock` has completed its rebuild and derived-stat calculation.

```text
Rebuild Complete
      ↓
stats_rebuilt
      ↓
Dependent Systems
```

This provides a synchronization point for systems that need to respond to finalized runtime statistics.

---

# 🧭 System Boundaries

| Responsibility                  | System                     |
| ------------------------------- | -------------------------- |
| Base stat storage               | `StatBlock`                |
| Level stat contribution         | `StatBlock.level_snapshot` |
| Equipment modifier registration | `StatManager`              |
| Effect modifier registration    | `StatManager`              |
| Effect lifecycle                | `EffectManager`            |
| Modifier combination            | `StatManager`              |
| Runtime stat rebuilding         | `StatManager`              |
| Derived stat formulas           | `StatCalculator`           |
| Effect source creation          | `EffectManager`            |
| Equipment source creation       | Equipment System           |
| Final stat storage              | `StatBlock`                |

This keeps the Stat Manager focused on **layering and reconstruction**, rather than owning the systems that create the underlying data.

---

# 🔗 System Relationships

```text
                    StatBlock
                       │
              ┌────────┴────────┐
              ↓                 ↓
        Base / Level       Runtime State
              │
              ↓
        ┌───────────────┐
        │ StatManager   │
        └───────┬───────┘
                │
       ┌────────┴────────┐
       ↓                 ↓
Equipment Sources   Effect Sources
       │                 │
       └────────┬────────┘
                ↓
       Combined Modifiers
                ↓
        Final Primary Stats
                ↓
        StatCalculator
                ↓
        Derived Statistics
```

---

# 🌱 Layered Stat Architecture

The Stat Manager intentionally separates **persistent equipment state** from **temporary runtime effect state**.

```text
                StatBlock
                   │
        ┌──────────┴──────────┐
        ↓                     ↓
 Equipment Layer        Effect Layer
        ↓                     ↓
 Persistent             Temporary
 Modifiers              Modifiers
        └──────────┬──────────┘
                   ↓
           Combined Modifiers
                   ↓
             Stat Rebuild
                   ↓
             Runtime Stats
```

This prevents temporary effects from modifying equipment data and prevents equipment changes from becoming indistinguishable from runtime buffs or debuffs.

---

# 🚀 Framework Integration

The Stat Manager is initialized as part of the framework's centralized launch flow.

Systems interact with the manager through explicit source APIs rather than directly modifying runtime stat values.

Examples:

```text
Equipment System
      ↓
set_equipment_source()

EffectManager
      ↓
set_effect_source()

EffectManager
      ↓
remove_effect_source()

Systems
      ↓
request_rebuild()
```

This establishes the Stat Manager as the centralized authority for layered runtime stat reconstruction.

---

# ✅ Design Rule

**StatManager is the central authority for rebuilding runtime statistics from layered stat sources.**

`StatBlock` owns **the stat data and base values**.

Equipment provides **persistent modifiers**.

Effects provide **runtime modifiers**.

`StatManager` combines those layers and rebuilds the runtime stat state.

`StatCalculator` determines **derived statistics**.

Future stat-affecting systems should register their contributions as explicit sources rather than directly modifying finalized `StatBlock` values.
