# 🧩 Quest Behavior Matrix

## Overview

`QuestBehaviorMatrix` defines the default runtime behavior associated with each `QuestCategory`.

It keeps category-specific rules centralized instead of embedding them throughout the quest system.

---

# Architecture

```text
QuestDefinition
      │
      ▼
 Quest Category
      │
      ▼
QuestBehaviorMatrix
      │
      ▼
QuestBehavior
```

---

# Behavior Rules

| Category      | Auto Track | Chain Locked | Cancel | Resets | Reputation |
| ------------- | :--------: | :----------: | :----: | :----: | :--------: |
| `NONE`        |      ❌     |       ❌      |    ❌   |    ❌   |      ❌     |
| `STORY`       |      ✅     |       ✅      |    ❌   |    ❌   |      ❌     |
| `SIDE`        |      ❌     |       ❌      |    ✅   |    ❌   |      ❌     |
| `DAILY`       |      ❌     |       ❌      |    ✅   |    ✅   |      ❌     |
| `FACTION`     |      ❌     |       ❌      |    ✅   |    ❌   |      ✅     |
| `CHAINED`     |      ✅     |       ✅      |    ✅   |    ❌   |      ❌     |
| `ACHIEVEMENT` |      ❌     |       ❌      |    ❌   |    ❌   |      ❌     |

---

# QuestBehavior

Each category resolves to a `QuestBehavior` containing:

```text
QuestBehavior
├── auto_track
├── chain_locked
├── can_cancel
├── resets
└── uses_reputation
```

`get_behavior()` retrieves the behavior rules for a `QuestDefinition` based on its quest category.

---

# Runtime Role

```text
QuestDefinition
      │
      │ quest_category
      ▼
QuestBehaviorMatrix
      │
      ▼
QuestBehavior
      │
      ▼
Quest Runtime Rules
```

The matrix provides **default category behavior**; it does not manage quest state or progression itself.
