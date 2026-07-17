## 🏷️ Item Generator System

The Item Generator System provides a data-driven pipeline for creating and managing all item resources.

Items are authored through CSV data and automatically converted into optimized `.tres` resources, allowing large-scale item databases without manual resource creation.

The system is responsible for:

* item resource generation
* item identity and metadata
* category and subcategory routing
* equipment configuration
* weapon, armor, and equip slot definitions
* item requirements
* rarity and economy values
* stackability rules
* stat modifier assignment
* item effect assignment
* generated item indexing

---

### 🧱 Item Resource Architecture

Each generated item is represented by an `ItemResource`.

Item resources contain:

* Visual data
* Item identity
* Item category/type
* Equipment data
* Requirements
* Economy data
* Gameplay rules
* Stat modifiers
* Attached effects

Items are fully data-driven and do not require manual resource creation.

---

### 🗂️ Category Routing

Items are automatically organized into database folders using category and subcategory metadata.

Example:

```

data/databases/items/
├── equipment/
│    ├── weapons/
│    ├── armor/
│
├── materials/
│    ├── metals/
│    ├── minerals/
│
├── consumables/
│    ├── potions/

```

This allows the item database to scale while maintaining organized resource management.

---

### ⚔️ Equipment Support

The item system supports:

* armor categories
* weapon categories
* equipment slots
* class requirements
* level requirements
* level scaling rules

Supported equipment definitions include:

* armor types
* weapon types
* equip slots
* custom equipment slots

---

### 📊 Item Stats & Modifiers

Items support dynamic stat modifiers through the existing stat architecture.

Generated items can define:

* primary stats
* defensive stats
* offensive stats
* resistances
* derived gameplay modifiers

Stat validation prevents invalid modifier definitions from entering the database.

---

### ✨ Item Effects

Items can attach `StatEffect` resources for additional gameplay behavior.

The Item System does not define effect behavior.

Instead:

* Item resources store effect references
* The Effect System handles effect definitions
* The Effect System handles runtime application

This allows equipment, consumables, and future item types to share the same effect pipeline.

See:

- [Effect System Architecture → StatEffect Pipeline](.docs/system/entity/effect_system.md)

---

### ⚙️ Generation Pipeline

The generation process:

1. Reads item CSV definitions
2. Validates item data
3. Creates ItemResource objects
4. Assigns metadata and gameplay properties
5. Resolves stat modifiers
6. Links attached effects
7. Saves `.tres` resources
8. Rebuilds the item index

---

### 🔎 Generated Item Index

The Item Index Generator scans generated resources and creates a runtime lookup index.

The runtime database provides:

* fast item loading
* deterministic resource discovery
* filesystem-independent access
* scalable item database growth

The index is automatically rebuilt whenever item generation runs.

---

### ✅ System Responsibilities

The Item Generator System:

✅ Creates item resources  
✅ Handles CSV → Resource conversion  
✅ Routes items into database categories  
✅ Validates item data  
✅ Connects items to stats and effects  
✅ Builds runtime indexes  

The Item Generator System does **not**:

❌ Handle inventory logic  
❌ Handle equipment behavior  
❌ Apply item effects  
❌ Manage loot generation rules  

Those responsibilities belong to:

- Inventory System
- Equipment System
- Effect System
- Loot System
```
