# 🧍 Class Data Resource

## Overview

The Class Data Resource defines the configuration for a playable or non-playable character class.

It provides class identity, ownership, archetype information, stat block assignments, starting abilities, and starting equipment.

Classes are data-driven and reference existing engine resources rather than containing runtime class logic.

---

### 🆔 Class Identity

Each `ClassData` resource defines:

* Class ID
* Display name
* Portrait
* Description

The `class_id` provides the unique identifier used by the class database and other systems.

---

### ⚙️ Class Configuration

Classes define several configuration values:

* Class owner
* Resource display name
* Archetype

The class owner determines what type of entity the class is intended for:

```text
NONE
PLAYER
ENEMY
NPC
```

This allows the same class architecture to support multiple entity types.

---

### 📊 Stat Block Assignment

Classes can reference multiple `StatBlock` definitions.

```gdscript
@export var stat_blocks: Array[String]
```

These IDs are resolved through the `StatBlockDatabase`.

```text
ClassData
   │
   └── Stat Block IDs
          │
          ↓
   StatBlockDatabase
          │
          ↓
      StatBlock
```

The resource provides:

```gdscript
get_stat_blocks()
```

which resolves the configured IDs into actual `StatBlock` resources.

---

### ✨ Starting Abilities

Classes can define a list of starting ability IDs.

```gdscript
@export var starting_abilities: Array[String]
```

The class resource stores the ability references, while the Ability System handles the actual ability definitions and runtime behavior.

---

### 🎒 Starting Equipment

Classes can define equipment assigned when the class is created.

Supported slots include:

* Head
* Shoulders
* Arms
* Hands
* Body
* Waist
* Legs
* Feet
* Weapon
* Offhand
* Wrist
* Neck
* Ring
* Back
* Unique
* Custom Slots 1–4

Each slot stores an item ID rather than the item resource itself.

---

### 🗂️ Equipment Resolution

The resource provides:

```gdscript
get_starting_equipment()
```

This converts the individual starting equipment fields into an equipment dictionary keyed by `ItemResource.EquipSlot`.

```text
ClassData
   │
   └── Starting Item IDs
          │
          ↓
   get_starting_equipment()
          │
          ↓
   EquipSlot → Item ID
```

This allows character creation and equipment systems to consume starting equipment through a consistent structure.

---

### 🔗 System Integration

Class Data integrates with:

* Class Database
* Stat Block Database
* Ability System
* Item System
* Character Creation
* Player System
* Enemy System
* NPC System

The resource defines **what a class contains**, while these systems handle loading, assignment, and runtime behavior.

---

### ✅ Resource Responsibilities

The Class Data Resource:

✅ Defines class identity
✅ Defines class ownership
✅ Defines class archetype
✅ References stat blocks
✅ References starting abilities
✅ Defines starting equipment
✅ Resolves stat block resources
✅ Converts starting equipment into an equipment mapping

The Class Data Resource does **not**:

❌ Calculate character stats
❌ Execute abilities
❌ Equip items at runtime
❌ Manage character progression
❌ Handle combat behavior

Those responsibilities belong to the appropriate runtime systems.
