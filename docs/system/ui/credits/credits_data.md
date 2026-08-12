# 🎬 Credits Data

## Overview

The Credits Data Generator converts the Credits CSV into a generated Godot data script used by the Credits UI.

It separates **editable Credits content** from the runtime presentation system:

```text
credits_data.csv
      │
      ▼
credits_data_generator.gd
      │
      ▼
credits_data.gd
      │
      ▼
credits_root.gd
      │
      ▼
Credits UI
```

The CSV is the authoring source. The generated `.gd` file is the runtime data source.

---

## Responsibilities

The generator handles:

* Reading the Credits CSV.
* Parsing each row by section.
* Grouping department contributors.
* Preserving department order.
* Collecting music credits.
* Collecting legal attributions.
* Collecting Special Thanks.
* Collecting dedications.
* Removing duplicate department members.
* Generating the final `credits_data.gd` script.

It does **not** control Credits UI presentation.

---

## CSV Input

The generator reads:

```text
res://data/generators/credits/credits_data.csv
```

Each row is converted into a normalized dictionary containing:

```text
section
department
name
role
title
composer
performer
context
text
```

The `section` field determines how the row is processed.

Supported sections:

```text
department
music
legal
special
dedication
```

---

## Section Processing

### Departments

Department rows are grouped by department name.

```text
Department
├── Member
│   ├── Name
│   └── Role
├── Member
└── Member
```

Departments are stored in a dictionary while `department_order` preserves their original CSV ordering.

Empty departments and completely empty contributor rows are ignored.

Duplicate `name + role` combinations within a department are removed during generation.

---

### Music

Music rows are collected into the generated `music` array.

Supported fields:

```text
title
composer
performer
context
```

The generator preserves these as track-level data for `credits_root.gd` to render.

---

### Legal

Legal rows are converted into the generated `legal` array using their `text` field.

```text
legal
├── Attribution
├── Attribution
└── Attribution
```

---

### Special Thanks

Special Thanks rows become contributor dictionaries:

```text
{
    "name": "...",
    "role": "..."
}
```

---

### Dedication

Dedication rows use the same name/role structure but are placed into the separate `dedication` collection.

---

## Generated Output

The generator writes:

```text
res://data/templates/ui/menus/credits/credits_data.gd
```

The generated script exposes:

```gdscript
func get_credits_data() -> Dictionary:
```

and returns the complete Credits dataset:

```text
{
    title,
    departments,
    music,
    legal,
    special,
    dedication
}
```

This is the data structure consumed by `credits_root.gd`.

---

## Runtime Data Flow

```text
CSV Authoring
     │
     ▼
Generator
     │
     ├── Departments
     ├── Music
     ├── Legal
     ├── Special
     └── Dedication
     │
     ▼
Generated credits_data.gd
     │
     │ get_credits_data()
     ▼
credits_root.gd
     │
     ▼
Dynamic Credits UI
```

The generator therefore acts as the **content compilation layer** between the editable CSV and the runtime Credits system.

---

## Editor Integration

The generator is marked:

```gdscript
@tool
```

and automatically runs `generate()` when loaded in the Godot editor.

This keeps the generated Credits data synchronized with the CSV during development.

---

## Architectural Boundary

| Component                   | Responsibility                                           |
| --------------------------- | -------------------------------------------------------- |
| `credits_data.csv`          | Authoring source for Credits content                     |
| `credits_data_generator.gd` | Parses, organizes, validates, and generates Credits data |
| `credits_data.gd`           | Generated runtime data provider                          |
| `credits_root.gd`           | Converts Credits data into UI                            |

### Key Principle

**CSV is the source of truth; `credits_data.gd` is the generated runtime representation.**

This keeps Credits content easy to maintain while allowing the runtime UI to consume a clean, structured data format.
