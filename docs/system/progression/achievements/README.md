# 🏆 Achievement System

The Achievement System provides the player-facing interface for browsing and viewing achievements.

Achievements are implemented through the Quest System using:

```text
QuestDefinition.QuestCategory.ACHIEVEMENT
```

The system provides:

* Achievement browsing
* Achievement filtering
* Achievement sorting
* Achievement card generation
* Achievement progress display
* Achievement details
* Achievement reward display

Achievement progression and runtime state remain owned by the Quest System.

---

# 🧠 Achievement Architecture

```text
                    Quest System

                         |
                  Achievement Data
                         |
                         ↓
                Achievement Menu
                         |
                  ┌──────┴──────┐
                  ↓             ↓
          Achievement Card   Details
```

The Achievement Menu presents achievement data provided by the Quest System.

The Achievement Card provides the individual achievement representation within the menu.

---

# 🔗 Achievement System Documentation

| SystemPurposeDocumentation |                                                       |                  |
| -------------------------- | ----------------------------------------------------- | ---------------- |
| 🖥️ Achievement Menu       | Achievement browsing, filtering, sorting, and display | [Achievement Menu](achievement_menu.md) |
| 🃏 Achievement Card        | Individual achievement display and progress           | [Achievement Card](achievement_card.md) |

---

# 🔗 System Integration

The Achievement System integrates with:

* 📜 Quest System
* 🧠 QuestManager
* 📋 QuestInstance
* 🎁 Item Database
* 🖥️ UI Manager

The Achievement System consumes Quest System data without owning achievement progression or completion logic.

---

# 📌 Design Rule

**The Quest System defines and manages achievements.**

**The Achievement Menu displays them.**

**The Achievement Card represents them.**

Achievement functionality should remain data-driven through the existing Quest System rather than creating a separate achievement progression system.
