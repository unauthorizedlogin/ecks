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


