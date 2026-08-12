# 🎬 Credits Root

## Overview

`credits_root.gd` is the **root controller for the Credits UI**. It loads structured credits data and dynamically builds the complete scrollable credits presentation.

It controls the **presentation layer**, including:

* Department contributor sections
* Names and roles
* Music credits
* Legal / attribution text
* Special Thanks
* Dedications
* Typography, colors, alignment, and spacing

The actual credit content comes from the configured Credits Data source.

---

## Architecture

```text
Credits Data
     │
     │ get_credits_data()
     ▼
credits_root.gd
     │
     ├── Departments
     │     └── credits_department.tscn
     │
     ├── Music
     ├── Legal / Attributions
     ├── Special Thanks
     └── Dedication
             │
             ▼
      Credits VBoxContainer
             │
             ▼
       Scrollable UI
```

`credits_root.gd` is the **assembly layer** between the Credits data and the Credits scene.

---

## Responsibilities

### Data Loading

The root loads the configured Credits Data script:

```gdscript
@export var credits_data_path
```

At runtime it calls:

```gdscript
get_credits_data()
```

and passes the resulting data into `render_credits()`.

The contributor information is therefore not hardcoded into the UI.

---

### Dynamic UI Construction

`render_credits(data)` completely rebuilds the Credits container.

It first clears previously generated controls, then constructs the presentation from the supplied data.

The rendering order is:

```text
Top Spacer
    ↓
Departments
    ↓
Music
    ↓
Legal / Attributions
    ↓
Special Thanks
    ↓
Dedication
    ↓
Bottom Spacer
```

Sections that contain no data are skipped.

---

## Department Rendering

Departments are created from the Credits Data.

Each populated department uses the reusable:

```text
credits_department.tscn
```

The root assigns the department title and dynamically creates contributor rows.

```text
Department
├── Header
├── Contributor
│   ├── Name
│   └── Role
├── Contributor
│   ├── Name
│   └── Role
└── Spacer
```

Contributor names and roles receive independent typography, colors, alignment, and sizing settings.

---

## Music Credits

Music is rendered as its own section.

Each track can contain:

* Title
* Composer
* Performer
* Context

```text
Music
├── Track Title
├── Composed by
├── Performed by
└── Context
```

Composer, performer, and context are optional, allowing individual tracks to contain only the attribution information available for them.

---

## Legal / Attribution

Legal entries are rendered as individual labels beneath a dedicated section header.

```text
Legal / Attributions
├── Attribution
├── Attribution
└── Attribution
```

The root provides independent color, alignment, and spacing configuration for these entries.

---

## Special Thanks

Special Thanks uses a separate presentation style from normal departments.

Each entry is dynamically created as a centered name/role row:

```text
Special Thanks
├── Name    Role
├── Name    Role
└── Name    Role
```

Dedicated colors and alignment settings allow this section to have its own visual treatment.

---

## Dedication

Dedications are rendered separately from Special Thanks.

```text
Dedicated To
├── Name    Role
├── Name
└── Name    Role
```

The role is only created when one is provided, preventing empty role fields from producing unnecessary controls.

---

## Presentation Configuration

The root exposes the Credits presentation through editor properties.

### Typography

* Base font
* Name font size and outline
* Title font size and outline
* Section title size and outline

### Colors

* Contributor names
* Contributor roles
* Section titles
* Music elements
* Legal text
* Special Thanks
* Dedications

### Alignment

Independent alignment controls exist for:

* Names
* Roles
* Section headers
* Music titles and credits
* Legal entries
* Special Thanks
* Dedications

### Spacing

Section and entry spacing is configurable through exported spacer heights rather than being fixed in the scene.

---

## Editor Support

The script uses:

```gdscript
@tool
```

so the Credits UI can be rebuilt inside the Godot editor.

The editor periodically reloads the Credits Data and calls `render_credits()`, allowing changes to the data and presentation configuration to be previewed without running the game.

At runtime, the Credits are loaded and rendered during `_ready()`.

---

## Scene Structure

The root expects the Credits scene to provide the scrollable rendering target:

```text
CreditsRoot
└── NinePatchRect
    └── ScrollContainer
        └── VBoxContainer
```

`credits_root.gd` owns the contents of the `VBoxContainer`; the surrounding scene owns the background and scroll layout.

---

## Architectural Boundary

| Component                   | Responsibility                                         |
| --------------------------- | ------------------------------------------------------ |
| **Credits Data**            | Defines what contributors and attributions exist       |
| **credits_root.gd**         | Builds and styles the Credits presentation             |
| **credits_department.tscn** | Provides reusable department structure                 |
| **Credits Scene**           | Provides the surrounding UI, background, and scrolling |

---

## Key Design

`credits_root.gd` is a **data-driven Credits presentation assembler**.

The system separates:

```text
Credits Data
    ↓
Content
```

from:

```text
Credits Root
    ↓
Presentation
```

This allows Credits content to change independently from the UI while keeping the entire Credits screen dynamically generated and consistently styled.
