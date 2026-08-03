# 🏆 Achievement System

## Overview

The Achievement Menu is a dedicated UI system for displaying and interacting with achievements.

Achievements are implemented through the Quest System using:

```
QuestDefinition.QuestCategory.ACHIEVEMENT
```

The Achievement Menu provides a specialized interface for achievement browsing.

It manages:

* Achievement filtering
* Achievement visibility rules
* Achievement sorting
* Achievement card generation
* Achievement progress display
* Achievement details rendering
* Achievement reward display

For achievement creation, definitions, objectives, and generation:

See:

[Quest System Documentation](./quest_system.md)

---

# 🧩 Architecture Role

The Achievement Menu is a presentation and discovery layer.

It does not create achievements or manage achievement state.

Data flow:

```
QuestManager
      |
      |
      v
Achievement Menu
      |
      |
      +--> AchievementCard
      |
      +--> Details Panel
```

The menu reads achievement definitions and runtime progress from the Quest System and converts them into UI elements.

---

# 📋 Achievement List Population

The menu builds the achievement list from registered quest definitions.

Filtering pipeline:

```
Quest Definitions
        |
        v
QuestCategory.ACHIEVEMENT
        |
        v
Visibility Filtering
        |
        v
Sorting
        |
        v
Achievement Cards
```

Only quests with:

```
QuestCategory.ACHIEVEMENT
```

are considered.

---

# 🔒 Achievement Visibility

Achievements support hidden progression.

Visibility rules determine which achievements appear in the menu.

## Visible Achievements

Achievements are displayed when:

* Completed
* First achievement in a chain
* Previously unlocked through progression

---

## Hidden Achievements

Hidden achievements remain unavailable until their unlock condition is met.

Example:

```
Hidden Achievement I

        |
        v

Completed Achievement I

        |
        v

Achievement II Revealed
```

---

## Secret Achievement Reveal

Future filtering will allow completed hidden achievements to appear after completion.

Behavior:

Before completion:

```
[ Hidden Achievement ]
```

After completion:

```
✔ Secret Achievement
```

The achievement remains hidden during progression but becomes visible once completed.

---

# 🔀 Achievement Sorting

Achievements are sorted by:

## Completion State

Incomplete achievements appear before completed achievements.

```
Active / In Progress

Completed
```

---

## Achievement Chain Order

Achievements belonging to the same progression line use:

```
quest_line_id
```

and:

```
quest_index
```

Example:

```
Explorer I
Explorer II
Explorer III
```

---

## General Ordering

Achievements outside of chains use:

```
sort_order
```

---

# 🃏 Achievement Card

## Purpose

`AchievementCard` is the list representation of an achievement.

Each card displays:

* Achievement icon
* Achievement name
* Completion state
* Progress bar

---

# Card Setup

Cards are initialized through:

```gdscript
setup(def: QuestDefinition)
```

Setup performs:

* Store achievement definition
* Assign achievement ID
* Display localized name
* Load icon
* Calculate progress

---

# Progress Display

The card determines display state from QuestManager data.

## Completed Achievement

Displays:

```
✔ Achievement Name

100%
```

---

## Active Achievement

Displays:

```
Achievement Name

████░░░ 4/10
```

Progress is calculated from:

```
ObjectiveState.current_amount
```

against:

```
ObjectiveDefinition.target_amount
```

---

## Locked Achievement

When no active QuestInstance exists:

* Progress bar hidden
* Achievement remains available through visibility rules

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
