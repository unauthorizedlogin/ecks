# 🌗 Transition Manager — Scene & Gameplay Transition Control

The Transition Manager provides the visual transition layer for the Ecks Framework.

It centralizes screen transition effects and provides a reusable interface for any system that needs to visually separate gameplay states.

Primary responsibilities:

* Screen fade effects
* Scene transition visuals
* Gameplay interruption transitions
* Configurable fade timing
* Transition sequencing

The Transition Manager does **not** control scene loading or gameplay state.

Instead:

* `GameManager` decides **when** a transition happens
* `TransitionManager` controls **how the transition looks**

---

# 🧠 Transition Authority Separation

The Transition Manager exists as a presentation layer.

Architecture:

```text id="d5g2hs"
GameManager

   |
   | Request Transition
   ↓

TransitionManager

   |
   | Fade Animation
   ↓

Player Screen
```

This keeps visual effects independent from:

* world loading
* save loading
* menu flow
* gameplay systems

---

# 🎭 Canvas Layer Architecture

TransitionManager extends:

```gdscript
CanvasLayer
```

This allows the transition overlay to remain above all gameplay content.

Render order:

```text id="h9x3dk"
UI Layer

      ↓

Transition Layer

      ↓

World

      ↓

Player
```

The fade effect remains visible regardless of:

* current scene
* camera movement
* world changes

---

# 🚀 Initialization

TransitionManager uses:

```gdscript
initialize()
```

with:

```gdscript
is_initialized
```

to prevent duplicate setup.

Startup flow:

```text id="q1t8x5"
Boot

 ↓

TransitionManager.initialize()

 ↓

Ready For Transitions
```

---

# 🎨 Fade Controller

The Transition Manager uses:

```gdscript
fade_rect
```

as the visual overlay.

Default:

```gdscript
Color.BLACK
```

The overlay can be customized through:

```gdscript
fade_rect_color
```

Supports:

* black fades
* white fades
* custom colors
* stylistic transitions

---

# ⏳ Transition Timing Configuration

All transition durations are exposed.

Settings:

```gdscript
default_fade_duration_out
default_fade_duration_in
default_fade_around_duration
```

Configured through:

```text
Inspector
```

Example:

```text
Fade Out
1.0 seconds

Fade In
1.0 seconds

Around Transition
0.5 seconds
```

This allows designers to adjust pacing without modifying code.

---

# 🌑 Fade Out

Function:

```gdscript
fade_out()
```

Purpose:

Hide gameplay before an operation occurs.

Flow:

```text id="l6cx5w"
Visible Gameplay

        ↓

Fade Overlay Alpha 0 → 1

        ↓

Screen Fully Hidden
```

Common uses:

* loading worlds
* opening cinematic scenes
* game over screens
* returning to menus

Example:

```gdscript
await TransitionManager.fade_out()
```

---

# 🌞 Fade In

Function:

```gdscript
fade_in()
```

Purpose:

Reveal gameplay after a transition completes.

Flow:

```text id="7n1q34"
Screen Hidden

        ↓

Fade Overlay Alpha 1 → 0

        ↓

Gameplay Visible
```

Common uses:

* after loading scenes
* after restoring saves
* after teleporting players

---

# 🔁 Fade Around Pipeline

The Transition Manager provides:

```gdscript
fade_around(callback)
```

for complete transition sequences.

Pattern:

```text id="l1xqms"
Fade Out

    ↓

Execute Action

    ↓

Fade In
```

Example:

```gdscript
await TransitionManager.fade_around(
    Callable(self, "load_world")
)
```

---

# 🗺️ Scene Transition Integration

Used alongside:

```gdscript
GameManager
```

Example level change:

```text id="4myt8a"
Change Level Request

        ↓

GameManager

        ↓

fade_out()

        ↓

Load World

        ↓

Restore Player

        ↓

fade_in()
```

The Transition Manager never loads scenes itself.

---

# 💾 Save / Load Integration

Save loading uses transitions to hide restoration.

Flow:

```text id="s7s9uo"
Load Save

     ↓

Fade Out

     ↓

Restore SaveData

     ↓

Load World

     ↓

Restore Player

     ↓

Fade In
```

This prevents visible intermediate states.

---

# 🎮 Gameplay Integration

Any system can request transitions.

Examples:

```text id="29u2n4"
Quest Completion
        |
        ↓
Cinematic

Teleport
        |
        ↓
Area Change

Death
        |
        ↓
Game Over

Loading
        |
        ↓
World Swap
```

---

# 🔗 System Relationships

Final architecture:

```text id="jd0m2y"
                 TransitionManager

                         |
                         ↓

                  Fade Overlay

                         |
        +----------------+----------------+
        |                |                |
        ↓                ↓                ↓

  GameManager      SaveManager       UI Systems


        |
        ↓

 Scene / Gameplay Changes
```

---

# Framework Role

The Transition Manager provides:

✅ centralized transition effects
✅ scene-independent screen fading
✅ configurable fade timing
✅ reusable transition pipeline
✅ clean separation between logic and presentation
✅ support for loading, saving, teleporting, and cinematic flows

The Transition Manager ensures every major game state change can happen cleanly and visually without individual systems needing to implement their own fade logic.

Gameplay systems request transitions.

The Transition Manager handles the presentation.
