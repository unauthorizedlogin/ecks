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

## 📄 Localization Data Architecture

All localized strings originate from a master CSV translation database.

Example structure:

```
translations.csv

key,comment,en,es,id
main_menu,Main menu title,Main Menu,Menú Principal,Menu Utama
```

Each entry contains:

* localization key
* translator notes/comments
* language-specific translations

The CSV acts as the source of truth for all supported languages.

---

## 🔤 Translation Key System

All player-visible text is designed around explicit localization keys.

Supported content includes:

* UI labels
* buttons
* menus
* item names
* item descriptions
* quests
* objectives
* dialogue
* system messages
* gameplay text

Every visible string is prepared for localization rather than relying on runtime text replacement.

The project does not depend on automatic translation.

---

## 📦 PO File Generation

The generator converts CSV translation data into standard `.po` files compatible with Godot's localization system.

Generated files include:

```
localization/
├── strings_en.po
├── strings_es.po
├── strings_id.po
├── strings_fr.po
└── ...
```

Each language receives its own translation file.

The generated PO files include:

* language metadata
* UTF-8 encoding
* translation entries
* translator comments
* revision timestamps

---

## 🌍 Multilingual Support

The localization pipeline is designed to scale across many languages.

The system supports:

* unlimited language columns
* independent translation files
* large translation databases
* incremental updates

Adding a new language requires only adding a new language column to the CSV source.

The generator automatically creates the corresponding `.po` file.

---

## 📝 Translation Preservation

The generator safely updates existing PO files.

When regenerating translations:

* existing translation data is loaded
* unchanged translations are preserved
* updated strings are refreshed
* new strings are added automatically

This allows the translation database to evolve without destroying previous localization work.

---

## ⚙️ Generation Pipeline

The generation process:

1. Reads the master localization CSV
2. Detects available language columns
3. Loads existing PO translation data
4. Processes translation entries
5. Updates changed strings
6. Generates language-specific `.po` files
7. Outputs updated translation resources

---

## 🛠️ Editor Integration

The Localization Generator runs directly inside the Godot editor.

Features include:

* automatic generation on load
* manual generation button
* editor-side translation updates

This allows localization files to be rebuilt without leaving the development environment.

---

## 📚 Localization Workflow

The recommended workflow:

1. Add new localization keys to the CSV
2. Provide translations for supported languages
3. Run the CSV → PO generator
4. Godot imports updated translation files
5. Runtime systems consume localized strings through `tr()`

All runtime systems reference localization keys rather than hardcoded player-facing text.

---

## ✅ System Responsibilities

The Localization Generator System:

✅ Converts CSV translation data into PO files
✅ Supports multiple languages
✅ Generates Godot-compatible translation files
✅ Maintains translation comments
✅ Preserves existing translations
✅ Centralizes localization management

The Localization Generator System does **not**:

❌ Translate text automatically
❌ Modify gameplay systems
❌ Replace runtime localization calls
❌ Handle dialogue logic
❌ Manage UI presentation

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
