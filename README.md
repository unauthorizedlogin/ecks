# ecks — Godot RPG Framework

ecks (pronounced "ex") is a fully data-driven RPG framework created by **ReD** AKA **unauthorizedlogin** for Godot Engine.

ecks is a Godot RPG framework focused on turning complex gameplay systems into flexible, data-driven tools. Instead of hard-coding gameplay logic, ecks focuses on creating flexible foundations where quests, dialogue, combat, inventory, progression, and world systems can be authored through data-driven resources.

ecks focuses on reducing the amount of custom code required when building RPGs by providing interconnected systems for quests, dialogue, combat, inventory, progression, localization, and world management.

The framework is developed alongside a full Adventure RPG project, where every system is tested through real gameplay requirements before being expanded into reusable tools.

## Community & Development

Follow the development of ecks and the Adventure RPG project:

- 🎥 YouTube: [unauthorizedlogin](www.youtube.com/@UnauthorizedLogin)
- 💬 Discord: [ecks Community](https://discord.gg/638gZMRxaj)
- 🎮 itch.io: [ecks on itch.io](https://unauthorizedlogin.itch.io)
- 🕹️ gamedev: [Game Development](https://gamedev.net/members/368596-unauthorizedlogin/)

[Coming - Contributing](./CONTRIBUTING.md)

Development updates, system breakdowns, and framework progress are shared through regular devlogs and community discussions.

## Development Status:

- Status: ecks is currently in active development.
- Stage: pre-alpha
- Version: 0.52.194
- Date: 7/16/2026

[Coming - Changelog](./docs/CHANGELOG)

## Built With

- Godot Engine
- GDScript
- Godot Resources
- Data-driven architecture

## Roadmap

Current development includes:
- Expanded Inventory Space
- Convert Level to Map
- Chunk Loading Refactor
- Launch Flow Cleanup
- Debug Difficulty

Future development includes:
- Additional RPG mechanics
- Expanded creator tools
- More content authoring workflows
- Framework documentation

[Coming - Roadmap](./docs/roadmap.md)

---

## 📖 Documentation

- [Systems Documentation](./docs)


## 🎨 Media

- [Screenshots](./screenshots)
- [Coming - Videos](./videos)
- [Coming - Artwork](./art)


## 🧠 Framework Philosophy

ecks is built around a creator-first, data-driven architecture designed to separate gameplay systems from the content those systems manage.

The framework follows a simple principle:

> Systems define the rules. Data defines the structure. Content defines the experience.

This separation allows creators to expand RPG content without constantly modifying core gameplay code.

ecks separates framework responsibilities into three layers:

# 🏗️ Systems Layer

# 📚 Data Layer

# 🎮 Content Layer

For a detailed breakdown of each system documentation can be found here:

- [Architecture Overview](docs/architecture)

---

## 🎮 Creator-Focused Development

ecks is designed to support both framework developers and content creators.

A creator should be able to define:

- An NPC's role
- A quest's objectives
- Dialogue states
- Rewards
- Item properties
- Progression rules

through data and configuration rather than rewriting gameplay systems.

The framework is built to turn complex RPG development into an organized authoring process while keeping the underlying architecture flexible for expansion.

## 🎛️ Inspector-Driven Configuration

Ecks is designed around Godot's Inspector workflow, giving creators a powerful editor-facing configuration layer without requiring constant script modification.

While gameplay systems are driven through resources, databases, and configurable definitions, framework scenes and components expose their creator-facing options directly through exported Inspector properties.

This includes more than simple visual adjustments. Ecks uses the Inspector to configure presentation, behavior, and localized content choices throughout the framework.

Examples include:

* UI fonts, colors, outlines, spacing, alignment, and layout settings
* Menu and panel presentation options
* HUD and display configuration
* Resource and scene references
* Gameplay component properties
* Numeric ranges and limits
* Creator-facing dropdown selections
* Localized label and title options

Ecks also provides enum-driven Inspector workflows for common text selections. Instead of requiring creators to manually enter localization keys or modify scripts, predefined options can be selected directly from dropdown menus.

For example, vendor configuration can expose options such as:

* Shop titles
* Inventory section labels
* Buyback labels
* Item information headings

These selections automatically map to the framework's localization pipeline while keeping the Inspector clean and creator-friendly.

Custom override fields are available when a project needs unique wording, allowing creators to use framework defaults while still supporting complete customization.

The result is a workflow where creators can configure and customize the majority of framework systems directly through Godot's editor, while still retaining the ability to extend or replace systems with custom code when desired.

---

## Features

### 🧬 Entity Framework System
- Centralized entity definition system controlling all entity-based gameplay data
- Shared class architecture supporting players, NPCs, enemies, and other world entities
- Data-driven stat blocks defining base attributes, derived stats, resistances, and combat properties
- Configurable entity class rules controlling behavior, progression, and gameplay restrictions
- Unified effect system supporting buffs, debuffs, modifiers, and difficulty scaling
- Entity-specific configuration for NPCs, enemies, and playable characters
- Difficulty modifiers integrated through the same effect pipeline used by entities
- Resource-based definitions allowing entities to be created and balanced without modifying code
- Shared framework pipeline ensuring consistent stat, effect, and rule handling across all entity types
- [Class System](./docs/class_system)
- [Effect System](./docs/effect_system)

### 📈 Stats & Progression System
- Data-driven actor stat architecture
- Shared stat identity across players, NPCs, enemies, and future actors
- Primary, derived, offensive, defensive, and resistance stats
- Flexible class and character progression support
- Resource-based stat definitions
- Extensible calculations for future mechanics
- [Stats System](./docs/stats_system)

### 🎒 Economy & Item Framework
- Complete data-driven economy foundation for managing all item-based gameplay systems
- Unified item database supporting equipment, consumables, resources, currencies, and progression items
- Modular item category system supporting:
  - Ammunition
  - Consumables
  - Currency
  - Equipment
  - Gems
  - Ingredients
  - Materials
  - Metals
  - Minerals
  - Stones
  - Potions
  - Quest items
  - Miscellaneous items
  - Additional expandable categories
- Equipment and inventory management integrated into the same item pipeline
- Data-driven vendor and shop system supporting customizable inventories, pricing, and merchant configurations
- Flexible economy architecture designed for future crafting, gathering, trading, and additional item systems
- [Inventory System](./docs/inventory_system)
- [Shop System](./docs/shop_system)


### 💬 Dialogue System
- NPC interaction framework
- Quest-aware dialogue states
- Flavor dialogue support
- Data-driven dialogue resources
- [Dialogue System](./docs/dialogue_system)

### 📜 Quest System
- Data-driven quest definitions
- Configurable objective types
- Quest chains and progression
- NPC-driven quest interactions
- Story and side quest tracking
- Author quests without modifying core systems
- [Quest System](./docs/quest_system)

### 🗡️ Combat System
- Event-driven combat pipeline
- Shared actor identity system
- Damage, effects, resistances, and combat events
- Combat-to-quest integration
- [Combat System](./docs/combat_system)

### 🌎 World Systems
- Persistent world state
- Save/load framework
- Scene and chunk management
- Content organization tools
- [World Systems](./docs/world_systems)

### 📊 Event Viewer System
- Centralized gameplay event tracking and feedback system
- Custom event channels for organizing gameplay information
- Configurable filters and display categories
- Color-coded event messages for different gameplay events
- Localization-ready event messages
- Saveable viewer configuration
- [Event Viewer](./docs/event_viewer)

### 🌐 Localization

- [Menus & UI](./docs/menus_ui)

ecks is built with localization as a core framework feature rather than an afterthought.

Every player-facing text field throughout the framework is already wrapped with `tr()`, including UI, menus, dialogue, quests, objectives, items, events, combat messages, notifications, and gameplay systems. Localization is explicitly authored and does not rely on automatic machine translation at runtime.

The framework includes a production-tested CSV → PO generation pipeline that has been battle-tested throughout development and is designed to scale alongside the project.

Features include:

- Framework-wide `tr()` integration for all player-visible text
- Data-driven localization pipeline
- Battle-tested CSV → PO generator workflow
- Generator-based translation management
- Over **14,000 unique localization keys**
- Support for **23 languages**
- Approximately **322,000 translated strings** (14,000+ keys × 23 languages)
- At an average of **7 words per string**, the framework currently manages over **2.25 million localized words**
- Localization-ready resources, menus, systems, and templates

## 🛠️ Creator Tools

- [Creator Tools](./docs/creator_tools)

ecks includes dedicated tools designed to streamline RPG development workflows and reduce repetitive content management.

### 📦 Item Creator

- Structured item authoring workflow
- Automated item resource generation
- Support for equipment, consumables, materials, and resources
- Data-driven item definitions
- Organized output routing for generated content

The Item Creator allows creators to build and manage large item databases through structured data instead of manually creating individual resources.

### 📊 XP Analyzer

- Experience progression analysis tool
- Level curve visualization and balancing support
- Helps evaluate RPG progression pacing
- Designed for tuning player advancement systems

The XP Analyzer helps creators design and balance progression systems by making experience requirements easier to evaluate and adjust.

## Project Identity

The ecks RPG Framework is developed by UnauthorizedLogin.

This repository is the official home of the framework and its public documentation.

Because search engines often associate similar RPG projects, creators, and discussions together, the following clarifications are provided to avoid confusion.

ecks is not associated or affiliated with:

- Godot RPG Framework by Grandro
- RPG in a Box
- Godot A-RPG or its associated repositories.
- ORK Framework.
- Godot open RPG by GDQuest or projects maintained by newold3.
- Skelerealms by SlashScreen.
- Community forks, template repositories, or unrelated GitHub projects using similar RPG terminology.
- Reddit posts, community discussions, or third-party examples. UnauthorizedLogin does not maintain an active Reddit presence, and Reddit content should not be considered an official source of information about Ecks.
- Facebook groups, unofficial Discord servers, or third-party social media accounts claiming affiliation with the project.
- Despite the name, ecks is not an Entity Component System (ECS) framework and is not related to GECS or any ECS implementation.

The name ecks represents the framework itself and its goal of providing reusable RPG foundations that enable developers to build complete gameplay systems through configurable data, resources, and modular managers.

Official Sources:

For accurate information about the framework, use only official ecks resources, including:

This GitHub repository
Official documentation published with the project
Content published by UnauthorizedLogin

Anything outside of those sources should be treated as unofficial unless explicitly linked from the project.

## 📄 License

ecks is a proprietary RPG framework developed by UnauthorizedLogin.

The source code is private and is not licensed as open source software.

The framework is shared as a development product intended to provide creators with access to its tools, systems, workflows, and authoring pipelines.

Usage rights, distribution rights, and source access are determined by the licensing model provided with ecks.

The framework architecture, code, systems, generators, and associated tools remain the intellectual property of UnauthorizedLogin.

For licensing inquiries or access information, please refer to the official ecks community channels.
