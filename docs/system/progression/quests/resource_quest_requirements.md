# 📋 Quest Requirement

## Overview

`QuestRequirement` defines a prerequisite that must be satisfied for a quest.

It provides a generic requirement structure using a requirement type, identifier, and optional numeric value.

---

# Resource Structure

```text
QuestRequirement
│
├── type
├── id
└── amount
```

---

# Requirement Types

| Type            | Purpose                            |
| --------------- | ---------------------------------- |
| `CLASS`         | Requires a specific class          |
| `EQUIPPED_ITEM` | Requires a specific equipped item  |
| `LEVEL`         | Requires a minimum level           |
| `LOCATION`      | Requires a specific location       |
| `STAT`          | Requires a stat threshold          |
| `QUEST`         | Requires a quest-related condition |

`NONE` represents an undefined requirement.

---

# Requirement Data

| Property | Description                                          |
| -------- | ---------------------------------------------------- |
| `type`   | Determines how the requirement is evaluated          |
| `id`     | Identifies the class, item, location, quest, or stat |
| `amount` | Numeric requirement value                            |

```text
QuestRequirement
      │
      ├── type ──────► What is required
      ├── id ────────► What it applies to
      └── amount ────► Required value
```

`amount` is primarily used for numeric checks such as `LEVEL` and `STAT`.

---

# Runtime Boundary

`QuestRequirement` defines the prerequisite but does not evaluate it.

```text
QuestRequirement
       │
       ▼
Quest / Requirement System
       │
       ▼
   Satisfied / Failed
```
