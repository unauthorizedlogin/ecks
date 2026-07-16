# ecks — Godot RPG Framework

ecks (pronounced "ex") is a data-driven RPG framework created by **ReD** AKA **unauthorizedlogin** for Godot Engine.

ecks is a Godot RPG framework focused on turning complex gameplay systems into flexible, data-driven tools. Instead of hard-coding gameplay logic, ecks focuses on creating flexible foundations where quests, dialogue, combat, inventory, progression, and world systems can be authored through data-driven resources.

ecks focuses on reducing the amount of custom code required when building RPGs by providing interconnected systems for quests, dialogue, combat, inventory, progression, localization, and world management.

The framework is developed alongside a full Adventure RPG project, where every system is tested through real gameplay requirements before being expanded into reusable tools.

## Community & Development

Follow the development of ecks and the Adventure RPG project:

- 🎥 YouTube: [unauthorizedlogin](www.youtube.com/@UnauthorizedLogin)
- 💬 Discord: [ecks Community](https://discord.gg/638gZMRxaj)
- 🎮 itch.io: [ecks on itch.io](https://unauthorizedlogin.itch.io)
- 🕹️ gamedev: [Game Development](https://gamedev.net/members/368596-unauthorizedlogin/)

Development updates, system breakdowns, and framework progress are shared through regular devlogs and community discussions.

## 🧠 Framework Philosophy

ecks is built around a creator-first, data-driven architecture designed to separate gameplay systems from the content those systems manage.

The framework follows a simple principle:

> Systems define the rules. Data defines the structure. Content defines the experience.

This separation allows creators to expand RPG content without constantly modifying core gameplay code.

## 🏗️ Systems, Data, and Content Separation

ecks separates framework responsibilities into three layers:

### Systems

Systems implement gameplay rules and behavior—not game-specific content.

Examples include:

Quest management
Dialogue processing
Combat resolution
Inventory handling
Character progression
World state management
Economy and currencies
UI coordination

Content is defined through resources, while systems provide the runtime logic that consumes those resources.

#🏗️ Standard System Architecture

Most systems follow a common architecture.

#Manager (Autoload)

The *Manager serves as the runtime authority for the subsystem.

Examples:

QuestManager
InventoryManager
CombatManager
GameManager
DialogueManager

Responsibilities include:

gameplay logic
runtime state
event routing
player interaction
coordination between systems
Database (Autoload)

Content-driven systems typically expose a *Database autoload.

Examples:

ItemDatabase
QuestDatabase
ClassDatabase
DialogueDatabase
NPCDatabase

The database acts as the public interface for accessing loaded content.

Resource Database

Behind each database is a resource registry responsible for discovering and loading framework resources.

Responsibilities include:

scanning generated indexes
loading resources
caching content
lookup by ID

This layer separates resource loading from gameplay logic.

Generators

Content pipelines are built around editor tools that generate framework data automatically.

Typical generators include:

Resource Generators
Index Generators
Localization Generators
Import Utilities

These tools convert source data (CSV, spreadsheets, etc.) into optimized runtime resources.

Resource Scripts

Resource classes define the data consumed by systems.

Examples include:

QuestDefinition
ItemResource
ClassRuleResource
NPCData
DialogueResource

Resources contain configuration only, while systems determine how they behave during gameplay.

### 📦 Data

The **`data/`** directory contains the framework's reusable content, templates, generators, and generated resource databases.

Unlike the `systems/` directory, **no gameplay scripts or runtime logic live here.**

Instead, this directory contains the assets and data consumed by the runtime systems.

---

# 🎯 Purpose

The data layer provides the framework's configurable content.

Examples include:

* Items
* Quests
* Dialogue
* NPCs
* Classes
* Maps
* UI scenes
* Character templates

Everything in `data/` is intended to be customized, generated, or instantiated by the systems layer.

Runtime behavior always lives inside `systems/`.

---

# 📁 Folder Organization

```text
data/
├── databases/
├── generators/
└── templates/
    └── launch/
```

The structure of the `data/` directory is **intentionally enforced**.

- The framework's resource generators, index generators, and runtime database loaders expect resources to exist in specific locations. 
- Maintaining this directory structure allows content to be discovered, indexed, and loaded automatically during the build process and boot sequence.
- Moving or reorganizing these directories will prevent resources from being generated or discovered correctly.


---

## 🗄️ Databases

The `databases/` directory contains the framework's generated resources.

Examples include:

* Item Resources
* Quest Resources
* Dialogue Resources
* NPC Resources
* Class Resources
* Level Resources

These resources are loaded by the corresponding system databases during boot.

Index generators scan these directories to build the lookup tables used by runtime systems.

All framework resources should be stored and organized here so they can be discovered automatically.

---

## ⚙️ Generators

The `generators/` directory contains the editor tooling used to build the framework's runtime data.

Typical contents include:

* `*_strings.csv` localization files
* Resource Generators
* Index Generators
* CSV import tools

Each major subsystem generally maintains its own generator pipeline.

Examples:

```text
Class Generator
Class Rules Generator
Item Generator
Quest Generator
Dialogue Generator
NPC Generator
```

Generators convert editable source data into optimized Godot resources and indexes used by the engine.

---

## 🎨 Templates

Templates provide ready-to-use framework content that can be customized and deployed into a project's game content.

Examples include:

* UI menus
* Characters
* Items
* Maps
* Common scenes
* Framework prefabs

These templates are designed to serve as reusable starting points rather than gameplay logic.

Any scripts attached to these scenes originate from the corresponding subsystem within `systems/`.

---

## 🚀 Launch

The `templates/launch/` directory contains the customizable startup flow for projects built with the framework.

Typical scenes include:

* Boot Loader
* Start Menu
* Launch Injector

Projects can replace or extend these scenes while preserving the framework's initialization pipeline.

---

# 🏗️ Relationship to Systems

The framework follows a strict separation between data and logic.

```text
Data
    ↓
Resources
    ↓
Systems
    ↓
Gameplay
```

* **`systems/`** contains all runtime scripts, managers, databases, and gameplay rules.
* **`data/`** contains the resources, scenes, templates, and generated content those systems consume.
* **No gameplay logic lives inside the `data/` directory.**

This separation keeps framework behavior modular while allowing projects to customize content without modifying the underlying engine architecture.


### 🎮 Content

The content/ directory is where developers build their actual game.

Unlike the framework's data/ directory, which provides reusable templates and generated resources, the content/ directory contains project-specific assets, scenes, and resources created by the developer.

This is the game's implementation layer.

🎯 Purpose

Content is where creators extend the framework into their own RPG.

Typical examples include:

Maps
Characters
NPCs
Enemies
Items & Equipment
Skills
UI artwork
Sprites
Animations
Audio
Visual effects
Game-specific scenes

Framework templates are intended to be duplicated, customized, and expanded here rather than modified directly.

🧱 Extending the Framework

Projects typically create their own scenes by extending the framework templates.

For example:

Framework Template
        ↓
Project Scene
        ↓
Game Content

A character, item, or menu can inherit from a framework template while adding project-specific visuals, behavior, and configuration.

This keeps framework updates separate from game content.

🏗️ Relationship to the Framework

The project architecture is intentionally separated into three distinct layers:

Systems
    ↓
Data
    ↓
Content
systems/ defines gameplay logic and engine behavior.
data/ provides reusable templates, generators, and framework resources.
content/ contains the developer's actual game.

This separation allows creators to build entirely different games on the same framework while keeping engine code, reusable framework assets, and project content cleanly isolated.

---

## 🎛️ Inspector-Driven Configuration

ecks is designed around Godot's Inspector workflow.

Framework systems expose configurable properties through exported variables, allowing creators to customize behavior directly in the editor.

Examples include:

- Quest behavior settings
- Tracking limits and HUD configuration
- Objective parameters
- Actor properties
- Combat settings
- UI layout options
- Resource references

This keeps configuration visible, editable, and accessible without requiring creators to constantly modify scripts.

---

## ⚙️ Data-Driven Authoring Pipelines

ecks includes generator workflows designed for large-scale content creation.

Creators can author content through structured formats and automatically generate Godot resources.

Supported workflows include:

- CSV-based authoring
- Automated Resource generation
- Database indexing
- Localization pipelines
- Organized output routing
- Content validation workflows

These pipelines allow RPG projects to manage hundreds or thousands of pieces of content while keeping data organized and consistent.

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

## Features

### 📊 Event Viewer System
- Centralized gameplay event tracking and feedback system
- Custom event channels for organizing gameplay information
- Configurable filters and display categories
- Color-coded event messages for different gameplay events
- Localization-ready event messages
- Saveable viewer configuration

### 📈 Stats & Progression System
- Data-driven actor stat architecture
- Shared stat identity across players, NPCs, enemies, and future actors
- Primary, derived, offensive, defensive, and resistance stats
- Flexible class and character progression support
- Resource-based stat definitions
- Extensible calculations for future mechanics

### 🗡️ Combat System
- Event-driven combat pipeline
- Shared actor identity system
- Damage, effects, resistances, and combat events
- Combat-to-quest integration

### 📜 Quest System
- Data-driven quest definitions
- Configurable objective types
- Quest chains and progression
- NPC-driven quest interactions
- Story and side quest tracking
- Author quests without modifying core systems

### 💬 Dialogue System
- NPC interaction framework
- Quest-aware dialogue states
- Flavor dialogue support
- Data-driven dialogue resources

### 🎒 Inventory & Items
- Item database system
- Equipment management
- Consumables and resources
- Data-driven item definitions

### 🌎 World Systems
- Persistent world state
- Save/load framework
- Scene and chunk management
- Content organization tools

### 🌐 Localization
- Data-driven localization pipeline
- Multi-language support
- Generator-based workflow

### 🛠️ Creator Tools

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

## Development Status

ecks is currently in active development.

The framework is being developed alongside an adventure RPG project, with systems being tested and expanded through real gameplay requirements.

## Built With

- Godot Engine
- GDScript
- Godot Resources
- Data-driven architecture

## Roadmap

Future development includes:
- Additional RPG mechanics
- Expanded creator tools
- More content authoring workflows
- Framework documentation

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
