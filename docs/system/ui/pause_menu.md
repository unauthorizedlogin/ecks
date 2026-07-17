# ⏸️ Pause Menu

The **Pause Menu** is a state-driven gameplay controller rather than a traditional menu system.

Instead of only acting as a collection of UI buttons, the Pause Menu directly manages the game's paused state through integration with **GameManager**, while coordinating UI visibility through **UIManager**.

The system provides a customizable foundation for creators while maintaining centralized control over gameplay interruption, saving access, and menu state transitions.

---

# 🎯 Core Responsibilities

The Pause Menu handles:

- Pausing gameplay
- Resuming gameplay
- Opening and closing pause UI
- Accessing save functionality
- Managing pause input
- Preventing accidental double-toggle actions
- Coordinating with global game state
- Integrating with the UI management layer

---

# 🧠 State Controller Design

Unlike traditional menus, the Pause Menu functions as a gameplay state controller.

When opened:

- GameManager switches into the paused state
- Gameplay input is suspended through the game state system
- Pause UI becomes visible
- Available pause actions become accessible

When closed:

- GameManager resumes gameplay
- Pause UI is hidden
- UIManager clears active menus
- Gameplay continues immediately

This allows the pause system to remain independent from individual gameplay systems while still controlling the overall game state.

---

# 🎮 Pause Controls

## Toggle Pause

The menu listens for the configured pause action and automatically toggles between:

- Active gameplay
- Paused gameplay

A cooldown system prevents accidental double activation when pausing or resuming.

---

# 💾 Save Access

The Pause Menu includes built-in access to save functionality.

The save button opens the save interface while maintaining the paused game state.

Creators can customize the save button label through:

- Built-in localized presets
- Custom localized overrides

---

# 🔘 Included UI Components

## 💾 Save Menu Button

Provides access to save functionality.

### Features

- 21 built-in localized label options
- Custom localized text override
- Inspector-based configuration

Examples:

- Open Save Menu
- Save Menu
- Save Screen
- Manage Save Files
- Capture Game State
- Store Game Data
- Write Save File

---

## 💾 Save Action Button

Handles the actual save action.

### Features

- 22 localized label presets
- Custom localized override
- Inspector-driven customization

Examples:

- Save Game
- Save Now
- Create Save
- Save Progress
- Backup Progress
- Record Progress
- Write Save File

---

## ▶️ Resume / Unpause Button

Returns the player to gameplay.

### Features

- 25 localized label presets
- Custom localized override
- Automatic GameManager resume
- Automatic UI cleanup

Examples:

- Continue
- Resume
- Resume Game
- Return to World
- Back to Game
- Jump Back In
- Continue Journey

---

## 🏷️ Pause Title Label

The pause title supports dynamic localized text selection.

### Features

- 8 built-in localized labels
- Custom localized override
- Inspector-based customization

Examples:

- Pause Menu
- Game Paused
- Break Time
- Escape Menu
- Take a Break

---

# 🌐 Localization

Every visible pause menu label is localization-ready.

All default labels:

- Use `tr()`
- Are available through enums
- Are selectable from the Inspector
- Require no additional scripting

Creators can either:

1. Select an existing localized phrase  
2. Provide their own custom localized key override

This allows the same pause system to support different genres, themes, and writing styles.

---

# ⚙️ Creator Customization

The Pause Menu is designed as a framework component.

Creators can:

- Replace menu artwork
- Add additional buttons
- Add inventory, quests, character sheets, or settings
- Change pause behavior
- Customize button actions
- Modify state transitions
- Replace default labels

---

# 🔌 System Integration

The Pause Menu integrates with:

## GameManager

Responsible for:

- Pause state changes
- Resume state changes
- Gameplay state authority

## UIManager

Responsible for:

- Closing active menus
- Coordinating UI visibility
- Connecting pause behavior with the larger UI system

---

# 🚀 Framework Role

The Pause Menu provides a production-ready pause foundation that combines:

- State management
- UI control
- Save access
- Localization support
- Creator customization

It is designed to act as the central pause controller while remaining flexible enough for RPGs, ARPGs, adventure games, and custom gameplay frameworks.
