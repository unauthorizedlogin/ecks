# 📐 StatFormula Resource

The `StatFormula` Resource defines a data-driven relationship between a source statistic and a derived statistic.

It provides the stat calculation layer with a standardized way to describe how one statistic contributes to another without hardcoding every relationship directly into the calculation system.

---

## 🧱 Resource Definition

**Resource:** `stat_formula.gd`
**Class:** `StatFormula`
**Type:** `Resource`

A StatFormula contains:

* Formula identity
* Source statistic
* Derived statistic
* Scaling multiplier
* Generator routing information

---

## 🆔 Identity

Each formula has a unique identifier:

```gdscript
@export var formula_id: String = ""
```

The ID allows formulas to be generated, indexed, referenced, and retrieved independently of the runtime calculation logic.

---

# 🔗 Source → Derived Relationship

The core purpose of `StatFormula` is to define:

```text
Source Stat
      │
      ▼
  Multiplier
      │
      ▼
Derived Stat
```

For example:

```text
STAMINA
   ×
Multiplier
   ↓
HP
```

The resource does not perform the calculation itself. It stores the data required by the stat calculation layer to perform it.

---

# 📥 SourceStat

`SourceStat` defines which existing statistic contributes to the formula.

Supported source categories include:

### Vital Stats

```text
STAMINA
VITALITY
WISDOM
```

### Derived Vitals

```text
HP
MP
CLASS_RESOURCE
```

### Primary Stats

```text
DEXTERITY
INTELLIGENCE
STRENGTH
```

### Secondary Stats

```text
AGILITY
WILLPOWER
FORTITUDE
```

### Tactical Stats

```text
ARMOR
BLOCK
DODGE
SNEAK
BARRIER
```

### Resistance

```text
ALL_RESIST
```

### Speed

```text
SPEED
```

### Influential Stats

```text
CHARM
CONCENTRATION
LUCK
```

The source enum provides a controlled vocabulary rather than relying on arbitrary string references.

---

# 📤 DerivedStat

`DerivedStat` identifies the statistic being generated or modified by the formula.

Supported categories include:

* HP
* MP
* Class Resource
* HP / MP / Resource Regen
* Damage
* Critical Hit
* Critical Damage
* Armor
* Block
* Dodge
* Sneak
* Barrier
* All Resistance
* Movement
* Attack Speed
* Cast Speed
* Gold Find
* Magic Find
* XP Gain
* Charm
* Concentration
* Luck
* Attack
* Defense
* Resistance Soft Cap

This allows multiple derived statistics to use the same formula architecture.

---

# ✖️ Multiplier

```gdscript
@export var multiplier: float = 0.0
```

The multiplier defines how strongly the source statistic contributes to the derived statistic.

Conceptually:

```text
derived contribution =
    source value × multiplier
```

The actual aggregation and calculation are handled by the stat calculation system.

---

# 🧮 Calculation Architecture

`StatFormula` is intentionally a **data resource**, not a calculation manager.

The architecture separates formula definition from formula execution:

```text
StatFormula Resource
        │
        │ defines
        ▼
Source → Multiplier → Derived
        │
        ▼
StatCalculator
        │
        ▼
StatBlock
```

This allows formula definitions to be changed or expanded without restructuring the underlying calculation code.

---

# 🧩 Formula Composition

Multiple formulas can contribute to the same derived statistic.

For example:

```text
STAMINA × 10 → HP
VITALITY × 5 → HP
```

The calculation system can evaluate both formulas and combine their contributions when rebuilding the final StatBlock.

This keeps individual relationships small and composable rather than requiring one large hardcoded formula for every statistic.

---

# 🔄 Relationship to StatBlock

`StatBlock` defines the available statistics.

`StatFormula` defines relationships between those statistics.

```text
StatBlock
├── Stamina
├── Vitality
├── Strength
├── Damage
├── HP
└── ...

        ▲
        │
   StatFormula
        │
        ▼
Source Stat → Derived Stat
```

The StatBlock therefore remains the actual statistical data structure, while StatFormula provides the configuration used to derive values within that structure.

---

# 🛠️ Generator Routing

The resource also contains:

```gdscript
var folder: String = ""
var subfolder: String = ""
```

These fields are used by the data-generation pipeline to determine where generated formula resources belong.

They are organizational metadata rather than part of the runtime mathematical calculation.

---

# 🔮 Extensibility

The formula architecture is designed to expand as additional stat relationships are introduced.

Future formula features can be added without changing the fundamental resource relationship:

```text
Source Statistic
      ↓
Formula Configuration
      ↓
Derived Statistic
```

The currently defined `multiplier` provides the basic scaling model, while the resource leaves room for additional calculation modes such as flat additions or alternate scaling behavior.

Currently commented fields indicate this planned direction:

```gdscript
# add_flat
# scale_type
```

These can be introduced later if the calculation architecture requires more complex formula definitions.
