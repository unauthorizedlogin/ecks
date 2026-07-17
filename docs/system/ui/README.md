# 🖥️ UI System

## Overview

The UI System provides the centralized framework responsible for managing the game's user interface, coordinating menu interactions, HUD visibility, and communication between gameplay systems and the player.

At the center of the system is the **UI Manager**, which acts as the global authority for UI registration, menu lifecycle management, input routing, and player movement locking while interfaces are active.

The system is designed around a **centralized management architecture**, separating:

- UI registration
- Menu lifecycle management
- HUD visibility
- Global UI input handling
- Movement lock coordination
- System-to-UI communication

Rather than allowing individual menus to operate independently, the UI Manager provides a single point of control for opening, closing, and coordinating interfaces throughout the game.

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
```

The UI Manager maintains references to the active interface, routes global UI events, and ensures gameplay state remains synchronized while menus are open.

---

## 🖥️ UI Manager

The UI Manager serves as the central controller for the entire user interface.

Responsibilities include:

- UI initialization
- Menu registration
- Opening and closing menus
- HUD visibility management
- Global UI input handling
- Movement lock coordination
- Runtime UI communication

The manager does not own menu-specific logic. Individual interfaces remain responsible for their own presentation and behavior while the UI Manager coordinates their lifecycle.

---

## 📚 Documentation

- 📄 [UI Manager Documentation](ui/ui_manager.md)

---

## 🎬 Credits System

The Credits System provides a fully data-driven framework for presenting contributor acknowledgements, music credits, legal attributions, special thanks, and dedication sections.

Credits are generated dynamically from external data resources rather than being hardcoded into the interface, allowing content, styling, and presentation to remain independent.

Features include:

- Dynamic credits generation
- Department and contributor organization
- Music attribution
- Legal and licensing notices
- Special thanks and dedication sections
- Localization-ready presentation
- Inspector-driven visual customization
- Editor preview support
- Dedicated credits music playback

---

## 📚 Documentation

- 📄 [Credits System Documentation](ui/credits_root.md)
- ⚙️ [Credits Generator Documentation](../data/generators/credits_generator.md)

---

## 📢 Event Viewer

The Event Viewer provides a centralized player-facing notification system for gameplay events across the framework.

Built around the **Event Manager** and **Filter Registry**, it collects, categorizes, filters, and displays messages from gameplay systems without requiring those systems to communicate directly with the UI.

Features include:

- Centralized event logging
- Multi-channel event routing
- Custom event views and filters
- Runtime event history
- Persistent viewer configuration
- Color-coded message formatting
- Decoupled UI architecture

Every major gameplay system reports events through this shared pipeline, providing a consistent and extensible notification framework.

---

## 📚 Documentation

- 📄 [Event Manager Documentation](ui/event_viewer.md)

---

## 💀 Game Over

The Game Over screen provides the player-facing interface displayed when the player dies.

It serves as the transition point between gameplay and recovery, presenting available post-death actions while coordinating with the game's UI and transition systems.

---

## 📚 Documentation

- 📄 [Game Over Documentation](ui/game_over.md)

---

## ⏸️ Pause Menu

The Pause Menu provides a state-driven gameplay interruption system rather than a traditional UI-only menu.

It integrates with the game's global state and UI management systems to control pausing, resuming, save access, and menu lifecycle behavior.

Features include:

- Gameplay pause/resume control
- Game state integration
- Save system access
- UI state coordination
- Input protection against accidental toggles
- Fully localized menu labels
- Inspector-driven customization

The Pause Menu acts as the central pause controller while allowing creators to expand it with additional gameplay interfaces such as settings, inventory, quests, or character management.

---

## 📚 Documentation

- 📄 [Pause Menu Documentation](ui/pause_menu.md)

---

## ⚙️ Settings Menu

The Settings Menu provides the player-facing interface for configuring game preferences and options.

It connects with the **SettingsManager** system to handle runtime configuration changes, persistent settings storage, and applying preferences throughout the game.

The menu is designed as a reusable framework component that can be accessed from multiple UI locations, including:

- Main Menu
- Pause Menu
- In-game Options Screens

Features include:

- Audio configuration
- Display settings
- Resolution management
- Fullscreen support
- VSync control
- Brightness adjustment
- Persistent player preferences
- Localization-ready controls

The Settings Menu separates presentation from configuration logic, allowing the SettingsManager to remain the authority for storing and applying player preferences.

---

## 📚 Documentation

- 📄 [Settings Menu Documentation](ui/settings_menu.md)

---

## 📊 Player Stats Menu

The Player Stats Menu provides the player-facing interface for viewing character progression, combat values, resources, and experience information.

It functions as a data-driven presentation layer connected to the player's stat and progression components, allowing character information to be displayed without manually managing individual values.

Features include:

- Attribute and derived stat display
- Combat value display
- Resource tracking
- Experience and level information
- Dynamic stat updates
- Localization-ready labels
- Component-based data binding
- Expandable character sheet structure

The Stats Menu reads from existing gameplay systems such as `StatBlock`, `VitalComponent`, and `PlayerLevelComponent`, ensuring displayed values always reflect the current player state.

---

## 📚 Documentation

- 📄 [Player Stats Menu Documentation](ui/stats_menu.md)
