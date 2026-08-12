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
| 🖥️ Achievement Menu       | Achievement browsing, filtering, sorting, and display | Achievement Menu |
| 🃏 Achievement Card        | Individual achievement display and progress           | Achievement Card |

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




---

# 🎯 Achievement Details Panel

Selecting an achievement displays:

## Achievement Information

Displays:

* Title
* Description
* Icon

Source:

```
QuestDefinition
```

---

# Objectives Display

## Locked Achievement

Displays objective descriptions only.

Example:

```
Defeat 100 Monsters
Collect 50 Gems
```

---

## Active Achievement

Displays objective progress.

Example:

```
Defeat Monsters 45/100
Collect Gems 23/50
```

---

## Completed Objectives

Displays:

```
✔ Objective Complete
```

---

# Rewards Display

Achievement rewards are displayed from the existing quest reward definitions.

The menu resolves reward display names through:

```
ItemDatabase
```

Displayed information:

* Reward name
* Reward quantity

---

# 🔄 Refresh Pipeline

The menu updates through QuestManager signals.

Connected signals:

```
quest_started
quest_updated
quest_completed
quest_failed
```

Flow:

```
QuestManager Signal
        |
        v
Achievement Menu Refresh
        |
        +--> Rebuild Cards
        |
        +--> Update Selected Achievement
```

---

# 🖥 Menu Controls

## Open

Opening the menu:

* Shows UI
* Notifies UIManager
* Populates achievements
* Restores selected achievement

---

## Close

Closing the menu:

* Hides UI
* Preserves selection state

---

## Toggle

Controlled through:

```
achievement_menu
```

---

# System Dependencies

| System        | Responsibility                                   |
| ------------- | ------------------------------------------------ |
| Quest System  | Achievement definitions, progression, completion |
| QuestManager  | Achievement runtime state                        |
| QuestInstance | Achievement objective progress                   |
| ItemDatabase  | Reward display names                             |
| UIManager     | Menu state handling                              |

---

# Current Features

✅ Achievement category filtering
✅ Achievement card generation
✅ Achievement selection
✅ Achievement progress display
✅ Achievement completion display
✅ Achievement objective rendering
✅ Achievement reward display
✅ Achievement chain visibility
✅ Achievement sorting
✅ Signal-driven updates
⬜ Hidden achievement completion reveal filtering
⬜ LIFE_TIME objective integration
