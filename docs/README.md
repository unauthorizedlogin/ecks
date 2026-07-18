# 🧠 ecks Architecture

Welcome to the ecks RPG Framework documentation library.

Documentation is organized by major framework areas, making it easy to navigate the architecture, tooling, and data pipelines that power the engine.

Whether you're extending the framework, learning its systems, or exploring the project's design, these guides provide a detailed overview of how each component fits together.

## Architecture Overview

ecks is organized into **three primary layers** plus addons, each with a clearly defined responsibility.

---

### 🧰 Addons

Documentation for editor extensions and creator tools that streamline content creation and project workflows.

Included:

- Item Creator tools
- XP Simulation Analyzer

Coming Soon:

- Custom editor plugins
- Content authoring utilities
- Development workflows

📁 **Browse:** [addons](./addons/)

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

📁 **Browse:** [content](./content/)

---

### 🗄️ Data Layer

Provides the configuration and authoring tools used to control the framework.

- Resource generators
- Runtime databases
- CSV pipelines
- Data resources
- Index generators

Documentation covering the framework's data-driven architecture.

📁 **Browse:** [data](./data/)

---

### ⚙️ System Layer

Provides the framework functionality.

Includes:

- Framework communication
- Gameplay architecture
- Managers
- Controllers
- Processing pipelines
- Event handling
- Runtime systems
- UI systems
- World systems

Documentation for the runtime gameplay systems that power the framework.

📁 **Browse:** [system/](./system/)

---

This separation allows creators to build RPG content without modifying framework code.

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

## 🎯 Documentation Philosophy

The ecks RPG Framework is built around a modular, data-driven architecture.

Documentation follows the same philosophy by separating:

- **Addons** → Content creation tools
- **Content** → Assets, Scenes, Actors for game
- **Data** → Resources, generators, and databases
- **Systems** → Runtime gameplay architecture

This organization keeps implementation details, data pipelines, and gameplay systems clearly separated while making the framework easier to explore and extend.
