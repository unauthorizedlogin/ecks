# 📜 Quest Generator System

The Quest Generator System provides a data-driven pipeline for creating all quest definitions used throughout the game.

Quests are authored through CSV data and automatically converted into optimized `.tres` resources, allowing complex quest content to be maintained without manual resource creation.

The system is responsible for:

* quest resource generation
* objective generation
* reward generation
* quest categorization
* quest line organization
* dialogue metadata assignment
* gameplay rule configuration
* quest indexing

See:

* Dialogue System
* Reward System
* Objective System

---

## 📦 Quest Resource Architecture

Each generated quest is represented by a `QuestDefinition`.

Quest resources contain:

* quest identity
* localized title and description
* quest category
* quest line information
* ordered objectives
* rewards
* dialogue metadata
* gameplay configuration
* progression requirements

Quest resources are fully data-driven and require no manual resource creation.

---

## 🗂️ Quest Organization

Quests are automatically organized into category and quest line folders during generation.

Example:

```text
data/databases/quests/
├── story/
│   ├── prologue/
│   ├── chapter_1/
│   └── chapter_2/
│
├── side/
│   ├── blacksmith/
│   ├── fishing/
│   └── exploration/
│
├── faction/
│
├── daily/
│
└── chained/
```

This organization scales cleanly while keeping large quest databases easy to maintain.

---

## 🎯 Objective Generation

Objectives are authored directly within the CSV and generated into `ObjectiveDefinition` resources inside each quest.

Supported objective types include:

* Collect
* Kill
* Talk
* Interact
* Travel
* Turn In
* Purchase
* Equip
* Unequip
* Level Up
* Gain XP
* Spend XP
* Start Quest
* Complete Quest
* Enter Area
* Exit Area
* Open UI
* Use Skill
* Craft
* Upgrade Item
* Custom objectives

Objectives also support:

* target IDs
* target categories
* target amounts
* objective gating
* completed descriptions
* NPC turn-in routing
* shop routing

The generator assembles objective sequences automatically from the CSV definition.

---

## 🎁 Reward Generation

Quest rewards are generated alongside objectives and stored directly within each quest.

Supported reward types include:

* Experience
* Currency
* Items
* Equipment
* Ammunition
* Potions

Each reward stores:

* reward type
* reward ID
* reward amount

Reward distribution is handled at runtime by the Reward System.

---

## 💬 Dialogue Integration

Quest resources include dialogue metadata used by the Dialogue System.

Supported metadata includes:

* dialogue keys
* quest button labels
* active quest labels
* ready-to-turn-in labels
* completion text
* failure text
* journal entries

This allows dialogue presentation to remain entirely data-driven.

---

## ⚙️ Gameplay Configuration

Each quest stores gameplay configuration independently.

Supported configuration includes:

* quest category
* quest line
* quest ordering
* required level
* required quests
* faction requirements
* reputation requirements
* location requirements
* repeatable quests
* hidden quests
* automatic start
* automatic completion
* timed quests
* party progression
* failure conditions
* priority
* tags
* version metadata

These settings define quest behavior while runtime systems manage progression and execution.

---

## 🔄 Generation Pipeline

The generation process:

1. Reads quest CSV definitions
2. Validates required quest data
3. Creates `QuestDefinition` resources
4. Builds objective definitions
5. Builds reward definitions
6. Assigns dialogue and gameplay metadata
7. Organizes quests by category and quest line
8. Saves `.tres` resources
9. Rebuilds the quest index

---

## 🔎 Generated Quest Index

The Quest Index Generator scans all generated quest resources and builds a runtime lookup index.

The runtime database provides:

* fast quest loading
* deterministic resource discovery
* filesystem-independent lookups
* scalable quest organization

The index is automatically rebuilt whenever quest generation runs.

---

## ✅ System Responsibilities

The Quest Generator System:

✅ Creates quest resources
✅ Generates objectives and rewards
✅ Handles CSV → Resource conversion
✅ Organizes quests by category and quest line
✅ Stores dialogue metadata
✅ Stores gameplay configuration
✅ Builds runtime indexes

The Quest Generator System does **not**:

❌ Execute quest logic
❌ Track objective progress
❌ Grant rewards
❌ Handle dialogue flow
❌ Update the quest journal
❌ Manage runtime quest state

Those responsibilities belong to:

* Quest Runtime System
* Objective System
* Reward System
* Dialogue System
* Quest Journal System
