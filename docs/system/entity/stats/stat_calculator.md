# 🧮 StatCalculator Architecture

## Overview

`StatCalculator` is the centralized calculation layer responsible for transforming a `StatBlock`'s raw stat sources into its finalized derived statistics.

It is implemented as a `RefCounted` utility class containing static calculation methods. It does not own stat data, effect state, equipment state, or formula definitions. Instead, it operates on an existing `StatBlock` using the data assembled by `StatManager` and the formula definitions provided by `FormulaDatabase`.

The calculator establishes a deterministic stat-resolution pipeline:

**Raw Stat Sources → Formula Resolution → Effect Modifiers → Derived Statistics → Ratings**

This separates **stat calculation** from **stat ownership and state management**.

---

## Architectural Role

`StatCalculator` exists to provide a single calculation authority for derived statistics.

Its responsibilities are:

* Calculate derived statistics from a `StatBlock`
* Combine base, level, and equipment contributions
* Resolve formula-driven stat bonuses
* Apply runtime effect modifiers
* Enforce stat-specific caps and limits
* Resolve resistance behavior and soft caps
* Calculate rating-derived values and display percentages
* Build the calculation context consumed by the formula system
* Maintain a consistent calculation order between dependent statistics

`StatCalculator` does **not**:

* Own `StatBlock` instances
* Store persistent stat state
* Manage equipment
* Manage active effects
* Define formulas
* Manage formula resources
* Apply effects to entities
* Determine when a stat recalculation should occur

Those responsibilities remain with the systems surrounding it.

---

# Calculation Ownership Model

The stat architecture separates three major concerns:

| Layer             | Responsibility                                                                    |
| ----------------- | --------------------------------------------------------------------------------- |
| `StatBlock`       | Stores the entity's stat state and source snapshots                               |
| `StatManager`     | Owns stat sources, effect modifiers, rebuild state, and calculation orchestration |
| `StatCalculator`  | Resolves the mathematical result of those sources                                 |
| `StatFormula`     | Defines data-driven relationships between source and derived stats                |
| `FormulaDatabase` | Provides the formulas and resolves formula bonuses                                |
| `ResistRules`     | Defines resistance-specific rules such as the soft cap                            |

`StatCalculator` therefore acts as the **mathematical execution layer** between stat state and finalized derived values.

---

# Core Data Flow

The calculator consumes several distinct sources of information.

```text
                    ┌────────────────────┐
                    │     StatBlock      │
                    │                    │
                    │ Base Stats         │
                    │ Level Snapshot     │
                    │ Equipment Snapshot │
                    │ Current Stats       │
                    └─────────┬──────────┘
                              │
                              ▼
                    ┌────────────────────┐
                    │   StatCalculator   │
                    │                    │
                    │ Build Context      │
                    │ Resolve Sources    │
                    │ Apply Formulas     │
                    │ Apply Modifiers    │
                    │ Apply Caps         │
                    │ Calculate Ratings  │
                    └───────┬─────┬──────┘
                            │     │
              ┌─────────────┘     └──────────────┐
              ▼                                  ▼
   ┌────────────────────┐             ┌────────────────────┐
   │  FormulaDatabase   │             │    StatManager     │
   │                    │             │                    │
   │ StatFormula data   │             │ Effect modifiers   │
   │ Formula resolution │             │ Runtime modifiers  │
   └────────────────────┘             └────────────────────┘
                            │
                            ▼
                    ┌────────────────────┐
                    │ Final StatBlock    │
                    │                    │
                    │ Derived Stats      │
                    │ Resistances        │
                    │ Ratings            │
                    └────────────────────┘
```

The important architectural distinction is that `StatCalculator` **reads from these systems but does not become the owner of their data**.

---

# Calculation Pipeline

`calculate_derived_stats()` is the primary entry point.

The calculation process follows a controlled ordering because several derived statistics can themselves become sources for later formulas.

The general pipeline is:

```text
1. Validate calculation state
        ↓
2. Build calculation context
        ↓
3. Calculate derived vitals
        ↓
4. Calculate regeneration
        ↓
5. Calculate damage-related values
        ↓
6. Calculate resistances
        ↓
7. Calculate defensive statistics
        ↓
8. Calculate attack speed / movement / speed
        ↓
9. Calculate world modifiers
        ↓
10. Calculate aggregate ratings
```

This ordering allows earlier derived values to become available to later formula resolution.

For example, a formula may use `HP`, `Armor`, `AllResist`, or another derived value as a source. The calculator therefore cannot treat every derived statistic as an isolated calculation.

---

# Source Composition

Derived values generally originate from three persistent snapshots:

* Base stat values
* Level-derived values
* Equipment-derived values

These sources are combined before formula bonuses and runtime effect modifiers are resolved.

The general model is:

```text
Base
+
Level
+
Equipment
+
Formula Bonus
        ↓
Effect Modifier
        ↓
Final Derived Stat
```

Not every statistic uses every source. The calculator determines the appropriate source composition for each derived-stat category.

This allows `StatBlock` to remain a data container while `StatCalculator` remains responsible for how those values become meaningful runtime statistics.

---

# Formula Integration

`StatCalculator` does not contain hardcoded relationships between every primary/source stat and every derived statistic.

Instead, formula relationships are delegated to the formula system.

`StatFormula` provides the definition of a relationship:

```text
Source Stat
      ↓
Derived Stat
      ↓
Multiplier
```

`FormulaDatabase` resolves those definitions against the calculation context.

This creates a data-driven extension point for stat relationships without requiring the calculator itself to be rewritten whenever a formula changes.

The calculator therefore provides the **execution environment** for formulas rather than becoming the formula database.

---

# Calculation Context

`build_context()` creates the snapshot used by `FormulaDatabase`.

The context exposes three major categories of information:

```text
attr
 ├─ Source attributes
 ├─ Derived values already calculated
 └─ Current stat values

base
 └─ Base stat snapshot

level
 └─ Level-derived snapshot

gear
 └─ Equipment snapshot
```

This context is important because formula evaluation needs access to both raw sources and already-resolved statistics.

For example:

```text
Strength
   ↓
Formula
   ↓
Damage
   ↓
Later Formula
   ↓
Another Derived Stat
```

The context allows the formula system to participate in this dependency chain without directly owning the `StatBlock`.

---

# Runtime Effect Integration

Runtime effects are applied after the calculator has established the base derived value and formula contribution.

`StatCalculator` retrieves effect modifiers through `StatManager`.

Effect modifiers support three architectural behaviors:

```text
Override
   OR
Flat Addition + Percentage Scaling
```

The normal modifier model is:

```text
(value + flat) × (1 + percent)
```

An override replaces the calculated value entirely.

This gives the effect system a controlled point of influence over finalized derived statistics without embedding effect logic into every individual stat calculation.

The relationship is therefore:

```text
Stat Sources
    ↓
Formula Resolution
    ↓
Derived Value
    ↓
Effect Modifier Resolution
    ↓
Final Runtime Value
```

---

# Resistance Architecture

Resistance calculation is treated as a specialized derived-stat pipeline rather than a generic stat calculation.

`AllResist` acts as the shared resistance foundation.

The calculator:

1. Resolves the base and equipment resistance rating.
2. Adds formula-based resistance.
3. Applies runtime effect modifiers.
4. Enforces the rating boundary.
5. Converts the rating into effective resistance.
6. Applies the effective shared resistance to individual resistance types.
7. Clamps individual resistance values to their supported range.

The resistance system therefore separates:

```text
AllResist
    ↓
Shared Effective Resistance
    ↓
Individual Resistance Types
```

Individual resistance types can additionally receive their own base and equipment contributions.

The resistance soft cap is not hardcoded as the primary rule. It is supplied through `ResistRules`, allowing the resistance rule set to remain data-driven.

---

# Rating and Percentage Conversion

`StatCalculator` also provides the conversion layer between internal ratings and player-facing probability percentages.

Ratings such as:

* Critical Hit
* Dodge
* Block

use a normalized rating-to-percentage conversion.

This keeps the internal representation separate from the presentation representation.

For example:

```text
Internal Rating
      ↓
Normalized Probability
      ↓
Display Percentage
```

The calculator therefore provides both runtime probability helpers and display-oriented conversion helpers without requiring UI systems to understand the underlying rating model.

---

# Derived Stat Classification

`is_derived_stat()` provides a centralized classification of statistics considered derived rather than primary/source values.

This gives other systems a single authority for determining whether a named stat belongs to the derived-stat layer.

The classification includes:

* Vitals
* Regeneration
* Damage
* Resistances
* Tactical statistics
* Speed
* Movement
* World modifiers

This prevents individual systems from maintaining separate hardcoded definitions of what constitutes a derived stat.

---

# Ratings

Ratings are calculated after the underlying defensive statistics have been resolved.

The current defensive rating is an aggregate of:

```text
Armor
+
Block
+
Dodge
+
AllResist
```

Attack rating is reserved for a later calculation model that can incorporate offensive output and attack speed.

This establishes ratings as **summary statistics**, not independent sources of truth.

They are calculated from already-finalized statistics rather than independently maintained.

---

# Dependency Direction

The architecture intentionally keeps dependencies flowing toward calculation rather than creating ownership cycles.

```text
StatBlock
   │
   ├──────────────┐
   ▼              ▼
StatCalculator   StatManager
   │              │
   │              └── Effect Sources
   │
   └──────► FormulaDatabase
                │
                └── StatFormula
```

`StatCalculator` may query `StatManager` for effect modifiers and `FormulaDatabase` for formula results, but it does not transfer ownership of those systems into the calculator.

This keeps the calculator stateless and reusable.

---

# Stateless Design

`StatCalculator` extends `RefCounted` and uses static methods because it does not represent a persistent runtime object.

There is no calculator instance that needs to maintain:

* Active state
* Entity references
* Cached effect state
* Equipment state
* Formula state
* Global calculation state

The `StatBlock` supplied to the calculator is the calculation target.

This makes the calculator effectively a deterministic transformation layer:

```text
StatBlock Sources
+
Formula Definitions
+
Runtime Effect Modifiers
+
Rules
        ↓
StatBlock Derived State
```

---

# Relationship to StatManager

`StatManager` and `StatCalculator` have deliberately different responsibilities.

### StatManager

Owns and coordinates:

* Stat sources
* Equipment snapshots
* Effect sources
* Effect modifiers
* Rebuild state
* Stat calculation requests

### StatCalculator

Performs:

* Derived-stat mathematics
* Formula evaluation integration
* Modifier application
* Caps and normalization
* Resistance resolution
* Rating calculation

The architectural boundary is:

> **StatManager decides when and why statistics need to be rebuilt. StatCalculator determines what the resulting statistics are.**

This prevents the manager from becoming a monolithic collection of stat formulas.

---

# Relationship to StatFormula

`StatFormula` is the data definition layer.

It describes formula relationships such as:

```text
Source Stat → Derived Stat × Multiplier
```

`StatCalculator` consumes the results of those relationships through `FormulaDatabase`.

Therefore:

```text
StatFormula
    = Formula Definition

FormulaDatabase
    = Formula Resolution

StatCalculator
    = Formula Execution Context + Derived Stat Calculation
```

This separation allows formula data to remain editable and generator-friendly while calculation behavior remains centralized.

---

# Relationship to ResistRules

Resistance rules are separated from the calculator's general stat formulas.

`ResistRules` defines configurable resistance behavior such as the soft-cap percentage.

`StatCalculator` consumes those rules during resistance resolution.

This keeps the resistance rule configuration independent from the broader formula architecture while still allowing resistance calculations to participate in the derived-stat pipeline.

---

# Architectural Boundaries

The system can be summarized into four distinct responsibilities:

| Component         | Architectural Role                             |
| ----------------- | ---------------------------------------------- |
| `StatBlock`       | Runtime stat state                             |
| `StatManager`     | Source ownership and calculation orchestration |
| `StatCalculator`  | Derived-stat calculation engine                |
| `FormulaDatabase` | Data-driven formula resolution                 |
| `StatFormula`     | Formula definition resource                    |
| `ResistRules`     | Resistance rule configuration                  |

The calculator is therefore the **execution layer of the stat architecture**, sitting between the stat state maintained by `StatManager`/`StatBlock` and the data-driven rules supplied by the formula and resistance systems.

---

# Design Principle

The core design principle of `StatCalculator` is:

> **Centralize stat mathematics without centralizing stat ownership.**

`StatCalculator` should know **how** a derived statistic is calculated, but it should not become responsible for **where the statistic came from, when it needs rebuilding, or how the source is managed**.

This preserves a clean separation between:

**State → Management → Calculation → Data-Driven Rules**

and provides a single authoritative calculation layer for the game's derived-stat architecture.
