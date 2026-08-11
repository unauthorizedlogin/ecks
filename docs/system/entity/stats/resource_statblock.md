# 📊 StatBlock Resource

The `StatBlock` Resource defines the complete statistical data model used by characters and other gameplay entities.

It stores base, derived, combat, resistance, speed, influential, and rating statistics in a single standardized structure. Runtime systems can duplicate and modify a `StatBlock` without requiring each entity type to define its own stat schema.

`StatBlock` is primarily a **data container**. Runtime stat calculation, equipment aggregation, and effect application are handled by the surrounding stat architecture.

---

## 🧱 Resource Definition

**Resource:** `resource_statblock.gd`
**Class:** `StatBlock`
**Type:** `Resource`

The resource contains:

* Stat identity
* Vital attributes
* Derived vitals
* Regeneration values
* Primary and secondary attributes
* Combat statistics
* Tactical statistics
* Resistances
* Speed statistics
* Influential statistics
* Derived ratings
* Internal stat snapshots and caches

---

## 🆔 Identity

Each StatBlock has a unique identifier:

```gdscript
@export var statblock_id: String = ""
```

The identifier allows stat blocks to be referenced by databases, generators, characters, equipment, and other systems.

---

## 📊 Stat Categories

### Vital Attributes

```text
Stamina
Vitality
Wisdom
```

These form the primary foundation for derived vital statistics.

### Derived Vitals

```text
HP
MP
ClassResource
```

These represent the entity's maximum runtime resources.

### Regeneration

```text
HPRegen
MPRegen
ResourceRegen
```

Regeneration values are consumed by `VitalComponent` during runtime regeneration ticks.

### Primary Attributes

```text
Strength
Dexterity
Intelligence
```

### Secondary Attributes

```text
Agility
Willpower
Fortitude
```

### Combat Attributes

```text
Damage
CritHit
CritDmg
```

### Tactical Attributes

```text
Armor
Block
Dodge
Sneak
Barrier
```

### Resistances

```text
AllResist
ArcaneResist
DarkResist
EarthResist
IceResist
FireResist
LightResist
LightningResist
PhysResist
PoisonResist
WaterResist
WindResist
```

### Speed

```text
Movement
AtkSpd
Speed
```

`AtkSpd` is stored as a floating-point value to support attack-speed scaling.

### Influential Attributes

```text
Charm
Concentration
Luck
```

### World Statistics

```text
GoldFind
MagicFind
XPGain
```

### Fully Derived Ratings

```text
Attack
Defense
```

These are generated values rather than primary authored attributes.

---

# 🔧 VALID_STATS

`VALID_STATS` defines the standardized list of statistics recognized by the StatBlock architecture.

```gdscript
const VALID_STATS = [...]
```

This list is used by internal stat storage and provides a common stat vocabulary for systems such as:

* Equipment
* Effects
* StatManager
* StatCalculator
* Character systems
* Difficulty scaling

Adding a new stat to the resource should therefore also include that stat in `VALID_STATS` when it needs to participate in the standardized stat pipeline.

---

# 💾 Base Stat Storage

StatBlock maintains an internal `_base_stats` dictionary.

```gdscript
var _base_stats := {}
```

The dictionary stores the resource's initial stat values and provides a stable reference for systems that need to distinguish base values from subsequently modified values.

Initialization calls:

```gdscript
store_base_stats()
```

which records all recognized statistics from `VALID_STATS`.

The resource also exposes:

```gdscript
get_stat_base(stat_name)
```

for retrieving the stored base value.

---

# 📸 Stat Snapshots

StatBlock maintains additional snapshots:

```gdscript
var base_snapshot: Dictionary = {}
var level_snapshot: Dictionary = {}
var equipment_snapshot := {}
```

These provide storage for separated sources of stat contribution.

The combined base/level value can be retrieved through:

```gdscript
get_base_total(stat)
```

This allows the broader stat architecture to maintain distinct sources before producing the final runtime values.

---

# 🧠 Runtime State

StatBlock contains internal runtime flags:

```gdscript
var is_dirty: bool = true
var lock_derived: bool = false
```

`is_dirty` indicates that the block may require rebuilding or recalculation.

`lock_derived` provides a mechanism for preventing derived-stat recalculation when required by the runtime stat pipeline.

These values are internal runtime state and are not intended to represent authored gameplay statistics.

---

# 📋 Property Validation

StatBlock provides:

```gdscript
has_property(stat_name)
```

to determine whether a requested statistic exists on the resource.

This allows generic systems to safely work with stat names without hardcoding property access for every statistic.

For example:

```gdscript
if stat_block.has_property("Damage"):
    ...
```

This is important for the effect and equipment systems, which operate against statistics by name.

---

# 📦 StatBlock Duplication

StatBlock provides:

```gdscript
duplicate_block()
```

to create an independent copy of the resource's stat properties.

The duplication process:

1. Creates a new `StatBlock`.
2. Iterates through the resource properties.
3. Excludes internal/resource engine properties.
4. Copies recognized stat properties.
5. Rebuilds the new block's base-stat cache.

This allows runtime entities to work with their own mutable StatBlock rather than modifying the original database resource.

---

# 🔗 Architecture Role

StatBlock sits at the center of the runtime statistics architecture but does not own the complete calculation pipeline.

```text
StatBlock
    │
    ├── Base Stats
    ├── Level Stats
    ├── Equipment Stats
    └── Runtime Stat Values
            │
            ▼
      StatManager
            │
            ▼
      StatCalculator
            │
            ▼
      Final Runtime Stats
            │
      ┌─────┴─────┐
      ▼           ▼
VitalComponent   Combat Systems
```

Effects and equipment can modify statistics through the broader stat-management architecture without requiring `StatBlock` itself to understand where those modifications originated.

---

# 🧩 Design Principle

`StatBlock` defines **what statistics exist**.

It does not define the gameplay behavior associated with those statistics.

For example:

* `HP` is a statistic.
* `HPRegen` is a statistic.
* `Damage` is a statistic.
* `FireResist` is a statistic.
* `AtkSpd` is a statistic.

The systems consuming those values determine how they affect gameplay.

This separation allows the StatBlock schema to remain reusable across players, NPCs, enemies, equipment, and future entity types.

---

# 🔮 Extensibility

The StatBlock is intentionally structured as an expandable common stat schema.

New statistics can be introduced without creating separate stat resources for each gameplay system, provided they are added to the StatBlock and the relevant calculation/runtime systems are updated to consume them.

This allows future systems to participate in the same statistical architecture rather than creating isolated modifier models.
