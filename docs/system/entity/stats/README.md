# 📊 Stat System

The Stat System provides the centralized framework for defining, modifying, calculating, and rebuilding runtime statistics.

The system separates **stat data**, **modification sources**, and **derived-stat formulas**, allowing equipment, effects, levels, classes, difficulty, and other gameplay systems to modify statistics through a common pipeline.

The system provides:

* Base and runtime statistics
* Equipment modifiers
* Effect modifiers
* Level-based stat values
* Derived statistics
* Data-driven stat formulas
* Resistance rules and caps
* Centralized stat rebuilding

---

# 🧠 Stat Architecture

```text
                    Stat System

                         |
                    StatManager
                         |
          ┌──────────────┼──────────────┐
          ↓              ↓              ↓
      StatBlock     StatFormula    ResistRules
          |              |              |
          ↓              ↓              ↓
     Base / Level   Derived Stats   Resistances
          |
    ┌─────┴─────┐
    ↓           ↓
Equipment     Effects
    |           |
    └─────┬─────┘
          ↓
    Combined Modifiers
          ↓
      StatCalculator
          ↓
    Final StatBlock
```

---

# 📦 Core Components

| Component         | Purpose                                                               | Documentation                        |
| ----------------- | --------------------------------------------------------------------- | ------------------------------------ |
| ⚙️ StatManager    | Central runtime authority for stat sources, modifiers, and rebuilding | [Stat Manager](stat_manager.md)      |
| 📊 StatBlock      | Stores the entity's base, level, and runtime statistics               | [StatBlock](resource_statblock.md)           |
| 🧮 StatFormula    | Defines how source statistics produce derived statistics              | [StatFormula](resource_stat_formula.md)       |
| 🛡️ ResistRules   | Defines resistance calculation rules and soft caps                    | [Resistance Rules](resource_resist_rules.md)  |
| 🧠 StatCalculator | Calculates derived statistics from the active StatBlock               | [StatCalculator](stat_calculator.md) |

---

# 🔄 Stat Pipeline

Statistics are rebuilt through a centralized layered pipeline.

```text
Base Stats
    ↓
Level Snapshot
    ↓
Equipment Sources
    ↓
Effect Sources
    ↓
Combined Modifiers
    ↓
Final Base/Modified Stats
    ↓
StatCalculator
    ↓
Derived Stats
    ↓
Runtime StatBlock
```

The system rebuilds statistics from their authoritative sources rather than incrementally modifying the final values.

This allows sources to be added, removed, or replaced without accumulating stale modifiers.

---

# 🧩 Modifier Layers

`StatManager` maintains separate modifier layers for persistent and runtime sources.

### Equipment

Persistent modifiers provided by equipped items.

```text
Equipment
    ↓
Equipment Sources
```

### Effects

Runtime modifiers provided by buffs, debuffs, passives, difficulty, and other effects.

```text
Effects
    ↓
Effect Sources
```

Both layers are combined during stat rebuilding.

---

# 🧮 Derived Statistics

Derived statistics are calculated after base, level, equipment, and effect values have been established.

`StatCalculator` uses `StatFormula` resources to determine how source statistics produce derived values.

Examples include:

* HP
* MP
* Class Resource
* Regeneration
* Damage
* Critical Hit
* Critical Damage
* Armor
* Block
* Dodge
* Barrier
* Movement
* Attack Speed
* Cast Speed
* Gold Find
* Magic Find
* XP Gain
* Attack
* Defense
* Resistances

---

# 📐 Stat Formulas

`StatFormula` provides the data-driven definition for derived-stat calculations.

A formula identifies:

* Formula ID
* Source statistic
* Derived statistic
* Multiplier

```text
Source Stat
     ↓
Multiplier
     ↓
Derived Stat
```

This allows derived-stat relationships to be configured as resources instead of being hardcoded into individual gameplay systems.

See [StatFormula](stat_formula.md) for the formula resource and [StatCalculator](stat_calculator.md) for runtime calculation.

---

# 🛡️ Resistance Rules

`ResistRules` defines shared resistance calculation rules.

Current configuration includes:

```gdscript
soft_cap_percent
```

The resistance system can therefore define common resistance behavior independently from individual entity statistics.

See [Resistance Rules](resist_rules.md).

---

# 🔗 System Integration

The Stat System provides the common runtime statistics layer for:

* 🧍 Character System
* 🧬 Class System
* ⚔️ Combat System
* ✨ Effect System
* 🛡️ Equipment System
* ⚡ Ability System
* 🎚️ Difficulty System
* 📈 XP System
* ❤️ Vital Systems

These systems provide data or modifiers while the Stat System remains responsible for assembling and calculating the resulting runtime statistics.

---

# 📌 Design Rule

**StatBlock stores the data. StatManager owns the layers. StatCalculator calculates derived values. StatFormula defines the relationships. ResistRules defines resistance behavior.**

This separation keeps stat calculation centralized and allows new modifiers and derived statistics to be added without distributing stat logic throughout gameplay systems.
