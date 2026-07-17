# 🧠 ecks Architecture

## Architecture Overview

ecks is organized into **three primary layers**, each with a clearly defined responsibility.

---

### ⚙️ System Layer

Provides the framework functionality.

- Runtime systems
- Gameplay logic
- Managers
- Controllers
- Processing pipelines
- Event handling
- Framework communication

---

### 📚 Data Layer

Provides the configuration and authoring tools used to control the framework.

- Resource definitions
- Configuration assets
- Templates
- Generators
- Database structures
- System settings

---

### 🎮 Content Layer

Contains the actual game built using the framework.

- Scenes
- Maps
- NPCs
- Enemies
- Interactions
- World objects
- Audio
- Visual assets

---

This separation allows creators to build RPG content without modifying framework code.

---

# ⚙️ System Layer

The **System Layer** contains the core ecks framework code.

It is responsible for:

- Runtime systems
- Gameplay logic
- Managers
- Controllers
- Processing pipelines
- Event handling
- Framework communication

## Examples

- QuestManager
- DialogueManager
- CombatManager
- InventoryManager
- SaveManager
- RewardManager

---

## 📁 Folder Organization

Systems are organized into functional categories using a consistent directory structure.

```text
system/
├── economy/
├── engine/
├── entity/
├── launch/
├── legal/
├── loot/
├── progression/
├── ui/
└── world/
```

Each category contains one or more subsystems.

### Example

```text
system/
└── progression/
    ├── achievements/
    ├── dialogue/
    └── quests/
```

Within each subsystem, code is typically separated into runtime logic and development tools.

```text
system/
└── progression/
    └── quests/
        ├── runtime/
        └── tools/
```

---

### Runtime

Contains gameplay code executed while the game is running.

Examples include:

- Managers
- Databases
- Runtime helpers
- Resource databases
- Gameplay logic

---

### Tools

Contains editor-only utilities used to build framework content.

Examples include:

- Resource generators
- Index generators
- CSV importers
- Validation tools
- Asset processing scripts

---

The **System Layer** determines how the framework works.

Creators generally do not modify this layer when creating content.

Instead, systems are configured through the **Data Layer**.

---

# 📚 Data Layer

The **Data Layer** is the creator-facing configuration layer of ecks.

It contains the resources, templates, and tools used to define how systems behave.

## Includes

- Godot Resource definitions
- Configuration assets
- System settings
- Templates
- Generators
- Database structures
- UI menu templates

## Examples

- Quest Definitions
- Objective Definitions
- Dialogue Resources
- NPC Data
- Item Definitions
- Stat Definitions
- Reward Definitions
- UI Configuration Resources

---

## 📁 Folder Organization

The structure of the `data/` directory is **intentionally enforced**.

The framework's resource generators, index generators, and runtime database loaders expect resources to exist in specific locations.

Maintaining this directory structure allows content to be discovered, indexed, and loaded automatically during the build process and boot sequence.

Moving or reorganizing these directories will prevent resources from being generated or discovered correctly.

```text
data/
├── databases/
├── generators/
└── templates/
    └── launch/
```

---

The **Data Layer** acts as the bridge between framework code and authored content.

Creators use the Data Layer to configure systems without editing the underlying implementation.

---

# 🎮 Content Layer

The **Content Layer** contains the actual game content created using ecks.

## Includes

- Scenes
- Maps
- NPC instances
- Enemies
- Interactions
- World objects
- Game-specific assets

---

## 📁 Folder Organization

Unlike the framework itself, the `content/` directory has **no required structure**.

Developers are free to organize their project however best fits their workflow.

### Example

```text
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
```

Or organize by gameplay progression.

```text
content/
├── chapter_01/
├── villages/
├── bosses/
├── dungeons/
└── cinematics/
```

The framework intentionally does not enforce a specific organization.

---

## Content References Data

Content references the **Data Layer** to define behavior.

For example, a scene may contain:

- An NPC using NPC Data
- A quest giver using Quest Definitions
- An enemy using Stat Data
- An interactable object using Interaction Data

The **Content Layer** represents the playable world built on top of ecks.

---

# 🔄 Layer Relationship

The three layers build on one another.

```text
          Content Layer
        (Your Game World)
               ▲
               │
        references data
               │
          Data Layer
(Resources • Templates • Generators)
               ▲
               │
      configures systems
               │
         System Layer
    (Framework Runtime Code)
```

- **System Layer** defines how the framework operates.
- **Data Layer** configures framework behavior through resources, generators, templates, and authoring tools.
- **Content Layer** uses the Data Layer to create the actual game world through scenes and authored content.

---

## 🚀 Boot Pipeline

The **Boot Orchestrator** transforms framework startup from independent autoload initialization into a controlled initialization pipeline.

Rather than every subsystem initializing independently, every manager, database, and framework service enters the game world in a known, deterministic, and reliable order before gameplay begins.
