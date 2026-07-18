# ecks — Godot RPG Framework

ecks (pronounced "ex") is a fully data-driven RPG framework created by **ReD** AKA **unauthorizedlogin** for Godot Engine.

ecks is a Godot RPG framework focused on turning complex gameplay systems into flexible, data-driven tools. Instead of hard-coding gameplay logic, ecks focuses on creating flexible foundations where quests, dialogue, combat, inventory, progression, and world systems can be authored through data-driven resources.

ecks focuses on reducing the amount of custom code required when building RPGs by providing interconnected systems for quests, dialogue, combat, inventory, progression, localization, and world management.

The framework is developed alongside a full Adventure RPG project, where every system is tested through real gameplay requirements before being expanded into reusable tools.

# Community & Development

Follow the development of ecks and the Adventure RPG project:

- 🎥 YouTube: [unauthorizedlogin](www.youtube.com/@UnauthorizedLogin)
- 💬 Discord: [ecks Community](https://discord.gg/638gZMRxaj)
- 🎮 itch.io: [ecks on itch.io](https://unauthorizedlogin.itch.io)
- 🕹️ gamedev: [Game Development](https://gamedev.net/members/368596-unauthorizedlogin/)

Development updates, system breakdowns, and framework progress are shared through regular devlogs and community discussions.

## **Development Status:**

- Status: ecks is currently in active development.
- Stage: pre-alpha
- Version: 0.52.196
- Date: 7/18/2026

## 📖 Documentation:

- [Systems Documentation](./docs)
- [Changelog](./docs/CHANGELOG.md)
- [Roadmap](./docs/ROADMAP.md)
- [Coming - Contributing](./CONTRIBUTING.md)

## 🎨 Media:

- [Screenshots](./screenshots)
- [Coming - Videos](./videos)
- [Coming - Artwork](./art)

## Built With

- Godot Engine
- GDScript
- Godot Resources
- Data-driven architecture

# 🧠 Framework Philosophy

ecks is built around a creator-first, data-driven architecture designed to separate gameplay systems from the content those systems manage.

The framework follows a simple principle:

> Systems define the rules. Data defines the structure. Content defines the experience.

This separation allows creators to expand RPG content without constantly modifying core gameplay code.

ecks separates framework responsibilities into three layers:

- 🏗️ Systems Layer
- 📚 Data Layer
- 🎮 Content Layer

For a detailed breakdown of each Layer & and its role, documentation can be found here:

- [Architecture Overview](docs/architecture.md)

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

# Engine Features

ecks is a modular, data-driven ARPG framework built around reusable gameplay systems rather than one-off implementations.

Every major feature—from entities and combat to quests, world streaming, localization, and creator tooling—is designed to work together through a shared architecture while remaining independently extensible. 

This allows creators to build, customize, and scale projects without rewriting core framework systems.

Currently Existing Systems:

---

### 💰 Economy Framework System

The Economy System provides the framework's complete commerce and currency architecture.

It is responsible for managing player wealth, merchant interactions, buying and selling, item distribution, and transaction processing through a collection of specialized managers.

For an overview of the system check out the README:

- [Economy Framework](./docs/system/economy/README.md)

Or, if you'd prefer a specific topic you can access them directly here:


- [Currency System](./docs/system/economy/currency_system.md)
- [Shop System](./docs/system/economy/shop_system.md)
- [Shop Vendor](./docs/system/economy/shop_vendor.md)

Note: all of these documents are also available in the subsystems README.

---

### ⚙️ Engine Framework System

The Engine Systems layer provides the foundational runtime infrastructure that powers the Ecks Framework.

The engine layer provides the foundation that all gameplay systems build upon.

For an overview of the system check out the README:

- [Engine Framework](./docs/system/engine/README.md)

Or, if you'd prefer a specific topic you can access them directly here:

- [Boot Orchestrator](./docs/system/engine/boot_orchestrator.md)
- [Game Manager](./docs/system/engine/game_manager.md)
- [Localization](./docs/system/engine/localization.md)
- [Save System](./docs/system/engine/save_system.md)
- [Transition Manager](./docs/system/engine/transition_manager.md)

Note: all of these documents are also available in the subsystems README.

---

### 🧬 Entity Framework System

The Entity Systems layer provides the framework foundation for all gameplay entities within ecks.

This layer contains the systems responsible for defining, controlling, and modifying any entity that exists in the game world. 

For an overview of the system check out the README:

- [Entity Framework](./docs/system/entity/README.md)

Or, if you'd prefer a specific topic you can access them directly here:

- [Behaviors](./docs/system/entity/behaviors.md)
- [Actor Controllers](./docs/system/entity/character_controllers.md)
- [Class System](./docs/system/entity/class_system.md)
- [Combat System](./docs/system/entity/combat_system.md)
- [Difficulty System](./docs/system/entity/difficulty_system.md)
- [Effect System](./docs/system/entity/effect_system.md)
- [NPC System](./docs/system/entity/npc_system.md)
- [Stats System](./docs/system/entity/stats_system.md)
- [XP System](./docs/system/entity/xp_system.md)

Note: all of these documents are also available in the subsystems README.

---

### 🚀 Launch Framework System

The Launch Flow System provides the complete startup pipeline responsible for transitioning the framework from application launch into an active gameplay session.

The Launch Flow separates application startup from gameplay execution, ensuring all required systems are prepared before the player enters the world.

For an overview of the system check out the README:

- [Launch Framework](./docs/system/launch/README.md)

Or, if you'd prefer a specific topic you can access them directly here:

- [Launch System](./docs/system/launch/launch_system.md)

Note: all of these documents are also available in the subsystems README.

---

### 🎒 Loot Framework System

The Loot System provides the centralized item framework responsible for item definitions, ownership, storage, equipment handling, and world-based loot interactions.

The Loot System does not rely on a single universal inventory pipeline. Instead, item categories are handled through dedicated managers responsible for their own routing, behavior, and serialization.

For an overview of the system check out the README:

- [Loot Framework](./docs/system/loot/README.md)

Or, if you'd prefer a specific topic you can access them directly here:

- [Chests System](./docs/system/loot/chests.md)
- [Equipment Manager](./docs/system/loot/equipment_manager.md)
- [Inventory Manager](./docs/system/loot/inventory_manager.md)
- [Item Resource](./docs/system/loot/item_resource.md)

Note: all of these documents are also available in the subsystems README.

---

### 📜 Progression Framework System

The Progression System provides the framework responsible for player progression through dialogue, quests, and narrative-driven gameplay.

Dialogue and quests remain independent systems while integrating through shared runtime managers and data resources.

For an overview of the system check out the README:

- [Progression Framework](./docs/system/progression/README.md)

Or, if you'd prefer a specific topic you can access them directly here:

- [Dialogue System](./docs/system/progression/dialogue_system.md)
- [Quest System](./docs/system/progression/quest_system.md)

Note: all of these documents are also available in the subsystems README.

---

### 🖥️ UI Framework System

The UI System provides the centralized framework responsible for managing the game's user interface, coordinating menu interactions, HUD visibility, and communication between gameplay systems and the player.

At the center of the system is the UI Manager, which acts as the global authority for UI registration, menu lifecycle management, input routing, and player movement locking while interfaces are active.

For an overview of the system check out the README:

- [UI Framework](./docs/system/ui/README.md)

Or, if you'd prefer a specific topic you can access them directly here:

- [Credits Scene](./docs/system/ui/credits_root.md)
- [Event Viewer](./docs/system/ui/event_viewer.md)
- [Game Over](./docs/system/ui/game_over.md)
- [Pause Menu](./docs/system/ui/pause_menu.md)
- [Settings Menu](./docs/system/ui/settings_menu.md)
- [Stats Menu](./docs/system/ui/stats_menu.md)
- [UI Manager](./docs/system/ui/ui_manager.md)

Note: all of these documents are also available in the subsystems README.

---

### 🌎 World Framework System

The World Systems framework provides the foundation for managing world loading, map structure, environmental presentation, interactive objects, and player navigation.

World systems are responsible for managing the game world state while remaining independent from gameplay systems such as quests, combat, and progression.

For an overview of the system check out the README:

- [World Framework](./docs/system/world/README.md)

Or, if you'd prefer a specific topic you can access them directly here:

- [Audio System](./docs/system/world/audio_system.md)
- [Chunk Loader](./docs/system/world/chunk_loader.md)
- [Chunk Manager](./docs/system/world/chunk_manager.md)
- [Destroyables](./docs/system/world/destroyables.md)
- [Environment System](./docs/system/world/environment_system.md)
- [Maps](./docs/system/world/maps.md)

Note: all of these documents are also available in the subsystems README.

---

## 🛠️ Database Framework

The Database Framework provides centralized runtime access to dialogue resources through the engine's standard database architecture.

This architecture provides fast, scalable resource access while maintaining a creator-friendly workflow.

For an overview of the system check out the README:

- [Database Framework](./docs/data/databases/README.md)

Or, if you'd prefer a specific topic you can access them directly here:

- [Class Database](./docs/data/databases/class_database.md)
- [Dialogue Database](./docs/data/databases/dialogue_database.md)
- [Difficulty Database](./docs/data/databases/difficulty_database.md)
- [Item Database](./docs/data/databases/item_database.md)
- [Map Database](./docs/data/databases/map_database.md)
- [Quest Database](./docs/data/databases/quest_database.md)
- [Stat Formula Database](./docs/data/databases/stat_formula_database.md)

Note: all of these documents are also available in the subsystems README.

---

## 🛠️ Generator Framework

The Generator System provides a data-driven content creation pipeline for converting structured creator data into runtime-ready engine resources.

The framework uses generators to create content without requiring manual resource creation or direct code modification.

For an overview of the system check out the README:

- [Generator Framework](./docs/data/generators/README.md)

Or, if you'd prefer a specific topic you can access them directly here:

- [Class Generator](./docs/data/generators/class_generator.md)
- [Class Rules Generator](./docs/data/generators/class_rules_generator.md)
- [Credits Generator](./docs/data/generators/credits_generator.md)
- [Dialogue Generators](./docs/data/generators/dialogue_generators.md)
- [Difficulty Generators](./docs/data/generators/difficulty_generators.md)
- [Item Generator](./docs/data/generators/item_generator.md)
- [Map Generator](./docs/data/generators/map_generator.md)
- [NPC Generator](./docs/data/generators/npc_generator.md)
- [.po Generator](./docs/data/generators/po_generator.md)
- [Quest Generator](./docs/data/generators/quest_generator.md)
- [Stat Formula Generator](./docs/data/generators/stat_formula_generator.md)
- [XP Generator](./docs/data/generators/xp_generators.md)

Note: all of these documents are also available in the subsystems README.

---

## 🛠️ Add Ons

The ecks Framework includes a collection of editor-based creator tools designed to accelerate content production while maintaining framework standards.

These tools extend the Godot editor workflow by allowing developers to create framework-compatible content through dedicated interfaces rather than manually configuring every resource and scene.

For an overview of the system check out the README:

- [Creator Tools](./docs/addons/README.md)

Or, if you'd prefer a specific topic you can access them directly here:

- [Item Creator](./docs/addons/item_creator.md)
- [XP Analyzer](./docs/addons/xp_analyzer.md)

Note: all of these documents are also available in the subsystems README.

---

# Project Identity

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

# 📄 License

ecks is a proprietary RPG framework developed by UnauthorizedLogin.

The source code is private and is not licensed as open source software.

The framework is shared as a development product intended to provide creators with access to its tools, systems, workflows, and authoring pipelines.

Usage rights, distribution rights, and source access are determined by the licensing model provided with ecks.

The framework architecture, code, systems, generators, and associated tools remain the intellectual property of UnauthorizedLogin.

For licensing inquiries or access information, please refer to the official ecks community channels.
