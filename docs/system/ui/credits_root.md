# 🎬 Credits System — Dynamic Attribution & Presentation Layer

The Credits System provides a fully data-driven framework for displaying contributor information, music credits, legal attributions, special thanks, and dedication sections.

Credits are generated dynamically from external data rather than being hardcoded into the UI scene.

Architecture:

```text
Credits Data Resource
          ↓
Credits Root Controller
          ↓
Dynamic UI Generation
          ↓
Scrollable Credits Presentation
```

---

# 🎯 Core Responsibility

The Credits System manages:

* contributor listings
* department organization
* role/title presentation
* music attribution
* legal notices
* special thanks
* dedication sections
* visual formatting
* localization support

The system separates **credits content** from **credits presentation**.

---

# 📄 Data-Driven Credits

Credits content is loaded from:

```gdscript
credits_data_path
```

Default:

```text
data/templates/ui/menus/credits/credits_data.gd
```

The UI does not contain fixed names or entries.

Instead:

```text
Credits Data
      ↓
Credits Root
      ↓
Generated UI
```

This allows credits to be updated without modifying the scene.

---

# 🏗 Dynamic UI Generation

Credits are constructed at runtime.

The system generates:

```text
Credits Root
 |
 └── ScrollContainer
      |
      └── VBoxContainer
            |
            ├── Departments
            ├── Music
            ├── Legal
            ├── Special Thanks
            └── Dedication
```

Each section is created only when data exists.

---

# 👥 Department & Contributor System

Departments organize contributors into logical groups.

Example:

```text
Development
 ├── Developer
 ├── Programmer

Art
 ├── Artist
 ├── Animator

Design
 ├── Designer
```

Each contributor supports:

```text
Name
Role / Title
```

The system automatically builds formatted contributor rows.

---

# 🎨 Visual Customization

Credits presentation is fully inspector-driven.

Customizable:

## Contributor Names

```gdscript
name_font_size
name_color
name_outline_size
name_alignment
```

---

## Contributor Roles

```gdscript
title_font_size
title_color
title_outline_size
title_alignment
```

---

## Section Headers

```gdscript
section_title_font_size
section_title_color
section_alignment
```

---

# 🎵 Music Credits

Music attribution has its own presentation layer.

Supports:

```text
Track Title
Composer
Performer
Context
```

Example:

```text
Main Theme

Composed by: Artist Name
Performed by: Performer Name

(Battle Music)
```

Music styling is independently configurable.

---

# 📜 Legal Attribution System

Supports dedicated legal sections for:

* copyright notices
* licenses
* third-party assets
* attribution requirements

Displayed separately from contributor credits.

---

# 💖 Special Thanks

Dedicated section for non-development acknowledgements.

Supports:

```text
Name
Role / Description
```

Uses separate styling from standard contributors.

---

# 🙏 Dedication Section

Provides a final dedicated acknowledgement area.

Supports:

```text
Name
Optional Title
```

Designed for personal dedications or final messages.

---

# 🌍 Localization Support

Static section titles use localization:

```gdscript
tr()
```

Examples:

```text
Music
Legal / Attributions
Special Thanks
Dedicated To
```

Credits content can therefore support translated presentation.

---

# 🛠 Editor Preview Support

Credits Root runs in:

```gdscript
@tool
```

mode.

Changes to credits data can be previewed directly inside the editor.

The system refreshes periodically while editing, allowing visual iteration without running the game.

---

# 📐 Layout Control

Credits spacing is fully configurable.

Supports:

```text
Top spacing
Department spacing
Music spacing
Legal spacing
Thanks spacing
Dedication spacing
Bottom padding
```

This allows cinematic scrolling layouts without modifying UI scenes.

---

# 🧱 Framework Role

The Credits System provides:

- ✅ data-driven contributor management
- ✅ dynamic UI generation
- ✅ localization-ready presentation
- ✅ inspector-controlled styling
- ✅ music attribution support
- ✅ legal attribution support
- ✅ editor preview workflow
- ✅ reusable end-game presentation layer

The credits system functions as a standalone presentation framework that separates **content management**, **visual styling**, and **runtime generation** into independent layers.

# 🎵 Credits Music System

The Credits Music System provides dedicated background music playback for the game's credits sequence.

Unlike the runtime music system used during gameplay, this controller is designed specifically for the credits scene, automatically managing a playlist from beginning to end without player interaction.

---

# 🎼 Credits Playlist

The system plays a configurable list of music tracks assigned to the credits scene.

### Responsibilities

- Load the credits playlist
- Begin playback automatically
- Maintain the current playlist position
- Play tracks in sequence

The first song begins automatically when the credits scene is loaded.

---

# ▶️ Automatic Playback

The credits music system starts playback as soon as the scene initializes.

If one or more songs are assigned, the playlist begins from the first track without requiring any user input.

---

# 🔄 Continuous Playlist Rotation

When a track finishes playing, the system automatically advances to the next song.

### Playback Flow

1. Detect the end of the current track.
2. Advance to the next playlist entry.
3. Wrap back to the first song when the playlist reaches the end.
4. Continue playback automatically.

This allows the credits sequence to continue uninterrupted regardless of its duration.

---

# 🎶 Scene-Specific Design

This controller is intentionally isolated from the main gameplay music systems.

It exists solely to provide a self-contained soundtrack for the credits scene, ensuring that credits playback remains independent of world music, combat themes, or environmental audio.

---

# 🧠 Runtime Design

The Credits Music System delegates audio playback to an `AudioStreamPlayer` while managing playlist order and progression.

Its responsibilities are limited to:

- Playlist management
- Track selection
- Automatic progression
- Continuous looping

This lightweight design keeps the credits sequence simple and self-contained.

---

# 📌 Summary

The Credits Music System provides automatic playlist playback for the game's credits scene, sequentially playing assigned music tracks and looping them continuously until the player exits the credits.
