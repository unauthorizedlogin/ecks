# ⚙️ Credits Data Generator — CSV → Runtime Credits Resource

The Credits Generator converts human-editable CSV credit data into a runtime-ready GDScript data resource.

This separates:

* credit authoring
* data organization
* runtime presentation

Architecture:

```text
Credits CSV
     ↓
Credits Generator
     ↓
credits_data.gd
     ↓
Credits Root UI
     ↓
Rendered Credits
```

---

# 🎯 Core Responsibility

The Credits Generator manages:

* CSV credit authoring
* section categorization
* department grouping
* contributor formatting
* music metadata generation
* legal attribution generation
* special thanks generation
* dedication generation
* duplicate cleanup

The runtime credits UI never reads CSV files directly.

---

# 📄 Source Data

Credits are authored through:

```text
data/generators/credits/credits_data.csv
```

CSV structure supports:

```text
Section
Department
Name
Role
Title
Composer
Performer
Context
Text
```

This allows all credit content to remain editable without touching UI code.

---

# 🔄 Generation Pipeline

When running inside the editor:

```gdscript
@tool
```

the generator automatically processes the CSV.

Flow:

```text
Editor Load
      ↓
Credits Generator
      ↓
Read CSV
      ↓
Organize Sections
      ↓
Generate GDScript
      ↓
Credits UI Loads Data
```

---

# 🗂 Credit Section Processing

The generator separates entries by section type.

Supported sections:

```text
department
music
legal
special
dedication
```

---

# 👥 Department Generation

Department entries are grouped automatically.

Example CSV:

```text
Department,Programming,Developer,Role
Department,Art,Artist,Role
```

Generates:

```text
Departments
 |
 ├── Programming
 |       └── Developer
 |
 └── Art
         └── Artist
```

Each department stores:

```gdscript
{
    "title": department_name,
    "members": []
}
```

---

# 🧹 Duplicate Protection

The generator removes duplicate contributors within departments.

Duplicate detection uses:

```text
Name + Role
```

Example:

```text
John Doe | Programmer
John Doe | Programmer
```

becomes:

```text
John Doe | Programmer
```

This prevents accidental repeated credits from appearing in the final display.

---

# 🎵 Music Credit Generation

Music entries generate structured metadata:

```gdscript
{
    "title": "",
    "composer": "",
    "performer": "",
    "context": ""
}
```

Supports:

* track names
* composers
* performers
* usage context

---

# 📜 Legal Attribution Generation

Legal entries are converted into localized text entries.

Generated:

```gdscript
"legal": [
    tr("Attribution Text")
]
```

Used for:

* licenses
* third-party assets
* copyright notices

---

# 💖 Special Thanks Generation

Special thanks entries generate:

```gdscript
{
    "name": "",
    "role": ""
}
```

Allows separate acknowledgement formatting from normal contributors.

---

# 🙏 Dedication Generation

Dedication entries support:

```gdscript
{
    "name": "",
    "role": ""
}
```

Designed for final acknowledgements and personal dedications.

---

# 🌍 Localization Integration

The generator automatically wraps translatable fields.

Generated examples:

```gdscript
tr("Credits")
tr("Department Name")
tr("Role")
tr("Context")
```

This keeps generated credit data compatible with the game's localization pipeline.

---

# 📦 Generated Output

The generator produces:

```text
data/templates/ui/menus/credits/credits_data.gd
```

Runtime access:

```gdscript
get_credits_data()
```

Returns:

```gdscript
Dictionary
```

containing:

```text
departments
music
legal
special
dedication
```

---

# 🧱 Framework Role

The Credits Generator provides:

✅ spreadsheet-style credit authoring
✅ automatic runtime resource generation
✅ section organization
✅ contributor grouping
✅ duplicate cleanup
✅ localization-ready output
✅ separation between content and presentation

The generator creates a maintainable credit pipeline where contributors can be managed as data while the UI remains a reusable presentation system.
