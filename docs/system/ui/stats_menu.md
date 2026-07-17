# 📊 Player Stats Menu System

## Overview

The Player Stats Menu provides a centralized interface for displaying player progression, combat values, derived attributes, resources, and experience information.

The system is designed as a data-driven UI layer that connects directly to the player's stat architecture, allowing creators to expose a complete character sheet without manually wiring individual values.

The menu dynamically reads from the player's `StatBlock`, `VitalComponent`, and `PlayerLevelComponent`, updating displayed values whenever relevant gameplay systems change.

---

# 🎯 Responsibilities

The Player Stats Menu is responsible for:

- Displaying player attributes and derived statistics
- Displaying current HP, MP, and class resources
- Displaying experience progression and level information
- Connecting to player progression components
- Handling localization for all displayed stat names
- Updating values dynamically through component signals

The menu acts as a presentation layer and does not own stat calculations.

---

# 🧠 Data Sources

The Stats Menu pulls information from several gameplay systems.

## StatBlock

The primary source for character statistics.

Provides:

- Base attributes
- Primary stats
- Secondary stats
- Damage values
- Defensive values
- Resistances
- Speed values
- Combat ratings

Examples:

- Vitality
- Strength
- Dexterity
- Armor
- Crit Chance
- Fire Resistance
- Attack Rating

---

## VitalComponent

Provides live resource information.

Tracked values:

- Health
- Mana
- Class Resource

Updates are received through component signals:

- Health changed
- Mana changed
- Resource changed

---

## PlayerLevelComponent

Provides progression data.

Displays:

- Current level
- Current experience
- Experience required for next level
- Experience progress bar

Updates through:

- XP gained signals
- Level up signals
- Loaded progression data

---

# 📋 Supported Stat Categories

The menu supports multiple stat groups.

## Base Attributes

- Vitality
- Wisdom
- Stamina

---

## Regeneration

- HP Regen
- MP Regen
- Resource Regen

---

## Primary Attributes

- Dexterity
- Intelligence
- Strength

---

## Secondary Attributes

- Agility
- Fortitude
- Willpower

---

## Damage Statistics

- Damage
- Critical Chance
- Critical Damage

---

## Influence Statistics

- Concentration
- Charm
- Luck

---

## Tactical Statistics

- Armor
- Block
- Dodge
- Barrier
- Sneak

---

## Resistances

- All Resist
- Ice Resist
- Fire Resist
- Lightning Resist
- Physical Resist

Resistance values are automatically converted into percentage display formatting.

Example:

```

750 resistance value

becomes

75% Resistance

````

---

## Speed Statistics

- Speed
- Attack Speed
- Cast Speed
- Movement Speed

---

## Combat Ratings

- Attack Rating
- Defense Rating

---

# 🌐 Localization Support

All displayed stat names are localization-ready.

The menu does not rely on automatic translation.

Every visible label is routed through:

```gdscript
tr()
````

Supported localized entries include:

* Stat names
* Resource names
* XP labels
* UI headers

Creators can safely replace or expand displayed terminology through the localization database.

---

# 🔄 Dynamic Updating

The menu updates automatically when gameplay values change.

Connected systems:

```
Player
 ├── StatBlock
 │    └── Attribute Values
 │
 ├── VitalComponent
 │    ├── HP
 │    ├── MP
 │    └── Resource
 │
 └── PlayerLevelComponent
      ├── XP
      └── Level
```

Updates occur through signals instead of constant polling.

---

# 🖥️ UI Integration

The Stats Menu registers itself globally:

```gdscript
add_to_group("player_stats_ui")
```

This allows other systems to locate the menu when required.

When opened:

* The menu becomes visible
* UIManager receives a menu notification
* Player data is refreshed
* XP information is updated

---

# 🎮 Menu Controls

The Stats Menu provides:

* Open menu functionality
* Close menu functionality
* Player assignment
* Automatic refresh after initialization

The menu begins hidden and is controlled by the global UI layer.

---

# 🧩 Creator Customization

Creators can customize:

* Layout organization
* Display ordering
* Stat grouping
* Header labels
* Localization entries
* Additional stat categories

The system is designed to support custom character sheets without requiring changes to the underlying stat architecture.

---

# 🏗️ Framework Benefits

The Player Stats Menu provides:

- ✅ Data-driven stat display
- ✅ Signal-based updates
- ✅ Full localization support
- ✅ Component-based integration
- ✅ Automatic player binding
- ✅ Expandable stat categories
- ✅ Creator-friendly UI customization

---

# 🔗 System Dependencies

Requires:

* `StatBlock`
* `VitalComponent`
* `PlayerLevelComponent`
* `UIManager`
* Player controller
* Localization database

---

# Summary

The Player Stats Menu is a complete character information interface built around the framework's stat and progression systems.

Rather than manually updating UI values, creators receive a ready-made character sheet capable of displaying combat, progression, resources, and attributes through the existing data architecture.
