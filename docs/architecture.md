# 🧠 ecks Architecture

## Architecture Overview

ecks is organized into three primary layers:

- System Layer
- Data Layer
- Content Layer

Each layer has a specific responsibility.

The System Layer provides the framework functionality.
The Data Layer provides the configuration and authoring tools used to control that functionality.
The Content Layer contains the actual game content created using the framework.

This separation allows creators to build RPG content without modifying framework code.

# ⚙️ System Layer

The System Layer contains the core ecks framework code.

This layer contains:

- Runtime systems
- Gameplay logic
- Managers
- Controllers
- Processing pipelines
- Event handling
- Framework communication

Examples:

- QuestManager
- DialogueManager
- CombatManager
- InventoryManager
- SaveManager
- RewardManager

📁 Folder Organization

Systems are organized into functional categories using a consistent directory structure.

systems/
    economy/
    engine/
    entity/
    launch/
    legal/
    loot/
    progression/
    ui/
    world/


Each category contains one or more subsystems.

Example:

systems/
└── progression/
    ├── achievements/
    ├── dialogue/
    └── quests/

Within each subsystem, code is typically separated into runtime logic and development tools.

systems/
└── progression/
    └── quests/
        ├── runtime/
        └── tools/

Runtime

Contains gameplay code used by the engine at runtime.

Examples:

- Managers
- Databases
- Runtime helpers
- Resource databases
- Gameplay logic

Tools

Contains editor-only utilities used to build framework content.

Examples:

- Resource generators
- Index generators
- CSV importers
- Validation tools
- Asset processing scripts

The System Layer is responsible for determining how the framework works.

Creators do not directly modify this layer when creating content.

Instead, systems are controlled through the Data Layer.


# 📚 Data Layer

The Data Layer is the creator-facing configuration layer of ecks.

This layer contains the resources, templates, and tools used to define how systems behave.

The Data Layer includes:

- Godot Resource definitions
- Configuration assets
- System settings
- Templates
- Generators
- Database structures
- UI menu templates

Examples:

- Quest Definitions
- Objective Definitions
- Dialogue Resources
- NPC Data
- Item Definitions
- Stat Definitions
- Reward Definitions
- UI Configuration Resources

# 📁 Folder Organization

The structure of the `data/` directory is **intentionally enforced**.

- The framework's resource generators, index generators, and runtime database loaders expect resources to exist in specific locations. 
- Maintaining this directory structure allows content to be discovered, indexed, and loaded automatically during the build process and boot sequence.
- Moving or reorganizing these directories will prevent resources from being generated or discovered correctly.

```text
data/
├── databases/
├── generators/
└── templates/
    └── launch/
```

The Data Layer acts as the bridge between framework code and authored content.

Creators use the Data Layer to configure systems without editing the underlying implementation.

# 🎮 Content Layer

The Content Layer contains the actual game content created using ECKS.

This includes:

- Scenes
- Maps
- NPC instances
- Enemies
- Interactions
- World objects
- Game-specific assets

📁 Folder Organization

Unlike the framework itself, the content/ directory has no required structure.

Developers are free to organize their project however best fits their workflow.

Example:

content/
├── characters/
├── enemies/
├── items/
├── maps/
├── ui/
├── audio/
├── sprites/
├── animations/
└── effects/

Or:

content/
├── chapter_01/
├── villages/
├── bosses/
├── dungeons/
└── cinematics/

The framework does not enforce a specific organization.

Content references the Data Layer to define behavior.

Examples:

A scene may contain:

- An NPC using NPC Data
- A quest giver using Quest Definitions
- An enemy using Stat Data
- An interactable object using interaction data

The Content Layer represents the playable world built on top of ecks.

## 🔄 Layer Relationship

The **System Layer** is the foundation of ecks and contains the core framework logic.

The **Data Layer** configures and controls how systems are used through resources, generators, templates, and authoring tools.

The **Content Layer** uses the Data Layer to create the actual game world through scenes and authored content.

The Boot Orchestrator transforms framework startup from independent autoload initialization into a controlled initialization pipeline where every subsystem enters the game world in a known and reliable state.

