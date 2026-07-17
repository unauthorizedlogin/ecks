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

# 🌐 Localization Generator

Localization uses a specialized pipeline.

Unlike gameplay resources, localization is converted into translation files.

Pipeline:

```text
CSV Data
        |
        ↓
Localization Generator
        |
        ↓
.po Translation Files
        |
        ↓
Godot Translation System
```

Supports:

* Multiple languages
* Centralized string management
* Framework-wide localization

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

# Summary

The Generator System allows creators to build large amounts of framework-compatible content through structured data workflows.

By converting source data into optimized runtime resources and indexes, the engine maintains scalable content management while keeping gameplay systems clean and independent from authoring workflows.

---

## 🧬 Class Generator

Defines the statistical identity and base attributes associated with entity classes.

While ClassRuleResources define how classes behave, Class Resources define the core statistical foundation used by player, enemy, and NPC archetypes.

Manages:

- Class identity
- Base statistics
- Derived statistics
- Damage values
- Resistances
- Regeneration values
- Speed attributes
- Combat ratings
- Entity categorization

Generated from CSV data and consumed by the Class Database runtime pipeline.

- [Class Generator](./docs/resources/class_generator.md)

---

## 🧬 Class Rule Generator

Defines the behavioral rules and restrictions associated with entity classes.

While StatBlocks define class statistics, ClassRuleResources define how classes interact with gameplay systems.

Manages:

- Equipment permissions
- Weapon restrictions
- Armor restrictions
- Starting equipment
- Combat affiliations
- Visual configuration
- Class tags
- Class restrictions

Generated from CSV data and consumed by the Class Database runtime pipeline.

- [Class Rule Generator](./docs/resources/class_rule_generator.md)

---

