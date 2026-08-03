# 🌐 Localization (.po) Generator System

The Localization Generator System provides a data-driven translation pipeline for managing all player-facing text across the project.

Localization strings are authored through CSV data and automatically converted into standard `.po` (Portable Object) translation files, allowing the entire game interface, content, and gameplay text to be maintained through a centralized localization database.

Unlike traditional automatic translation workflows, the system is built around explicit translation keys and pre-authored language data.

The system is responsible for:

* CSV → PO translation generation
* multilingual string management
* translation key organization
* localization comments
* PO header generation
* translation file updates
* preserving existing translation data

See:

* UI Systems
* Dialogue System
* Quest System
* Item Database System

---

# 🛠️ CSV → PO Generator — Localization Authoring Pipeline

The CSV Generator is the developer-facing localization tool.

Its responsibility:

Convert:

```text
Master CSV
```

into:

```text
Godot .po Translation Files
```

---

# 📄 Master Translation CSV

All localization data originates from one source file:

```text
translations.csv
```

Structure:

```csv
key,comment,en,es,id
main_menu,Main Menu Title,Main Menu,Menú Principal,Menu Utama
```

Columns:

| Column          | Purpose             |
| --------------- | ------------------- |
| key             | Internal identifier |
| comment         | Translator context  |
| en              | English source      |
| Other languages | Translated values   |

---

# 🔄 Translation Generation Flow

```text
translations.csv

      |
      ↓

CSV Parser

      |
      ↓

Language Detection

      |
      ↓

PO Generation

      |
      ↓

strings_<locale>.po

      |
      ↓

Godot Import

      |
      ↓

.translation Resource
```

---

# 🌐 Automatic Language Detection

The generator automatically detects languages from CSV headers.

Example:

```csv
key,comment,en,fr,de,ja
```

Creates:

```text
strings_en.po
strings_fr.po
strings_de.po
strings_ja.po
```

No manual language configuration is required.

---

# 📝 Translation Preservation

The generator preserves existing PO data.

Before overwriting:

```text
Existing PO
      |
      ↓
Read Existing Entries
      |
      ↓
Compare CSV Changes
      |
      ↓
Update Changed Strings Only
```

This prevents:

* lost translations
* unnecessary overwrites
* translator progress loss

---

# 💬 Translator Comments

CSV comments become PO translator notes.

CSV:

```csv
key,comment,en
exit_button,Button text,Exit
```

Generated:

```po
#. Button text
msgid "Exit"
msgstr "Exit"
```

Provides context for translators.

---

# 🖥️ Editor Integration

The generator runs inside the Godot editor.

Provides:

```text
Generate .po Files
```

button.

Workflow:

```text
Open Editor
      |
      ↓
Modify CSV
      |
      ↓
Press Button
      |
      ↓
Regenerate Translation Files
```

---

# 📦 Generated File Structure

Recommended structure:

```text
localization
 |
 ├── translations.csv
 |
 ├── strings_en.po
 ├── strings_es.po
 ├── strings_fr.po
 ├── strings_ja.po
 |
 └── strings_<locale>.translation
```

---

# 🔗 System Relationships

Final architecture:

```text
                 Localization System

                        |
        +---------------+---------------+
        |                               |
        ↓                               ↓

 LanguageManager              CSV → PO Generator

 Runtime                       Development

        |                               |
        ↓                               ↓

TranslationServer          Translation Files

        |
        ↓

      UI / Gameplay Systems

        |
        ↓

      tr("KEY")
```

---

## ✅ System Responsibilities

The Localization Generator System:

- ✅ Converts CSV translation data into PO files
- ✅ Supports multiple languages
- ✅ Generates Godot-compatible translation files
- ✅ Maintains translation comments
- ✅ Preserves existing translations
- ✅ Centralizes localization management

The Localization Generator System does **not**:

- ❌ Translate text automatically
- ❌ Modify gameplay systems
- ❌ Replace runtime localization calls
- ❌ Handle dialogue logic
- ❌ Manage UI presentation

Those responsibilities belong to:

* Dialogue System
* UI Systems
* Quest System
* Runtime Localization Manager

---

## 🚀 Production Localization Pipeline

The result is a scalable localization workflow where all player-facing content can be authored, translated, and maintained from a single data source.

Localization remains:

* deterministic
* export-safe
* data-driven
* easy to expand
* independent from gameplay code

This allows the project to support large-scale multilingual releases without requiring manual resource editing.
