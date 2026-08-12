# 💬 Dialogue Data

## Overview

The Dialogue Data layer defines the authored dialogue content used by the dialogue system.

It stores dialogue identifiers, states, lines, speakers, presentation metadata, and state-specific interaction labels.

---

## Architecture

```text
Dialogue Data
│
├── Dialogue Resource
│   ├── dialogue_id / quest_id
│   └── State Blocks[]
│
├── State Block
│   ├── state
│   ├── talk_label
│   └── Dialogue Lines[]
│
└── Dialogue Line
    ├── npc_id
    ├── speaker_id
    ├── text
    └── animation_tag
```

---

## Dialogue Resources

### `FlavorDialogueResource`

Defines non-quest dialogue content.

```text
FlavorDialogueResource
└── dialogue[]
    └── QuestDialogueStateBlock
```

### `QuestDialogueResource`

Defines quest-specific dialogue content and associates it with a quest through `quest_id`.

```text
QuestDialogueResource
└── quest_id
└── dialogue[]
    └── QuestDialogueStateBlock
```

---

## State Blocks

`QuestDialogueStateBlock` organizes dialogue by interaction state.

Examples include:

* `START`
* `ACTIVE`
* `COMPLETE`
* `TURN_IN`

Each block contains:

* State identifier
* Talk button label
* Ordered dialogue lines

---

## Dialogue Lines

`QuestDialogueLine` defines an individual spoken line.

| Property        | Purpose                             |
| --------------- | ----------------------------------- |
| `npc_id`        | NPC associated with the line        |
| `speaker_id`    | Speaker identifier                  |
| `text`          | Dialogue text                       |
| `animation_tag` | Optional presentation/animation tag |

---

## Data Flow

```text
Dialogue Resource
       │
       ▼
 State Block
       │
       ▼
 Dialogue Lines
       │
       ▼
 DialogueRuntime
       │
       ▼
 Dialogue UI
```

The resources define **what can be said**; `DialogueRuntime` handles **playing it**.
