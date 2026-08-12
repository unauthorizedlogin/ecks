# 🎬 Credits Department — Architecture

## Overview

`credits_department.gd` is the **reusable department layout component** used by the Credits Root.

It controls the visual structure of an individual Credits department, including its department title and the grid used to arrange contributor entries.

The component does not manage Credits data or decide which contributors belong to a department. It provides the **layout container** that `credits_root.gd` populates.

---

## Architecture Role

```text
credits_root.gd
      │
      │ creates department
      ▼
CreditsDepartment
      │
      ├── Department Title
      │
      └── Members Grid
             │
             ├── Name / Role
             ├── Name / Role
             └── ...
```

The root determines **what gets added**.
`credits_department.gd` determines **where those entries are placed**.

---

## Scene Structure

```text
CreditsDepartment
├── TitleLabel
├── CenterContainer
│   └── MembersGrid
├── HBoxContainer
│   ├── NameLabel
│   └── RoleLabel
└── FullRow
    └── LeftPair
        ├── NameLabel1
        └── RoleLabel1
        RightPair
        ├── NameLabel2
        └── RoleLabel2
```

The department scene provides the reusable layout structure for contributor rows and grid-based presentation.

`MembersGrid` is the primary insertion point used by the script.

---

## Responsibilities

### Department Title

`set_title()` assigns the department's display title:

```gdscript
func set_title(title: String):
    if title_label:
        title_label.text = title
```

The title itself comes from the Credits data; this component only displays it.

### Member Layout

`add_member()` inserts a generated contributor Control into:

```text
CenterContainer/MembersGrid
```

```gdscript
func add_member(entry: Control):
    members_grid.add_child(entry)
```

This allows `credits_root.gd` to construct contributor rows dynamically while leaving the actual grid layout to the department scene.

---

## Layout Responsibility

The department component is intentionally focused on **presentation structure**.

It controls:

* Department title placement.
* Contributor grid placement.
* Name/role column arrangement.
* Full-row layout options.
* Grid/container configuration.
* Visual spacing and alignment provided by the scene.

The root does not need to know the internal layout mechanics of the department.

---

## Current Layout Model

The department currently supports the primary contributor presentation through the members grid:

```text
Department Title
        │
        ▼
┌─────────────────────────┐
│ Name        │ Role      │
│ Name        │ Role      │
│ Name        │ Role      │
└─────────────────────────┘
```

The grid provides the consistent alignment needed for department contributor lists.

---

## Future Layout Expansion

A planned enhancement is adding a dedicated **center spacer/pipe** between the name and role columns.

Conceptually:

```text
Name        │        Role
Name        │        Role
Name        │        Role
```

This would provide additional formatting control without changing the overall department architecture.

The existing department component is already the appropriate location for that enhancement because it owns the contributor layout rather than the Credits data or root rendering logic.

---

## Architectural Boundary

| Component                   | Responsibility                        |
| --------------------------- | ------------------------------------- |
| `credits_data.csv`          | Credits content                       |
| `credits_data_generator.gd` | Generates structured Credits data     |
| `credits_root.gd`           | Builds and populates Credits sections |
| `credits_department.gd`     | Controls department layout            |
| `CreditsDepartment.tscn`    | Defines department UI structure       |

### Key Principle

**`credits_department.gd` is the reusable layout component for a single Credits department.**

It receives a title and contributor Controls from the Credits Root and handles their placement within the department's predefined layout.
