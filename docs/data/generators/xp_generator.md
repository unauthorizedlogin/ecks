# 📈 XP Generator System

The XP Generator System provides a data-driven pipeline for creating all character progression resources.

Experience curves and level growth tables are authored through CSV data and automatically converted into optimized `.tres` resources, allowing progression to be managed entirely through data rather than hardcoded values.

The system is responsible for:

* XP curve resource generation
* level growth resource generation
* level progression data
* stat growth data
* progression indexing
* runtime progression lookup

See:

* Character Progression System
* Class System
* Stat System

---

## 📦 Progression Resource Architecture

Each generated progression dataset is represented by one of two resource types:

* `LevelCurveResource`
* `LevelGrowthData`

Together these resources define both progression requirements and stat growth.

Generated resources contain:

* class progression data
* maximum level configuration
* total XP requirements
* XP required per level
* level-based stat growth
* runtime lookup tables

All progression resources are fully data-driven and do not require manual resource creation.

---

## 📈 XP Curve Resources

Level Curve resources define the experience required to progress through each level.

Each generated curve stores:

* class identifier
* maximum level
* cumulative XP by level
* XP required to reach the next level

The runtime uses these resources for:

* level resolution from total XP
* XP progression
* progress bar calculations
* maximum level detection

Runtime progression never recalculates experience formulas. All values are precomputed during generation.

---

## 📊 Level Growth Resources

Level Growth resources define the stat progression for every level.

Each generated growth table stores:

* class identifier
* per-level stat values
* progression rows
* validation settings

Each level contains a dictionary of stat values generated directly from the CSV pipeline.

This allows designers to tune character growth without modifying gameplay code.

---

## 🌱 Data-Driven Progression

Progression is authored entirely through CSV files.

Supported data includes:

* total XP requirements
* XP between levels
* health growth
* mana growth
* primary stat growth
* secondary stat growth
* custom progression values

The generator converts this data into optimized runtime resources.

---

## ⚙️ Generation Pipeline

The generation process:

1. Reads XP curve CSV definitions
2. Builds Level Curve resources
3. Reads level growth CSV definitions
4. Builds Level Growth resources
5. Saves generated `.tres` resources
6. Rebuilds the progression index

---

## 🔎 Generated XP Index

The XP Index Generator scans all generated progression resources and builds a runtime lookup index.

The runtime database provides:

* deterministic resource loading
* export-safe progression discovery
* filesystem-independent lookups
* scalable progression organization

The index is automatically rebuilt whenever XP resources are regenerated.

---

## ✅ System Responsibilities

The XP Generator System:

✅ Creates XP curve resources
✅ Creates level growth resources
✅ Handles CSV → Resource conversion
✅ Stores progression data
✅ Stores stat growth data
✅ Builds runtime indexes

The XP Generator System does **not**:

❌ Award experience
❌ Handle level-up logic
❌ Calculate derived stats
❌ Modify character attributes directly
❌ Manage progression events

Those responsibilities belong to:

* Character Progression System
* Stat System
* Class System
* Character Runtime Systems
