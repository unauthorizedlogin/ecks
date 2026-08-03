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

# Framework Localization Coverage

The localization framework provides:

- ✅ 23 supported languages
- ✅ English-key translation architecture
- ✅ Runtime locale switching
- ✅ Saved player language preference
- ✅ Dynamic translation loading
- ✅ CSV-driven translation management
- ✅ PO generation pipeline
- ✅ Translator comments
- ✅ Existing translation preservation
- ✅ Editor-integrated generation workflow

---
