# 📜 Stat Formula Generator System

The Stat Formula Generator System provides a data-driven pipeline for creating all stat conversion formulas used throughout the game.

Stat formulas are authored through CSV data and automatically converted into optimized `.tres` resources, allowing character progression and derived stat calculations to be managed entirely through data rather than hardcoded logic.

The system is responsible for:

* stat formula resource generation
* source-to-derived stat mapping
* formula categorization
* multiplier configuration
* runtime formula indexing

See:

* Stat System
* Class System
* Combat System

---

## 📦 Stat Formula Resource Architecture

Each generated formula is represented by a `StatFormula`.

Formula resources contain:

* formula identity
* source stat
* derived stat
* conversion multiplier
* category
* subcategory

Stat formulas are fully data-driven and require no manual resource creation.

---

## 📂 Formula Organization

Formulas are automatically organized into category and subcategory folders during generation.

Example:

```text
system/entity/stats/data/formulas/
├── vitals/
│   ├── hp/
│   ├── mp/
│   └── resource/
│
├── damage/
│   ├── melee/
│   ├── ranged/
│   └── magic/
│
├── defense/
│   ├── armor/
│   ├── dodge/
│   └── block/
│
└── ratings/
```

This organization allows large formula libraries to remain easy to maintain while supporting future expansion.

---

## 🔄 Source Stat Mapping

Each formula defines a conversion from a source stat into a derived stat.

Supported source statistics include:

* Vitality
* Stamina
* Wisdom
* Strength
* Dexterity
* Intelligence
* Agility
* Fortitude
* Willpower
* Armor
* Block
* Dodge
* Barrier
* Sneak
* Speed
* Charm
* Concentration
* Luck
* All Resist
* Health
* Mana
* Class Resource

Each formula references exactly one source statistic.

---

## 📈 Derived Stat Generation

Generated formulas can produce derived gameplay statistics including:

* Health
* Mana
* Class Resource
* Health Regeneration
* Mana Regeneration
* Resource Regeneration
* Damage
* Critical Hit Chance
* Critical Damage
* Armor
* Block
* Dodge
* Barrier
* Sneak
* All Resist
* Movement Speed
* Attack Speed
* Cast Speed
* Attack Rating
* Defense Rating
* Soft Resistance Cap
* Charm
* Concentration
* Luck

Each derived value is calculated using the configured source stat and multiplier.

---

## ⚙️ Formula Configuration

Each formula stores the data required for runtime calculations.

Supported configuration includes:

* formula ID
* source stat
* derived stat
* multiplier
* category
* subcategory

This keeps progression balancing entirely data-driven and allows formulas to be tuned without modifying runtime code.

---

## 🔄 Generation Pipeline

The generation process:

1. Reads formula CSV definitions
2. Validates source and derived stat mappings
3. Creates `StatFormula` resources
4. Assigns category and subcategory metadata
5. Saves `.tres` resources
6. Rebuilds the formula index

---

## 🔎 Generated Formula Index

The Formula Index Generator scans all generated formula resources and builds a runtime lookup index.

For editor visibility, formulas are grouped by category and subcategory.

At runtime, all formulas are exposed through a single master index, providing:

* fast formula loading
* deterministic resource discovery
* filesystem-independent lookups
* scalable formula organization

The index is automatically rebuilt whenever formula generation runs.

---

## ✅ System Responsibilities

The Stat Formula Generator System:

✅ Creates stat formula resources
✅ Handles CSV → Resource conversion
✅ Maps source stats to derived stats
✅ Stores multiplier configuration
✅ Organizes formulas by category and subcategory
✅ Builds runtime indexes

The Stat Formula Generator System does **not**:

❌ Perform stat calculations
❌ Apply formulas to entities
❌ Modify stat values at runtime
❌ Handle leveling or progression logic
❌ Balance combat systems

Those responsibilities belong to:

* Stat System
* Class System
* Combat System
* Entity Stat Runtime
