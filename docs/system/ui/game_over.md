# 💀 Game Over

The **Game Over** scene provides a fully customizable death screen that creators can adapt to fit virtually any game style.

Rather than hardcoding button labels, each built-in action includes a large collection of localized presets that can be selected directly from the Inspector. For projects requiring unique wording, every button also supports localized custom text overrides.

---

# 🎯 Features

- Ready-to-use Game Over screen
- Multiple built-in recovery actions
- Fully localized button presets
- Localized custom text overrides
- Inspector-driven configuration
- Creator-friendly customization
- No scripting required for label changes

---

# 🔘 Included Actions

## 🔁 Restart Level

Restarts the current level through the GameManager.

### Includes

- 25 built-in localized label presets
- Optional custom localized text
- Inspector dropdown selection
- One-click restart functionality

Examples include:

- Restart
- Retry Level
- Restart Stage
- Replay Level
- Start Over
- Restart Mission
- Retry Game
- and many more.

---

## 💾 Retry From Checkpoint

Loads the player's last checkpoint or autosave.

### Includes

- 31 localized preset labels
- Optional localized override
- Inspector configuration
- Integrated autosave loading

Examples include:

- Retry
- Load Checkpoint
- Retry From Checkpoint
- Continue From Checkpoint
- Load Last Save
- Respawn at Checkpoint
- Restart From Checkpoint
- and many more.

---

## ❤️ Revive

Revives the player without reloading the level.

The default implementation creates a revive effect and restores the player's Vital Component before closing the Game Over screen.

### Includes

- 30 localized preset labels
- Optional localized override
- Inspector configuration
- Built-in revive support
- Automatic Game Over dismissal

Examples include:

- Revive
- Continue
- Rise Again
- Rebirth
- Resurrect
- Second Chance
- Fight On
- Wake Up
- and many more.

---

## 🏠 Return to Menu

Returns the player to the game's main menu.

### Includes

- 30 localized preset labels
- Optional localized override
- Inspector configuration
- Built-in GameManager integration

Examples include:

- Return to Main Menu
- Return to Title Screen
- Return Home
- Return to Hub
- Return to Start Menu
- Return to Lobby
- Return to World Map
- and many more.

---

# 🌐 Localization

Every built-in preset is already wrapped with `tr()` and immediately available for localization.

Creators can simply choose a preset from the Inspector without writing code.

If a project requires unique wording, enabling **Custom Text Override** allows any custom string to be localized using the exact same workflow.

---

# ⚙️ Designed for Customization

The supplied Game Over scene serves as a production-ready starting point.

Creators are free to:

- Rearrange the layout
- Replace artwork
- Add animations
- Add additional buttons
- Remove unused actions
- Replace button behavior
- Theme the UI for any genre

The included implementation provides the core recovery flow while remaining flexible enough to fit arcade games, RPGs, roguelikes, platformers, survival games, and completely custom gameplay experiences.
