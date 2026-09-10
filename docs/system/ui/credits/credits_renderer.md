# 🎬 Credits Renderer Architecture

### Purpose

`CreditsRenderer` is the content-generation layer of the Credits system. It converts the structured Credits data into the runtime UI hierarchy, applies the presentation configuration supplied by `CreditsRoot`, and registers generated labels with the Credits animation pipeline.

### Architecture Role

```text
Credits Data
     ↓
CreditsRoot
     ↓
CreditsRenderer
     ├── Departments
     ├── Music
     ├── Legal
     ├── Attributions
     ├── Special Thanks
     └── Dedication
            ↓
      CreditsTextAnimator
```

`CreditsRenderer` owns **what UI elements are created and how Credits data is organized**, while `CreditsTextAnimator` owns how individual labels animate.

### Responsibilities

* Clear previously generated Credits content.
* Reset the text animation sequence before rendering.
* Create shared `LabelSettings` configurations.
* Build the complete Credits content hierarchy.
* Generate spacing between major Credits sections.
* Render department/member entries.
* Render music and composer/performer information.
* Render legal text.
* Render attribution entries.
* Render Special Thanks entries.
* Render dedication entries.
* Apply configured colors, fonts, alignment, outlines, and spacing.
* Pass generated labels to `CreditsTextAnimator`.

### Render Pipeline

Each render begins by resetting animation state and clearing the existing container.

```text
Reset Animator
      ↓
Clear Container
      ↓
Create Label Settings
      ↓
Render Sections
      ↓
Animate Generated Labels
```

Sections are rendered in a fixed presentation order:

1. Departments
2. Music
3. Legal / Attributions
4. Attributions
5. Special Thanks
6. Dedication

Configured spacers are inserted between major sections and at the beginning/end of the Credits content.

### Department Rendering

Departments are rendered from the supplied department data.

Empty departments are skipped.

Each populated department creates a dedicated `credits_department.tscn` instance containing:

* Department title
* Header spacing
* Department members
* Department spacing

Individual members use separate name and role labels with independently configurable animation targets.

### Content Types

The renderer supports multiple Credits data structures:

| Content        | Runtime Representation              |
| -------------- | ----------------------------------- |
| Departments    | Department scene + member rows      |
| Music          | Track containers + credit labels    |
| Legal          | Individual text labels              |
| Attribution    | Name, title, and description labels |
| Special Thanks | Centered name/title entries         |
| Dedication     | Centered name/title entries         |

Optional data sections are skipped when their corresponding arrays are missing or empty.

### Presentation Configuration

`CreditsRoot` supplies the renderer with centralized presentation settings including:

* Fonts
* Font sizes
* Outline sizes/colors
* Text colors
* Alignment
* Size flags
* Section spacing
* Section-specific spacing
* Animation configuration

`CreditsRenderer` translates these settings into generated controls and shared `LabelSettings` resources.

### Animation Integration

Generated labels are passed to:

```text
CreditsTextAnimator.animate()
```

The renderer does not implement animation behavior itself.

Animation timing therefore remains centralized while the renderer determines **which labels exist and where they appear in the Credits hierarchy**.

### Translation

Static section labels and music prefixes are passed through Godot's translation system using `tr()`, allowing generated Credits UI text to participate in localization.

### File(s) Affected

* `credits_renderer.gd`
* `credits_root.gd` — presentation configuration/data source
* `credits_text_animator.gd` — label animation dependency
* `credits_department.tscn` — department presentation
* Credits data provider configured by `credits_data_path`

### Notes

`CreditsRenderer` is the **content construction layer** of the Credits architecture. It deliberately separates data-driven UI generation from animation, background presentation, and screen lifecycle management.

The renderer therefore sits between the Credits data source and the generated UI, while `CreditsTextAnimator` remains responsible for animation behavior.
