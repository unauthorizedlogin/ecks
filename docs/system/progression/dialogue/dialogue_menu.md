# 💬 Dialogue System

## Overview

The Dialogue System handles NPC conversations, dialogue state selection, quest dialogue, flavor dialogue, dialogue offers, and dialogue UI.

It is divided into:

* **Dialogue Data** — authored dialogue resources and dialogue line/state structures
* **Dialogue Runtime** — executes active dialogue
* **Dialogue Manager** — resolves NPC options and routes interactions
* **Quest Offer Controller** — manages quest offer conversation state
* **Quest UI Adapter** — converts quest state into dialogue states/buttons
* **Dialogue UI** — presents dialogue, speakers, portraits, and options

---

## Architecture

```text
                    ┌─────────────────────┐
                    │    Dialogue Data    │
                    │                     │
                    │ Flavor Dialogue     │
                    │ Quest Dialogue      │
                    │ State Blocks        │
                    │ Dialogue Lines      │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │  Dialogue Manager   │
                    │                     │
                    │ NPC context         │
                    │ Options             │
                    │ Quest integration    │
                    └──────┬─────────┬────┘
                           │         │
              ┌────────────┘         └────────────┐
              ▼                                   ▼
┌─────────────────────────┐          ┌─────────────────────────┐
│   Dialogue Runtime      │          │  Quest Offer Controller │
│                         │          │                         │
│ Active dialogue         │          │ Offer state             │
│ Active state block      │          │ Quest/NPC offer         │
│ Current line            │          │ Conversation → Offered  │
│ Line progression        │          └─────────────────────────┘
└────────────┬────────────┘
             │
             ▼
┌─────────────────────────┐
│      Dialogue UI        │
│                         │
│ Speaker / Portrait      │
│ Dialogue text           │
│ Talk options            │
│ Quest options           │
│ Special options         │
│ Leave option            │
└─────────────────────────┘
```

---

# 📦 Dialogue Data

Dialogue is authored as reusable Resource data.

```text
DialogueResource
│
├── FlavorDialogueResource
│   └── dialogue_id
│
├── QuestDialogueResource
│   └── quest_id
│
├── QuestDialogueStateBlock
│   ├── state
│   ├── talk_label
│   └── lines[]
│
└── QuestDialogueLine
    ├── npc_id
    ├── speaker_id
    ├── text
    └── animation_tag
```

### Dialogue Types

| Resource                  | Purpose                          |
| ------------------------- | -------------------------------- |
| `FlavorDialogueResource`  | General NPC/flavor conversations |
| `QuestDialogueResource`   | Quest-specific conversations     |
| `QuestDialogueStateBlock` | Dialogue for a specific state    |
| `QuestDialogueLine`       | Individual spoken line           |

Dialogue states can represent things such as:

```text
INTRO
ACTIVE
COMPLETE
```

---

# 🎬 Dialogue Runtime

`DialogueRuntime` is the execution layer for active conversations.

It manages:

* Active dialogue resource
* NPC currently speaking with the player
* Active dialogue state block
* Current line index
* Dialogue progression
* Dialogue start/end signals
* Flavor vs quest dialogue state

```text
DialogueManager
      │
      │ start dialogue
      ▼
DialogueRuntime
      │
      ├── Resolve State Block
      │
      ├── Set Line Index
      │
      ├── Emit Current Line
      │
      ├── Advance
      │
      └── End
             │
             ▼
        Dialogue UI
```

### Runtime Signals

```text
active_state_changed
dialogue_started
dialogue_ended
line_changed
dialogue_line_changed
```

`dialogue_line_changed` provides the complete `QuestDialogueLine`, allowing the UI to resolve the speaker, portrait, title, and text.

---

# 🎯 Quest Dialogue Integration

Quest dialogue is driven by quest state rather than hardcoded dialogue selection.

```text
QuestManager
     │
     ▼
QuestUIAdapter
     │
     ├── NOT_STARTED → INTRO
     ├── IN_PROGRESS → ACTIVE
     └── COMPLETED   → COMPLETE
```

The adapter also determines the appropriate quest interaction button state:

```text
Quest State
     │
     ▼
QuestUIAdapter
     │
     ├── QUEST_START
     ├── QUEST_TRACK
     └── QUEST_OFFERED
```

This keeps quest-state interpretation outside the dialogue UI.

---

# 🤝 Quest Offer Controller

`QuestOfferController` manages the temporary runtime state of a quest being offered through dialogue.

```text
NONE
  │
  │ begin_offer()
  ▼
IN_CONVERSATION
  │
  │ finalize_offer()
  ▼
OFFERED
```

It stores:

```text
quest_id
npc_id
```

This allows the final dialogue line to remain visible while the quest offer becomes available.

---

# 🎨 Dialogue UI

`DialogueUI` is the presentation layer.

```text
DialogueRuntime
      │
      ▼
DialogueUI
      │
      ├── Speaker
      ├── Portrait
      ├── Dialogue Text
      │
      └── Options
           ├── Talk
           ├── Quest
           ├── Special
           └── Leave
```

The UI handles:

* Opening/closing dialogue
* Speaker display
* Player/NPC portrait switching
* Dialogue text
* Dialogue line progression input
* Quest option display
* Talk option display
* Special option display
* Leave option display
* Quest cancellation refresh
* Dialogue option selection

It does **not** determine quest state or resolve quest eligibility.

---

# 🔄 Full Dialogue Flow

```text
NPC Interaction
      │
      ▼
DialogueManager
      │
      ├──────────────► QuestUIAdapter
      │                     │
      │                     ▼
      │                Quest State
      │
      ▼
Dialogue Data
      │
      ▼
DialogueRuntime
      │
      ├── State Block
      ├── Current Line
      └── Advance
             │
             ▼
        DialogueUI
             │
             ├── Speaker
             ├── Portrait
             ├── Text
             └── Options
```

### Architecture Boundary

```text
DATA
  Dialogue Resources
       ↓
RESOLUTION
  DialogueManager
  QuestUIAdapter
       ↓
RUNTIME
  DialogueRuntime
  QuestOfferController
       ↓
PRESENTATION
  DialogueUI
```
