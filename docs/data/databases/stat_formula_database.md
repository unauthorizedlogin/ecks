# 🧮 Stat Formula Database System

The Stat Formula Database System provides the runtime lookup architecture for all stat conversion formulas used throughout the character stat system.

Formula definitions are generated as `.tres` resources from CSV data and loaded into a centralized runtime database, allowing derived stats, combat calculations, and progression systems to consume formulas through deterministic lookups rather than hardcoded logic.

The system is responsible for:

* loading generated stat formulas
* indexing formulas by ID
* organizing formulas by category
* organizing formulas by subcategory
* providing runtime formula lookups
* calculating derived stat bonuses
* loading resistance rule definitions

See:

* Stat System
* Character System
* Combat System
* Progression System

---

# 📦 Formula Database Architecture

The Stat Formula Database is built around three layers:

```
CSV Data
   ↓
Stat Formula Generator
   ↓
StatFormula (.tres)
   ↓
Formula Index
   ↓
FormulaResourceDatabase
   ↓
FormulaDatabase Runtime Access
```

Each formula exists as an individual resource containing:

* formula identifier
* source stat
* derived stat
* multiplier value
* category metadata
* subcategory metadata

---

# 📄 Stat Formula Resources

Each generated `StatFormula` resource represents one stat conversion rule.

Example:

```
DEXTERITY → DAMAGE
Multiplier: 0.5
```

Resources define relationships between:

* source attributes
* derived statistics
* scaling values

Supported formula categories include:

* vital stats
* damage calculations
* defensive calculations
* regeneration
* movement
* tactical stats
* ratings
* resistances

---

# 🗂️ Formula Organization

Generated formulas are organized using category and subcategory routing.

Example:

```
data/formulas/

├── defense/
│   ├── armor/
│   └── resistance/
│
├── damage/
│
├── regen/
│   ├── hp/
│   └── mana/
│
├── tactical/
│   ├── block/
│   ├── dodge/
│   └── sneak/
│
└── speed/
```

This structure keeps formulas easy to maintain while allowing the runtime database to access them through generated indexes.

---

# 🔎 Formula Runtime Index

The Formula Index Generator scans generated resources and creates an export-safe runtime lookup list.

The generated index provides:

* deterministic loading
* filesystem-independent discovery
* export compatibility
* fast resource initialization

Example:

```gdscript
const FORMULAS_ALL := [
    "res://.../formula_damage_str.tres",
    "res://.../formula_hp_regen.tres",
]
```

The runtime never searches directories directly.

---

# 🧠 FormulaResourceDatabase

`FormulaResourceDatabase` is responsible for loading and organizing all formula resources.

During initialization it:

1. Loads the generated formula index
2. Loads every `StatFormula` resource
3. Validates formula resources
4. Registers formulas by ID
5. Builds category indexes

Runtime storage:

```gdscript
formula_map
category_map
```

---

# 📚 Runtime Lookup

The database provides multiple access methods.

## Direct Formula Access

Lookup by unique formula ID:

```gdscript
get_formula("formula_damage_str")
```

Used for systems that require a specific known calculation.

---

## Category Access

Retrieve grouped formulas:

```gdscript
get_by_category("damage")
```

Returns all formulas belonging to a category.

---

## Subcategory Access

Retrieve specialized formula groups:

```gdscript
get_by_subcategory("tactical", "dodge")
```

Allows systems to process only relevant calculations.

---

# 📊 Derived Stat Calculation

The Formula Database can resolve stat bonuses dynamically.

The runtime:

1. Searches formulas matching the requested derived stat
2. Reads the required source attribute
3. Applies the formula multiplier
4. Returns the calculated contribution

Example:

```
DEXTERITY
      ↓
formula_damage_dex
      ↓
DAMAGE bonus
```

This allows derived stats to scale entirely through data.

---

# 🛡️ Resistance Rule Integration

The Formula Database also loads resistance rule resources.

These rules define global resistance behavior such as:

* resistance caps
* scaling rules
* mitigation boundaries

Resistance logic remains data-driven instead of being embedded into combat code.

---

# ⚙️ Initialization Pipeline

The runtime initialization process:

1. Creates FormulaResourceDatabase
2. Loads generated formula index
3. Loads all StatFormula resources
4. Loads resistance rules
5. Builds lookup dictionaries
6. Exposes formulas to gameplay systems

---

# 🔄 Reload Support

The database supports forced reloads for development and testing.

Reloading:

* clears existing formula data
* recreates the resource database
* reloads all formulas

Useful for:

* editor testing
* balance iteration
* formula tuning

---

# ✅ System Responsibilities

The Stat Formula Database System:

✅ Loads stat formula resources
✅ Provides runtime formula access
✅ Organizes formulas by category
✅ Organizes formulas by subcategory
✅ Calculates derived stat contributions
✅ Loads resistance rules
✅ Supports deterministic resource loading

---

# ❌ System Does Not

The Stat Formula Database System does **not**:

❌ Define base attributes
❌ Manage player stats directly
❌ Handle equipment bonuses
❌ Apply damage calculations
❌ Control combat events
❌ Manage leveling

Those responsibilities belong to:

* Stat Manager
* Equipment System
* Combat System
* Progression System

---

# 🚀 Data-Driven Stat Scaling

The Stat Formula Database allows the entire stat relationship layer to be balanced without code changes.

Designers can modify:

* stat scaling
* derived conversions
* defensive calculations
* regeneration values
* movement scaling

by updating formula data and regenerating resources.

This creates a scalable stat architecture where gameplay formulas remain configurable, deterministic, and production-ready.
