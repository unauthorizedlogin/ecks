# 🖥️ UI System

## Overview

The UI System provides the centralized framework responsible for managing the game's user interface, coordinating menu interactions, HUD visibility, and communication between gameplay systems and the player.

At the center of the system is the **UI Manager**, which acts as the global authority for UI registration, menu lifecycle management, input routing, and gameplay state synchronization.

The system is designed around a centralized architecture separating:

- UI registration
- Menu lifecycle management
- HUD visibility
- Global UI input handling
- Gameplay-to-UI communication

---

# 🧠 System Architecture

```text
                  Gameplay Systems
                         │
                         ▼
                    UI Manager
                         │
 ┌──────────────┬──────────────┬──────────────┐
 ▼              ▼              ▼              ▼

HUD          Menus         Dialogs        Popups
````

The UI Manager coordinates interface lifecycle while individual UI systems remain responsible for their own presentation and behavior.

---

# 🔗 UI System Documentation

The following systems make up the UI layer.

| System | Purpose | Documentation |
|---|---|---|
| 🎬 Credits System | Data-driven contributor credits, acknowledgements, and presentation management      | [Credits System](./credits/) |
| 💬 Dialogue UI    | Dialogue presentation, NPC info, conversation, and player interaction handling      | [Dialogue UI](./dialogue_ui.md)     |
| 📢 Event Viewer   | Centralized gameplay notifications, event routing, and message filtering            | [Event Viewer](./events/)   |
| 💀 Game Over      | Player death interface and post-death state management                              | [Game Over](./game_over.md)         |
| ⏸️ Pause Menu     | Gameplay pause control, state management, and menu coordination                     | [Pause Menu](./pause_menu.md)       |
| ⚙️ Settings Menu  | Player preferences, configuration, and persistent settings management               | [Settings Menu](./settings_menu.md) |
| 📊 Stats Menu     | Character progression, stats, resources, and combat information display             | [Stats Menu](./stats_menu.md)       |
| 🖥️ UI Manager     | Central UI authority for registration, lifecycle management, and input coordination | [UI Manager](./ui_manager.md)       |

---

# Summary

The UI System provides a unified interface management layer through centralized coordination, modular UI components, and gameplay system integration.

Each UI subsystem specializes in a specific presentation or interaction responsibility while connecting through shared UI management infrastructure.
The README acts as a **map**, not a duplicate manual.
