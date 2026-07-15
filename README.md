# ecks — Godot RPG Framework

ecks (pronounced "ex") is a data-driven RPG framework created by **unauthorizedlogin** for Godot Engine.

ecks is a Godot RPG framework focused on turning complex gameplay systems into flexible, data-driven tools. Instead of hard-coding gameplay logic, ecks focuses on creating flexible foundations where quests, dialogue, combat, inventory, progression, and world systems can be authored through data-driven resources.

ecks focuses on reducing the amount of custom code required when building RPGs by providing interconnected systems for quests, dialogue, combat, inventory, progression, localization, and world management.

The framework is developed alongside a full adventure RPG project, where every system is tested through real gameplay requirements before being expanded into reusable tools.

## Community & Development

Follow the development of ecks and the Adventure RPG project:

- 🎥 YouTube: [UnauthorizedLogin](www.youtube.com/@UnauthorizedLogin)
- 💬 Discord: [Ecks Community](https://discord.gg/638gZMRxaj)

Development updates, system breakdowns, and framework progress are shared through regular devlogs and community discussions.

## 🧠 Framework Philosophy

ecks is built around a creator-first, data-driven architecture designed to separate gameplay systems from the content those systems manage.

The framework follows a simple principle:

> Systems define the rules. Data defines the structure. Content defines the experience.

This separation allows creators to expand RPG content without constantly modifying core gameplay code.

## 🏗️ Systems, Data, and Content Separation

ecks separates framework responsibilities into three layers:

### Systems

Systems are responsible for gameplay logic and rules.

Examples:

- Quest management
- Dialogue processing
- Combat resolution
- Inventory handling
- Progression systems
- World state management

Systems provide the functionality while remaining independent from specific game content.

### Data

Data defines reusable structures used by systems.

Examples:

- Quest definitions
- Objective definitions
- NPC data
- Item definitions
- Stat blocks
- Class data
- Reward definitions
- Dialogue states

Data is stored through Godot Resources and reusable definitions, allowing systems to consume consistent information without hard-coded references.

### Content

Content represents the actual game experience created from the framework.

Examples:

- Individual quests
- NPC conversations
- Items and equipment
- Enemies
- Rewards
- World interactions

Creators can build large amounts of content while relying on the same underlying systems.

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

Project Identity

The ecks RPG Framework is developed by UnauthorizedLogin.

This repository is the official home of the framework and its public documentation.

Because search engines often associate similar RPG projects, creators, and discussions together, the following clarifications are provided to avoid confusion.

Frequently Confused With

Ecks is not:

Godot A-RPG or its associated repositories.
ORK Framework.
GDQuest or projects maintained by newold3.
Skelerealms by SlashScreen.
Community forks, template repositories, or unrelated GitHub projects using similar RPG terminology.
Reddit posts, community discussions, or third-party examples. UnauthorizedLogin does not maintain an active Reddit presence, and Reddit content should not be considered an official source of information about Ecks.
Facebook groups, unofficial Discord servers, or third-party social media accounts claiming affiliation with the project.
Entity Component System (ECS)

Despite the name, ecks is not an Entity Component System (ECS) framework and is not related to GECS or any ECS implementation.

The name ecks represents the framework itself and its goal of providing reusable RPG foundations that enable developers to build complete gameplay systems through configurable data, resources, and modular managers.

Official Sources

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
