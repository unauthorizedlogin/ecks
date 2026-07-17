# Item Creator

## Overview

The Item Creator is an editor addon designed to streamline the creation of equipment and loot content inside the Ecks Framework.

Instead of manually creating item scenes, assigning properties, and configuring large stat blocks, developers can generate new item content through a dedicated editor interface.

The tool creates framework-compatible item scenes while exposing common item statistics through organized editor controls.

---

# Purpose

The Item Creator exists to simplify the item authoring pipeline.

Traditional item creation requires developers to:

- Duplicate base scenes
- Rename resources
- Configure stat values
- Verify exported properties
- Maintain consistent folder structures

The Item Creator automates this workflow by providing a centralized creation interface.

---

# Features

## Item Scene Generation

The Item Creator generates new item scenes from framework base templates.

Generated items:

- Inherit framework item behavior
- Follow expected scene structures
- Are placed into content directories
- Remain editable inside the Godot editor

The creator workflow uses inherited scenes, allowing future framework updates to propagate through base item definitions.

---

## Supported Item Types

Current supported item categories:

- Weapons
- Armor

The system is designed to expand into additional content categories as new item systems are introduced.

---

# Stat Authoring

The Item Creator provides direct editing for item statistics.

Supported stat groups include:

## Vital Stats

- Health
- Mana
- Stamina

---

## Regeneration Stats

- Health Regeneration
- Mana Regeneration
- Stamina Regeneration

---

## Primary Stats

- Strength
- Dexterity
- Intelligence

---

## Influence Stats

- Agility
- Constitution
- Charm
- Luck
- Vitality
- Wisdom

---

## Damage Stats

- Damage
- Critical Hit Chance
- Critical Damage

---

## Tactical Stats

- Armor
- Block
- Dodge
- Sneak

---

## Resistance Stats

- Physical Resistance
- Fire Resistance
- Ice Resistance
- Lightning Resistance
- All Resistance

---

## Speed Stats

- Attack Speed
- Cast Speed
- Movement Speed
- General Speed

---

# Live Preview System

The Item Creator includes a real-time preview panel.

As developers modify item values, the preview updates automatically to display:

- Item name
- Item type
- Active stat groups
- Assigned values

Only non-zero statistics are displayed, keeping previews clean and focused.

---

# Workflow

The Item Creator workflow:

```

Open Item Creator
|
v
Select Item Type
|
v
Define Item Name
|
v
Configure Statistics
|
v
Preview Generated Item
|
v
Create Framework Item Scene
|
v
Continue Editing In Inspector

```

---

# Framework Integration

The Item Creator integrates with the Ecks Framework item pipeline.

Generated content is designed to work with:

- Item databases
- Loot systems
- Equipment systems
- Stat systems
- Effect systems
- Save/load systems

---

# Design Philosophy

The Item Creator follows the Ecks Framework principle of separating content creation from runtime logic.

Developers define:

- What an item is
- Which statistics it provides
- How it should be configured

The framework handles:

- Runtime loading
- Equipment behavior
- Stat application
- Gameplay integration

This allows large amounts of content to be created without manually modifying gameplay systems.

---

# Future Expansion

The Item Creator foundation supports future additions including:

- Item effects
- Affixes
- Rarity configuration
- Enchantments
- Crafting materials
- Loot metadata
- Additional equipment categories

The goal is to provide a complete authoring pipeline for framework-driven ARPG content creation.
```


