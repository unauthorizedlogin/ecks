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
              ┌───────────┴───────────┐
              ↓                       ↓
      Dynamic Departments        Credits Audio
              |                       |
              ↓                       ↓
       Credits Scroll            Music Playlist
```

`CreditsData` defines the credits content.

`CreditsRoot` controls the credits scene and generates the presentation.

`CreditsDepartment` and `CreditsScroll` provide the visual structure for the generated credits.

`CreditsAudio` provides dedicated credits music playback.

---

# 🔗 Credits System Documentation

| SystemPurposeDocumentation |                                                        |                    |
| -------------------------- | ------------------------------------------------------ | ------------------ |
| 🎬 Credits Menu            | Main credits scene and dynamic presentation controller | [Credits Root](credits_root.md)       |
| 📄 Credits Data            | Defines contributor, attribution, and credits content  | [Credits Data](credits_data.md)        |
| 👥 Credits Department      | Displays contributor departments and entries           | [Credits Department](credits_dept.md)  |
| 🎵 Credits Audio           | Manages automatic credits music playback               | [Credits Audio](credits_audio.md)       |
| 📜 Credits Scroll          | Provides the scrollable credits presentation           | [Credits Scroll](credits_scroll.md)      |

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

**`CreditsRoot` builds the presentation.**

**`CreditsDepartment` and `CreditsScroll` structure the UI.**

**`CreditsAudio` controls the credits soundtrack.**

The Credits System should keep credits content, presentation, layout, and audio responsibilities separated so the credits sequence can be updated without embedding contributor information directly into the scene.
