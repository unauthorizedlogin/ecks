# 📦 Item Resource

## Overview

The Item Resource is the base data definition for all item objects.

It stores item identity, classification, equipment information, requirements, economy values, stack rules, stat modifiers, and effects.

---

# Resource Structure

```

ItemResource
│
├── Visual
│   ├── icon
│   ├── name
│   └── description
│
├── Identity
│   ├── item_id
│   ├── type
│   ├── item_type
│   ├── category
│   └── subcategory
│
├── Equipment
│   ├── equip_slot
│   ├── armor_type
│   └── weapon_type
│
├── Requirements
│   ├── required_class_id
│   ├── required_level
│   ├── max_level
│   └── level_scaling
│
├── Economy
│   ├── price
│   └── rarity
│
├── Rules
│   └── stackable
│
├── Stats
│   └── modifiers
│
└── Effects
└── effects

```

---

# Visual Data

| Property | Description |
|---|---|
| icon | Item display icon |
| name | Item display name |
| description | Item description text |

---

# Identity Data

| Property | Description |
|---|---|
| item_id | Unique item identifier |
| type | Item category enum |
| item_type | String-based item type identifier |
| category | Item category grouping |
| subcategory | Item subgroup |

---

# Item Types

| Type | Purpose |
|---|---|
| AMMO | Ammunition items |
| CONSUMABLE | Consumable items |
| CURRENCY | Currency items |
| EQUIPMENT | Equipment items |
| GEM | Gem items |
| INGREDIENT | Ingredient items |
| ITEM | Generic item |
| METAL | Metal materials |
| MINERAL | Mineral materials |
| MISC | Miscellaneous items |
| POTION | Potion items |
| QUEST | Quest items |
| STONE | Stone materials |

---

# Equipment Data

| Property | Description |
|---|---|
| equip_slot | Equipment slot assignment |
| armor_type | Armor classification |
| weapon_type | Weapon classification |

---

# Armor Types

| Type |
|---|
| CLOTH |
| LIGHT |
| MEDIUM |
| HEAVY |

---

# Weapon Types

| Type |
|---|
| AXE |
| BOW |
| CROSSBOW |
| DAGGER |
| GUN |
| HAMMER |
| INSTRUMENT |
| MACE |
| SCYTHE |
| SHIELD |
| SPEAR |
| STAFF |
| SWORD |
| TOTEM |
| UNARMED |
| WAND |

---

# Requirements

| Property | Description |
|---|---|
| required_class_id | Required class identifier |
| required_level | Minimum required level |
| max_level | Maximum level restriction |
| level_scaling | Enables level scaling |

---

# Economy

| Property | Description |
|---|---|
| price | Item value |
| rarity | Item rarity classification |

---

# Rules

| Property | Description |
|---|---|
| stackable | Determines if multiple items can share a stack |

---

# Stats

| Property | Description |
|---|---|
| stat_blocks | Array of StatBlock resources attached to the item |

---

# Effects

| Property | Description |
|---|---|
| effects | Array of StatEffect resources attached to the item |

# Abilities

| Property | Description |
|---|---|
| abilities | Array of AbilityDefinition resources attached to the item |
