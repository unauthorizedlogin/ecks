# 🌐 Localization System — Data-Driven Multilingual Translation Framework

The Localization System provides the global language infrastructure for the Ecks Framework.

It is divided into two independent layers:

1. **Language Manager**

   * Runtime localization authority
   * Applies player language preferences
   * Loads translations dynamically
   * Controls active locale

2. **CSV → PO Generator**

   * Localization authoring pipeline
   * Converts master translation tables into Godot-compatible translation files
   * Maintains translation data across supported languages

Together they provide a complete localization workflow:

```text
Developer Content
        |
        ↓
Master Translation CSV
        |
        ↓
CSV → PO Generator
        |
        ↓
Godot Translation Files
        |
        ↓
Language Manager
        |
        ↓
Runtime Localization
```

---

# 🧠 Language Manager — Runtime Localization Authority

`LanguageManager` is the global runtime controller for localization.

It is responsible for:

* Active language selection
* Locale switching
* Loading translation resources
* Applying saved language preferences
* Providing available languages to UI systems

It operates as an Autoload Singleton.

---

# 🌍 Supported Languages

The framework uses locale identifiers as the source of truth.

Current supported languages:

```text
en      English
ar      Arabic
da      Danish
de      German
es      Spanish
fi      Finnish
fr      French
he      Hebrew
hi      Hindi
id      Indonesian
it      Italian
ja      Japanese
ko      Korean
nl      Dutch
nb      Norwegian
pl      Polish
pt      Portuguese (Brazil)
ru      Russian
sv      Swedish
tr      Turkish
vi      Vietnamese
zh      Chinese Simplified
zh_TW   Chinese Traditional
```

Total:

```text
23 Supported Languages
```

---

# 🔑 Translation Key Authority

The localization system uses the English string as the translation key.

Example:

```gdscript
tr("New Game")
```

Translation table:

```text
msgid "New Game"

en:
"New Game"

es:
"Nuevo Juego"

fr:
"Nouvelle Partie"

de:
"Neues Spiel"
```

Architecture:

```text
English Key
      |
      ↓
Translation Lookup
      |
      ↓
Current Locale
      |
      ↓
Translated String
```

This keeps localization deterministic and avoids dependency on automatic translation systems.

---

# 📖 Runtime Translation Loading

Translation files follow:

```text
strings_<locale>.translation
```

Example:

```text
strings_en.translation
strings_es.translation
strings_ja.translation
strings_zh_TW.translation
```

Loaded dynamically:

```gdscript
TranslationServer.add_translation()
```

---

# 🚀 Localization Boot Flow

Localization is applied during startup.

Pipeline:

```text
Game Boot
    |
    ↓
Load Settings.cfg
    |
    ↓
Read Saved Locale
    |
    ↓
Set TranslationServer Locale
    |
    ↓
Load Translation Resource
    |
    ↓
Register Translation
    |
    ↓
Game Starts
```

Example:

```text
Player Setting:

locale = "fr"

        ↓

LanguageManager

        ↓

strings_fr.translation

        ↓

French UI
```

---

# 💾 Player Language Persistence

The selected language is stored through:

```text
user://settings.cfg
```

Example:

```ini
[language]

locale="ja"
```

If no preference exists:

```gdscript
return "en"
```

English is always the fallback language.

---

# 🖥️ UI Integration

LanguageManager exposes:

```gdscript
available_languages
```

for UI selection menus.

Example:

```gdscript
for code in available_languages:
    print(code)
```

Provides:

```text
Internal Code
      |
      ↓
Display Name

fr
French (Français)

ja
Japanese (日本語)

zh_TW
Chinese Traditional (繁體中文)
```

This allows language menus to remain fully data-driven.

---

# Framework Role

The Localization System removes hard-coded language dependencies from the entire framework.

Every player-facing string can be routed through:

```gdscript
tr("English Key")
```

allowing:

* menus
* quests
* dialogue
* items
* abilities
* UI
* notifications
* gameplay text

to share one centralized translation pipeline.

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

The result is a scalable localization foundation capable of supporting large RPG projects with thousands of strings across multiple languages without manual file management.
