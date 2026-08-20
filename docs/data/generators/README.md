# ⚙️ Generator System Architecture

## Overview

The Generator System provides a data-driven content creation pipeline for converting structured creator data into runtime-ready engine resources.

Generators bridge the gap between authoring workflows and gameplay systems by transforming:

- Resource definitions
- CSV data
- Structured content files

into optimized runtime assets.

The framework uses generators to create content without requiring manual resource creation or direct code modification.

The standard generator pipeline:

1. **Source Data**
2. **Generator Tool**
3. **Runtime Resource Output**
4. **Database / System Consumption**

---

# 🏗️ Generator Architecture

```text
Creator Data
      |
      |
      v
Generator System
      |
      |
      v
Runtime Resources
      |
      |
      v
Database / Gameplay Systems
````

---

# 📦 Resource Generators

Most framework content uses resource-based generation.

Pipeline:

```text
Resource Definition
        |
        ↓
Generator
        |
        ↓
.tres Resource
        |
        ↓
Runtime Database
        |
        ↓
Gameplay System
```

Generated resources provide:

* Editor compatibility
* Runtime performance
* Consistent structure
* Validation support
* Database integration

Examples:

* Classes
* Items
* Quests
* NPCs
* Dialogue
* Effects
* Environment data

---

# 📊 CSV-Based Generators

Large datasets are authored through CSV files.

Pipeline:

```text
CSV Data
        |
        ↓
Generator
        |
        ↓
.tres Resources
        |
        ↓
Runtime Systems
```

CSV generation provides:

* Spreadsheet-based authoring
* Bulk content creation
* Easier balancing
* Consistent resource output
* Large-scale content management

---

## 🌐 Localization Generator System

The Localization Generator System provides a data-driven pipeline for creating and managing all translation resources used throughout the project.

Localization strings are authored through CSV data and automatically converted into standard `.po` translation files, allowing player-facing content to be maintained through a centralized localization database.

The system is responsible for:

* CSV → PO translation generation
* localization key management
* multilingual translation file generation
* translation metadata handling
* PO file updates
* existing translation preservation

Generated translation files are consumed by the runtime localization pipeline.

- [Localization Generator](./po_generator.md)

---

---

# 📜 Credits Generator

Credits use a lightweight data conversion pipeline.

Pipeline:

```text
CSV Data
        |
        ↓
Credits Generator
        |
        ↓
credits.gd
        |
        ↓
Credits System
```

This allows credits content to be maintained externally while remaining runtime accessible.

---

# 🗂️ Index Generators

Indexes provide deterministic resource discovery.

Pipeline:

```text
Resource Scan
        |
        ↓
Index Generator
        |
        ↓
Generated .gd Index
        |
        ↓
Runtime Database
```

Index generators:

* Scan resource locations
* Validate available content
* Generate resource paths
* Remove runtime filesystem scanning

Used by:

* Database systems
* Content registries
* Runtime loaders

---

# 🔄 Complete Framework Pipeline

```text
Creator Content
        |
        |
        +----------------+
        |                |
        v                v

      CSV Data     Resource Data

        |                |

        v                v

     Generator System

             |

             v

     Runtime Assets

             |

             +----------------+
             |                |
             v                v

       Databases       Gameplay Systems
```

---

# 🛠️ Creator Workflow

Creating generated content:

1. Create or edit source data.
2. Run the appropriate generator.
3. Generated runtime files are created.
4. Indexes update automatically when required.
5. Systems consume generated resources.

Creators work with data.

The framework handles:

* Resource creation
* File generation
* Validation
* Runtime availability

---

# ✅ Design Benefits

The Generator System provides:

* Data-driven content creation
* Reduced manual resource management
* Consistent runtime structures
* Large-scale content support
* Export-safe loading
* Editor-friendly workflows
* Separation between authoring and runtime systems

---

# 🏭 Generator Implementations

The framework uses the shared Generator System Architecture across all content creation pipelines.

Each generator converts structured creator data into runtime-ready resources, indexes, or system-specific data.

Implemented generators:

| Generator | Purpose | Documentation |
|---|---|---|
| ⚡ Ability Generator | Generates ability definitions and gameplay ability resources | [Ability Generator](./ability_generator.md) |
| 📷 Camera Generator | Generates camera profiles for world and minimap | [Camera Generator](./camera_generator.md) |
| 🧬 Class Generator | Generates class definitions and statistical foundations | [Class Generator](./class_generator.md) |
| 🏷️ Class Rules Generator | Generates class behavior rules and restrictions | [Class Rules Generator](./class_rules_generator.md) |
| 🎬 Credits Generator | Generates runtime credit data from CSV definitions | [Credits Generator](./credits_generator.md) |
| 💬 Dialogue Generators | Generates dialogue resources and conversation data | [Dialogue Generators](./dialogue_generators.md) |
| ⚖️ Difficulty Generators | Generates gameplay difficulty configurations | [Difficulty Generators](./difficulty_generators.md) |
| ✨ Effect Generator | Generates gameplay effect definitions and effect resources | [Effect Generator](./effect_generator.md) |
| 📦 Item Generator | Generates item resources and equipment definitions | [Item Generator](./item_generator.md) |
| 🗺️ Map Generator | Generates world map resources and level definitions | [Map Generator](./map_generator.md) |
| 👤 NPC Generator | Generates NPC definitions and references | [NPC Generator](./npc_generator.md) |
| 🌐 PO Generator | Generates translation resources from CSV data | [.po Generator](./po_generator.md) |
| 📜 Quest Generator | Generates quest definitions and objectives | [Quest Generator](./quest_generator.md) |
| 🧮 Stat Formula Generator | Generates stat conversion formulas | [Stat Formula Generator](./stat_formula_generator.md) |
| 📈 XP Generators | Generates character progression resources | [XP Generators](./xp_generators.md) |

---

# Summary

The Generator System allows creators to build large amounts of framework-compatible content through structured data workflows.

By converting source data into optimized runtime resources and indexes, the engine maintains scalable content management while keeping gameplay systems clean and independent from authoring workflows.

---
