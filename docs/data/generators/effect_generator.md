# 🏷️ Effect Resource Generator System

The Effect Resource Generator System provides a data-driven pipeline for creating and managing all effect resources.

Effects are authored through CSV data and automatically converted into optimized `StatEffect` `.tres` resources, allowing large-scale effect databases without manual resource creation.

The system is responsible for:

* effect resource generation
* effect identity and metadata
* category and subcategory routing
* stat modification definitions
* effect behavior configuration
* duration and timing settings
* resource cost definitions
* revive configuration
* knockback configuration
* effect tag assignment
* generated effect indexing

---

## 🧱 Effect Resource Architecture

Each generated effect is represented by a `StatEffect` resource.

Effect resources contain:

* Effect identity
* Display information
* Target stat
* Effect type
* Trigger behavior
* Target rules
* Duration settings
* Cost requirements
* Special behavior data
* Attached tags

Effects are fully data-driven and do not require manual resource creation.

---

## ⚔️ Effect Type Support

The Effect System supports multiple gameplay behaviors through a shared `StatEffect` resource.

Supported effect types include:

* Stat modifications
* Stat overrides
* Healing
* Damage over time
* Healing over time
* Temporary buffs
* Temporary debuffs
* Revive effects
* Enchantments
* Auras
* Passives
* Knockback effects

The generator creates the resource definition.

The Effect System handles runtime behavior and application.

---

## 🗂️ Category Routing

Effects are automatically organized into database folders using category and subcategory metadata.

Example:

```
data/databases/entity/effects/

├── damage/
│    ├── fire/
│    ├── poison/

├── buff/
│    ├── stats/
│    ├── temporary/

├── heal/
│    ├── instant/
│    ├── regen/

├── revive/
│    ├── resurrection/
```

This keeps large effect databases organized while allowing unlimited expansion.

---

## ⏱️ Effect Timing Configuration

Effects support configurable timing behavior.

Generated effects can define:

* trigger type
* target type
* duration
* tick interval

Examples:

```
Burning

Trigger:
OnHit

Duration:
10 seconds

Tick:
1 second
```

```
Weapon Enchant

Trigger:
OnEquip
```

---

## 💸 Effect Costs

Effects can define resource costs through the generator pipeline.

Supported costs:

* HP
* MP
* Class resources

Supported payment modes:

* None
* Require Full
* Spend Available

This allows effects to support gameplay systems such as:

* Blood magic
* Resource-based abilities
* Cost-driven consumables

---

## 🧩 Special Effect Data

The generator supports additional effect configurations.

Supported systems include:

### Revive Effects

Supports:

* HP restoration
* MP restoration
* Resource restoration
* Percentage-based values

---

### Knockback Effects

Supports:

* Knockback force
* Direction data

---

## 🏷️ Effect Tags

Effects support custom tags for future filtering and gameplay logic.

Example:

CSV:

```
fire;damage;magic
```

Generated:

```
[
 fire,
 damage,
 magic
]
```

Tags allow other systems to identify effect categories without requiring hardcoded checks.

---

## ⚙️ Generation Pipeline

The generation process:

1. Reads effect CSV definitions
2. Validates effect data
3. Creates `StatEffect` resources
4. Assigns effect properties
5. Applies timing and cost configuration
6. Applies special effect data
7. Routes resources into categories
8. Saves `.tres` resources
9. Rebuilds the effect index

---

## 🔎 Generated Effect Index

The Effect Index Generator scans generated resources and creates a runtime lookup index.

The runtime database provides:

* fast effect loading
* deterministic resource discovery
* filesystem-independent access
* scalable effect database growth

The index is automatically rebuilt whenever effect generation runs.

---

## 🔗 Effect System Integration

The Effect Generator System provides effect definitions.

It does not control how effects are applied.

Instead:

* Effect resources define behavior
* Effect Database provides runtime lookup
* Effect Manager handles active effects
* Gameplay systems request effects when needed

Example:

```
Item
 |
 ↓
StatEffect Reference
 |
 ↓
Effect Database
 |
 ↓
Effect Manager
 |
 ↓
Entity
```

---

## ✅ System Responsibilities

The Effect Resource Generator System:

✅ Creates effect resources
✅ Handles CSV → Resource conversion
✅ Routes effects into database categories
✅ Generates effect metadata
✅ Configures effect behavior
✅ Builds runtime indexes

The Effect Resource Generator System does **not**:

❌ Apply effects to entities
❌ Manage active effect duration
❌ Handle combat calculations
❌ Control item abilities
❌ Manage ability execution

Those responsibilities belong to:

* Effect Database System
* Effect Manager System
* Combat System
* Item System
* Ability System
