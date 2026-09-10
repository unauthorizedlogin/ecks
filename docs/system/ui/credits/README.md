# 🎬 Credits System

The Credits System provides a data-driven framework for displaying contributor acknowledgements, music credits, legal attributions, special thanks, and dedications.

Credits content is separated from presentation, allowing contributor and attribution data to be updated independently from the credits UI.

The system provides:

* Contributor and department organization
* Dynamic credits generation
* Music attribution and playback
* Legal attribution
* Special thanks
* Dedications
* Scrollable credits presentation
* Text entrance, hold, and exit animations
* Background selection and switching
* Localization support
* Editor preview support
* Inspector-driven visual configuration

---

# 🧠 Credits Architecture

```text
                    Credits System

                          |
                    Credits Data
                          |
                          ↓
                   Credits Root
                          |
                          ↓
                  Credits Renderer
                          |
              ┌───────────┼───────────┐
              ↓           ↓           ↓
       Credits Department  Scroll   Text Animators
                                      |
                              ┌───────┼───────┐
                              ↓       ↓       ↓
                           Entrance  Hold    Exit

                 Credits Background
                         |
                 Background Control

                    Credits Audio
                         |
                   Music Playlist
```

`CreditsData` defines the credits content.

`CreditsRoot` provides presentation configuration and passes control to the dedicated credits components.

`CreditsRenderer` builds the dynamic credits presentation.

`CreditsBackground`, `CreditsAudio`, and the text animators provide dedicated presentation behavior.

---

# 🔗 Credits System Documentation

| System                   | Purpose                                               | Documentation                                     |
| ------------------------ | ----------------------------------------------------- | ------------------------------------------------- |
| 🎬 Credits Root          | Main credits scene and presentation configuration     | [Credits Root](credits_root.md)                   |
| 🖥️ Credits Renderer     | Builds and manages the dynamic credits presentation   | [Credits Renderer](credits_renderer.md)           |
| 📄 Credits Data          | Defines contributor, attribution, and credits content | [Credits Data](credits_data.md)                   |
| 👥 Credits Department    | Displays contributor departments and entries          | [Credits Department](credits_dept.md)             |
| 📜 Credits Scroll        | Provides the scrollable credits presentation          | [Credits Scroll](credits_scroll.md)               |
| ✨ Credits Text Animator  | Controls text entrance animations                     | [Credits Text Animator](credits_text_animator.md) |
| ⏸️ Credits Hold Animator | Controls text hold animations                         | [Credits Hold Animator](credits_hold_animator.md) |
| 🚪 Credits Exit Animator | Controls text exit animations                         | [Credits Exit Animator](credits_exit_animator.md) |
| 🖼️ Credits Background   | Selects and switches credits backgrounds              | [Credits Background](credits_background.md)       |
| 🎵 Credits Audio         | Manages automatic credits music playback              | [Credits Audio](credits_audio.md)                 |

---

# 🔗 System Integration

The Credits System integrates with:

* 🔊 Audio System
* 🌍 Localization System
* 🖥️ UI System
* 📄 Data Resources

The Credits System owns credits presentation while credits content remains data-driven through the Credits Data resource.

---

# 📌 Design Rule

**`CreditsData` defines the credits.**

**`CreditsRoot` provides configuration and delegates presentation.**

**`CreditsRenderer` builds the credits presentation.**

**Dedicated animators, background, scroll, and audio components handle their respective presentation responsibilities.**

The Credits System should keep credits content, rendering, animation, layout, background, and audio responsibilities separated so the credits sequence can be updated without embedding contributor information directly into the scene.
