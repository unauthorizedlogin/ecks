# ⚙️ Settings Menu

The **Settings Menu** provides a centralized configuration interface for player preferences and game options.

It is built around the **SettingsManager autoload**, which handles runtime changes, persistence, and applying settings across the entire game.

The menu is designed as a reusable framework component and can be used from:

- Main Menu
- Pause Menu
- Options Screens
- In-game UI

---

# 🎯 Core Responsibilities

The Settings System manages:

- Audio configuration
- Display configuration
- Brightness control
- Resolution selection
- Fullscreen mode
- VSync
- Save/load access
- Menu navigation

---

# 🧠 Settings Manager Architecture

The **SettingsManager** acts as the authority for all player preferences.

It handles:

- Loading settings on startup
- Applying settings during runtime
- Saving settings permanently
- Restoring previous audio states
- Managing display configuration

Settings are stored locally using:

```
user://settings.cfg
```

---

# 🔊 Audio Settings

The system supports independent audio controls.

Available controls:

- Master Volume
- Music Volume
- Sound Effects Volume
- Global Mute

The manager also stores previous volume states, allowing audio levels to be restored after unmuting.

Settings are applied directly through Godot's AudioServer.

---

# 🖥️ Display Settings

The Settings System supports:

## Fullscreen

Allows switching between:

- Windowed mode
- Fullscreen mode

---

## Resolution

Includes automatic resolution detection and filtering.

Supported aspect ratios:

- 16:9
- 4:3
- 21:9 Ultrawide

Includes support for common resolutions from:

- 640×480
- 1280×720
- 1920×1080
- 2560×1440
- 3440×1440
- 3840×2160

The system automatically filters invalid aspect ratios and sorts available resolutions by pixel count.

---

## VSync

Provides runtime VSync control through ProjectSettings.

---

# 🌞 Brightness Control

The Settings System supports runtime brightness adjustment through a screen overlay.

Features:

- Persistent brightness value
- Runtime adjustment
- Automatic overlay synchronization
- World scene integration

Brightness is controlled by adjusting overlay transparency rather than modifying scene lighting.

---

# 💾 Persistent Configuration

All settings are automatically saved and restored.

Stored values include:

## Audio

- Master volume
- Music volume
- SFX volume
- Mute state
- Previous volume values

## Video

- Fullscreen state
- Resolution
- Brightness
- VSync state

---

# 🧩 Settings Menu Base Class

The `SettingsMenu` class provides a reusable menu foundation.

Shared functionality includes:

- Load Game access
- Settings popup access
- Quit button handling
- Save window support
- Popup closing behavior
- Menu focus management

Menus inheriting from this system automatically receive common navigation behavior.

---

# 🔘 Button & Label Customization

All Settings Menu buttons and labels are creator-ready.

Each UI element supports:

- Enum-based preset selection
- Inspector configuration
- Localization support
- Custom text overrides

Creators can select from built-in localized options or provide their own localized strings.

---

# 🌐 Localization

All visible menu text is prepared for localization.

Default labels:

- Use `tr()`
- Are available through enums
- Require no additional scripting
- Can be replaced with custom localized keys

This allows the same Settings Menu system to support different:

- Genres
- Themes
- Languages
- UI styles

---

# ⚙️ Creator Customization

The Settings Menu is designed as a flexible framework component.

Creators can:

- Add additional settings
- Replace UI layouts
- Add gameplay options
- Add accessibility options
- Create custom configuration panels
- Extend SettingsManager values

---

# 🚀 Framework Role

The Settings System provides a production-ready foundation for managing player preferences.

It separates:

**SettingsManager**
- Stores data
- Applies configuration
- Handles persistence

from:

**SettingsMenu**
- Provides UI access
- Handles navigation
- Presents options

This separation allows settings to remain consistent across the entire game while supporting multiple menu implementations.
