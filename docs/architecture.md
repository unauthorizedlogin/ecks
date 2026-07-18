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



---



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
