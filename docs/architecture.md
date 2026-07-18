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


---

## 🚀 Boot Pipeline

The **Boot Orchestrator** transforms framework startup from independent autoload initialization into a controlled initialization pipeline.

Rather than every subsystem initializing independently, every manager, database, and framework service enters the game world in a known, deterministic, and reliable order before gameplay begins.
