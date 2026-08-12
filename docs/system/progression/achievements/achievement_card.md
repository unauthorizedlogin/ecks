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
