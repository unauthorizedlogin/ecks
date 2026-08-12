# 🎛️ Dialogue UI Adapter

## Overview

`QuestUIAdapter` bridges **quest runtime state** and **dialogue UI state**.

It determines which dialogue state or quest button state should be presented for a quest/NPC interaction.

## Architecture

```text
QuestManager
     │
     ▼
QuestUIAdapter
     │
     ├── Dialogue State
     │     ├── INTRO
     │     ├── ACTIVE
     │     └── COMPLETE
     │
     └── Button State
           ├── QUEST_START
           ├── QUEST_TRACK
           └── QUEST_OFFERED
```

## Responsibilities

* Resolve dialogue state from quest status
* Resolve quest button state for an NPC
* Verify NPC dialogue eligibility
* Detect pending quest offers
* Translate quest runtime state into UI-facing states

The adapter contains **presentation mapping logic**, but does not own quest state or dialogue execution.
