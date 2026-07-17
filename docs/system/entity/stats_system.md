# 📈 Stats System

## Oveviewer

- Data-driven actor stat architecture
- Shared stat identity across players, NPCs, enemies, and future actors
- Primary, derived, offensive, defensive, and resistance stats
- Flexible class and character progression support
- Resource-based stat definitions
- Extensible calculations for future mechanics

## StatManager

The StatManager is the authoritative stat calculation system responsible for rebuilding player statistics from multiple modifier layers.

It provides a centralized pipeline for:

- Base stat restoration
- Level-based stat application
- Equipment modifier application
- Runtime effect modifier application
- Derived stat calculation
- UI stat synchronization

Stat changes are never applied permanently to the base stat block.

Instead, all modifiers are stored as separate sources and rebuilt through a deterministic calculation pipeline.

---

# 🧠 Architecture Role

StatManager acts as the bridge between:

```

EquipmentManager
|
|
EffectManager
|
v
StatManager
|
v
StatBlock
|
v
Derived Stats / UI

````

StatManager does not own:

- Equipment logic
- Buff/debuff duration
- Item validation
- Effect triggering

It only calculates the final resulting stat state.

---

# 🧩 Layered Modifier System

Stat calculations are separated into two independent modifier layers.

## 🛡️ Equipment Layer

Persistent modifiers from equipped items.

Examples:

- Weapons
- Armor
- Accessories
- Permanent gear bonuses

Stored through:

```gdscript
equipment_sources
````

Structure:

```text
StatBlock
 |
 └── Equipment Sources
       |
       ├── Weapon
       ├── Helmet
       └── Ring
```

---

## ⚡ Effect Layer

Temporary runtime modifiers.

Examples:

* Buffs
* Debuffs
* Potions
* Skills
* Status effects

Stored through:

```gdscript
effect_sources
```

Structure:

```text
StatBlock
 |
 └── Effect Sources
       |
       ├── Poison
       ├── Strength Buff
       └── Armor Reduction
```

---

# 🔄 Stat Rebuild Pipeline

All stat changes flow through:

```gdscript
rebuild_stats()
```

Pipeline:

```
RESET BASE STATS
        |
        v
APPLY LEVEL SNAPSHOT
        |
        v
MERGE EQUIPMENT MODIFIERS
        |
        v
MERGE EFFECT MODIFIERS
        |
        v
CALCULATE DERIVED STATS
        |
        v
UPDATE UI
```

---

# 🔒 Rebuild Protection

StatManager prevents recursive rebuild loops.

Protection systems:

* Active rebuild lock
* Rebuild queue
* Deferred processing

Example:

```
Equipment Change
        |
        v
Request Rebuild
        |
        v
Already rebuilding?
        |
        +---- Yes → Queue
        |
        +---- No → Execute
```

This prevents:

* Duplicate rebuilds
* Recursive stat updates
* Partial stat states

---

# 🛡️ Equipment Integration

EquipmentManager provides:

```gdscript
set_equipment_source()
```

Example:

```gdscript
StatManager.set_equipment_source(
    "equipment",
    modifiers,
    player.stat_block
)
```

Equipment changes:

1. Remove previous equipment layer
2. Merge current equipment modifiers
3. Request stat rebuild
4. Apply final values

Equipment remains the single source of permanent gear bonuses.

---

# ⚡ Effect Integration

Runtime effects provide:

```gdscript
set_effect_source()
```

Example:

```gdscript
StatManager.set_effect_source(
    "rage_buff",
    modifiers,
    player.stat_block
)
```

Effects can:

* Add flat bonuses
* Add percentage bonuses
* Override values

When removed:

```gdscript
remove_effect_source()
```

The stat system rebuilds automatically.

---

# 🧮 Modifier Combination

All active sources are merged into one calculation set.

Example:

```
Equipment:

Damage +20


Buff:

Damage +25%


Final:

(Base + 20) * 1.25
```

---

# Modifier Formats

## Flat Modifier

```gdscript
{
    "damage": 10
}
```

Becomes:

```gdscript
{
    "flat": 10
}
```

---

## Structured Modifier

```gdscript
{
    "damage": {
        "flat": 10,
        "percent": 0.15
    }
}
```

Result:

```
(Base + 10) * 1.15
```

---

## Override Modifier

```gdscript
{
    "speed": {
        "override": 100
    }
}
```

Overrides normal calculation.

---

# 📈 Derived Stat Calculation

After all modifiers are applied:

```gdscript
stat_block.calculate_derived_stats()
```

runs.

This allows stats such as:

* Damage scaling
* Defense calculations
* Resistances
* Ratings
* Secondary attributes

to always calculate from the final modified values.

---

# 📡 Stat Update Signals

After rebuild:

```gdscript
stats_rebuilt(stat_block)
```

is emitted.

Listeners can update:

* Character panels
* Equipment screens
* Combat displays
* Stat windows

---

# 🖥️ UI Synchronization

After rebuilding:

```gdscript
player_stats_ui.update_stats_ui()
```

is called when available.

This keeps displayed values synchronized with the calculated state.

---

# 🧱 Design Rules

## Single Source of Truth

Permanent stats are never directly modified by equipment or effects.

Instead:

```
Source Modifier
        |
        v
StatManager
        |
        v
Calculated StatBlock
```

---

## No Incremental Stat Drift

The system avoids:

* Adding bonuses repeatedly
* Forgetting removed modifiers
* Stale temporary effects

Every rebuild starts from:

```
Base Stats
+
Level Snapshot
+
Current Modifier Layers
```

---

# Final Architecture

```
                 EquipmentManager
                       |
                       v
              Equipment Sources
                       |
                       |
EffectManager ---> Effect Sources
                       |
                       v

                 StatManager

                       |
                       v

                  StatBlock

                       |
                       v

              Derived Statistics
                       |
                       v

                     UI
```

The result is a deterministic layered stat engine where equipment, buffs, debuffs, and future systems can safely modify player power without corrupting the underlying stat state.

```

# 📊 StatBlock Resource Architecture

## 🧠 CORE SYSTEM: STAT DATA FOUNDATION

`StatBlock` is the primary data resource that defines all character, enemy, and entity statistics.

It acts as the central container for:

- Base attributes
- Derived statistics
- Equipment calculations
- Level scaling
- Runtime stat rebuilding
- Formula-driven stat generation
- Combat ratings

The `StatBlock` does **not** manage gameplay logic.

It is a pure data/model layer consumed by:

- `StatManager`
- Combat systems
- Equipment systems
- UI stat displays
- Formula databases
- Character progression systems


---

# 🧩 ARCHITECTURE ROLE

```

Class / Entity Data
|
▼
StatBlock
|
▼
StatManager
|
├── Equipment Modifiers
├── Effect Modifiers
├── Level Scaling
└── Derived Calculations
|
▼
Final Runtime Stats

```

---

# 🧱 STAT LAYERS

The StatBlock system separates statistics into multiple categories.

## Base Statistics

Directly assigned values from:

- Class definitions
- Character creation
- Enemy templates
- Progression systems


Examples:

- Strength
- Dexterity
- Intelligence
- Vitality
- Wisdom
- Stamina


---

# ❤️ Vital Statistics

## Base Vitals

| Stat | Purpose |
|---|---|
| Stamina | Physical endurance scaling |
| Vitality | Health scaling |
| Wisdom | Resource scaling |

## Derived Vitals

| Stat | Purpose |
|---|---|
| HP | Maximum health |
| MP | Maximum mana/resource pool |
| ClassResource | Class-specific resource |

Derived through:

- Base values
- Level growth
- Equipment
- Formula database bonuses


---

# 🔥 Regeneration Statistics

Controls resource recovery.

| Stat | Purpose |
|---|---|
| HPRegen | Health regeneration |
| MPRegen | Mana regeneration |
| ResourceRegen | Class resource regeneration |

---

# ⚔️ Primary Statistics

Main offensive attributes.

| Stat | Purpose |
|---|---|
| Strength | Physical scaling |
| Dexterity | Precision/agility scaling |
| Intelligence | Magical scaling |

---

# 🛡 Secondary Statistics

Additional character attributes.

| Stat | Purpose |
|---|---|
| Agility | Movement/evasion scaling |
| Willpower | Mental resistance/resource scaling |
| Fortitude | Defensive scaling |

---

# ⚔️ Damage Statistics

Combat damage modifiers.

| Stat | Purpose |
|---|---|
| Damage | Base damage output |
| CritHit | Critical chance rating |
| CritDmg | Critical damage multiplier |

Critical values use rating conversion:

```

Rating → Percentage

```

Example:

```

100 CritHit = 100% rating conversion

```

---

# 🛡 Tactical Statistics

Combat defensive/offensive utility stats.

| Stat | Type |
|---|---|
| Armor | Derived defense |
| Block | Chance rating |
| Dodge | Chance rating |
| Sneak | Stealth rating |
| Barrier | Damage prevention rating |

---

# 🔥 Resistance Statistics

Elemental and physical mitigation.

| Stat |
|-|
| IceResist |
| FireResist |
| LightResist |
| PhysResist |
| AllResist |

Resistance system:

- Uses rating values
- Converts into percentage mitigation
- Applies global resistance rules
- Supports hard caps and soft caps


Example:

```

1000 Resistance Rating = 100%

```

Runtime cap:

```

75% maximum effective resistance

```

---

# ⚡ Speed Statistics

Controls action timing and movement.

| Stat | Purpose |
|---|---|
| Movement | Movement scaling |
| Speed | General speed rating |
| AtkSpd | Attack speed |
| CastSpd | Casting speed |

---

# 💬 Influential Statistics

Social and utility attributes.

| Stat |
|-|
| Charm |
| Concentration |
| Luck |

Used by:

- Dialogue systems
- Rewards
- Chance systems
- Formula calculations


---

# ⭐ Fully Derived Ratings

High-level combat summaries.

## Attack Rating

Calculated from:

- Expected damage output
- Attack speed
- Cast speed


## Defense Rating

Calculated from:

- Armor
- Block
- Dodge
- Resistance


These are display/comparison statistics.

---

# 🔄 Runtime Stat Pipeline

## Initialization

When created:

```

StatBlock
|
▼
store_base_stats()
|
▼
_cache original values

```

The original values become the permanent baseline.


---

# ♻️ Rebuilding Statistics

Runtime rebuilding follows this order:

```

Reset To Base
|
▼
Apply Level Growth
|
▼
Apply Equipment Layer
|
▼
Apply Effect Layer
|
▼
Calculate Derived Stats
|
▼
Emit stats_rebuilt

```

---

# 📦 Equipment Integration

StatBlock supports equipment-driven modifiers through:

```

equipment_snapshot

```

Equipment can modify:

- Attributes
- Damage
- Defense
- Resistances
- Speed
- Ratings


---

# 🧪 Effect Integration

Temporary gameplay modifiers use:

```

effect_sources

```

Examples:

- Buffs
- Debuffs
- Potions
- Status effects
- Temporary abilities


---

# 🧮 Formula Driven Calculations

Derived statistics are not hardcoded values.

They are calculated through:

```

FormulaDatabase
|
▼
StatFormula
|
▼
StatBlock.calculate_derived_stats()

```

Supports:

- Class scaling
- Equipment scaling
- Level scaling
- Future balance changes


---

# 📚 Equipment Validation

`VALID_STATS` provides the approved stat pool for:

- Equipment generation
- Affix generation
- Item modifiers
- Loot systems


Supported modifiers include:

- Attributes
- Damage
- Defense
- Resistances
- Speed
- Combat ratings


---

# 🧬 StatBlock Duplication

StatBlocks support deep copying.

Used for:

- Character templates
- Enemy instances
- Runtime copies
- Save/load operations


Process:

```

Original StatBlock
|
▼
duplicate_block()
|
▼
Independent Runtime Copy

````

---

# 🔍 Utility Functions

## Rating Conversion

```gdscript
rating_to_percent()
````

Converts ratings into usable percentages.

---

## Display Helpers

Examples:

```gdscript
get_crit_display()
get_resist_percent_display()
```

Used by UI layers.

---

## Property Validation

```gdscript
has_property()
```

Allows safe dynamic stat access.

---

# 🧠 Design Goals

The StatBlock system provides:

✅ Centralized stat ownership
✅ Data-driven formulas
✅ Equipment compatibility
✅ Temporary effect support
✅ Derived stat rebuilding
✅ Class scalability
✅ Enemy/player shared architecture
✅ Future-proof RPG stat expansion

---

# Final Architecture Summary

```
              StatBlock
                  |
 ┌────────────────┼────────────────┐
 │                │                │
Base Stats    Snapshots       Derived Stats
 │                │                │
 │                │                │
Class Data   Equipment      Formula Database
              Effects
              Levels
                  |
                  ▼
            Runtime Stats
                  |
                  ▼
        Combat / UI / Gameplay
```

`StatBlock` is the foundation layer that allows every entity in the RPG framework to share the same scalable stat architecture.

```

# 🧮 StatFormula CSV Generator

## 🧠 CORE SYSTEM: FORMULA RESOURCE GENERATION PIPELINE

`StatFormulaCSVGenerator` is an editor-side generation tool responsible for converting spreadsheet-style formula definitions into runtime `.tres` resources.

It provides a data-driven pipeline for creating:

- Stat scaling formulas
- Derived stat calculations
- Class growth relationships
- Balance tuning resources

Instead of manually creating individual `StatFormula` resources, formulas are authored through CSV data and automatically generated into the framework's resource structure.


---

# 🧩 ARCHITECTURE ROLE

This is a **development/editor pipeline tool**.

It does not execute combat calculations.

Its responsibility is:

```

CSV Definition
|
▼
StatFormulaCSVGenerator
|
▼
StatFormula Resources (.tres)
|
▼
Formula Database Index
|
▼
Runtime Stat Calculations

```

Runtime systems consume the generated resources through:

- `FormulaDatabase`
- `StatBlock`
- `StatManager`


---

# 📂 INPUT DATA SOURCE

The generator reads:

```

res://system/entity/stats/tools/formula_strings.csv

```

The CSV acts as the authoring layer for formulas.

Each row defines:

| Column | Purpose |
|---|---|
| Formula ID | Unique formula identifier |
| Category | Formula grouping |
| Subcategory | Formula organization |
| Source Stat | Attribute used as input |
| Derived Stat | Output stat generated |
| Multiplier | Scaling value |


Example structure:

```

formula_id,
category,
subcategory,
source_stat,
derived_stat,
multiplier

````


---

# ⚙️ GENERATION PROCESS

## 1. Editor Initialization

The generator runs automatically through:

```gdscript
@tool
````

It only executes inside the Godot editor.

Runtime builds are ignored.

Flow:

```
Editor Loads Script
        |
        ▼
_enter_tree()
        |
        ▼
generate()
```

---

# 📁 Output Structure

Generated formulas are organized automatically:

```
res://system/entity/stats/data/formulas/

    Category/

        Subcategory/

            formula_id.tres
```

Example:

```
formulas/

    combat/

        damage/

            strength_damage.tres

    defense/

        armor/

            vitality_armor.tres
```

This keeps formula resources organized by purpose rather than storing everything in a single directory.

---

# 🧬 Enum Validation

The generator validates CSV values against the framework enums.

## Source Validation

Uses:

```gdscript
StatFormula.SourceStat
```

Ensures every source stat exists.

Examples:

* Strength
* Dexterity
* Intelligence
* Vitality

---

## Derived Validation

Uses:

```gdscript
StatFormula.DerivedStat
```

Ensures every output stat exists.

Examples:

* HP
* Damage
* Armor
* Resistances
* Attack Speed

Invalid entries are skipped with warnings.

---

# 🏗 Resource Creation

For each valid CSV row:

A new:

```gdscript
StatFormula.new()
```

resource is created.

The generator assigns:

```gdscript
formula_id
category
subcategory
source_stat
derived_stat
multiplier
```

The completed resource is saved as:

```
formula_id.tres
```

---

# 🔄 Automatic Index Rebuild

After generating resources, the generator triggers:

```
formula_index_generator.gd
```

The index rebuild ensures newly created formulas are immediately discoverable.

Pipeline:

```
CSV Update
      |
      ▼
Generate .tres Resources
      |
      ▼
Rebuild Formula Index
      |
      ▼
FormulaDatabase Loads Updated Data
```

---

# 📚 Integration With Stat System

Generated formulas feed directly into:

```
StatBlock.calculate_derived_stats()
```

Example flow:

```
Strength
    |
    ▼
StatFormula
    |
    ▼
FormulaDatabase
    |
    ▼
Damage Calculation
    |
    ▼
Updated StatBlock.Damage
```

---

# 🎯 Design Goals

The CSV generator provides:

✅ Spreadsheet-driven stat balancing
✅ No manual resource creation
✅ Automatic folder organization
✅ Enum-safe formula references
✅ Runtime-ready `.tres` generation
✅ Automatic database indexing
✅ Scalable RPG formula architecture

---

# 🧠 Final Architecture Summary

```
        Designer
           |
           ▼
     formula_strings.csv
           |
           ▼
 StatFormulaCSVGenerator
           |
           ▼
     StatFormula.tres
           |
           ▼
 Formula Index Generator
           |
           ▼
   FormulaDatabase
           |
           ▼
     StatBlock System
           |
           ▼
     Character Stats
```

`StatFormulaCSVGenerator` provides the authoring bridge between spreadsheet balance design and the runtime stat calculation system.

```


