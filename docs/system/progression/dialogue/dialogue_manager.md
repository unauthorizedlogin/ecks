# 🎭 Dialogue Manager

## Overview

`DialogueManager` is the central NPC interaction controller.

It owns the active dialogue session, resolves NPC context, builds available interaction options, and routes player actions to the appropriate systems.

---

## Architecture

```text
NPC Interaction
      │
      ▼
DialogueManager
      │
      ├── NPCDatabase
      ├── QuestManager
      ├── DialogueDatabase
      ├── DialogueRuntime
      ├── QuestOfferController
      ├── QuestUIAdapter
      ├── UIManager
      └── Vendor / Shop
```

---

## Responsibilities

### Dialogue State

* Opens and closes NPC interactions
* Tracks current NPC and player
* Tracks active dialogue state
* Handles pending quest turn-ins

### Option Resolution

Builds available NPC interaction options for:

* Flavor dialogue
* Quest offers
* Active quests
* Quest turn-ins
* Shops
* Leave

### Quest Integration

* Resolves quests associated with the current NPC
* Starts quest conversations
* Handles quest offers and acceptance
* Handles quest turn-in dialogue
* Notifies `QuestManager` of TALK objectives
* Routes quest completion

### Dialogue Routing

Routes interaction actions into:

* `DialogueRuntime`
* `QuestManager`
* Shop/Vendor systems
* Dialogue UI

### UI Coordination

* Binds the dialogue UI to the manager
* Provides NPC data
* Builds and refreshes interaction options
* Updates dialogue text
* Emits dialogue and option state signals

---

## Core Flow

```text
NPC Interaction
      │
      ▼
DialogueManager.open()
      │
      ├── Resolve NPC
      ├── Bind Dialogue UI
      ├── Build Options
      └── Play Greeting
              │
              ▼
       Player Selects Option
              │
      ┌───────┼────────┐
      ▼       ▼        ▼
   Dialogue  Quest    Shop
      │       │        │
      ▼       ▼        ▼
DialogueRuntime / QuestManager / Vendor
```

---

## Quest Interaction Flow

```text
NPC
 │
 ▼
QuestManager Quest Definitions
 │
 ▼
DialogueManager
 │
 ├── QUEST_INQUIRE
 │       └── Quest Offer
 │
 ├── QUEST_ACCEPT
 │       └── QuestManager.start_quest()
 │
 ├── QUEST_ACTIVE
 │       └── Quest Dialogue
 │
 └── QUEST_TURNIN
         │
         ▼
   Turn-In Dialogue
         │
         ▼
   Objective Validation
         │
         ▼
   QuestManager.complete_quest()
```

---

## Dialogue Types

### Flavor Dialogue

Resolved through the NPC's `dialogue_id`.

Used for:

* Greetings
* General conversation
* State-based NPC dialogue
* Shop greetings

### Quest Dialogue

Resolved through the quest definition and quest dialogue database.

Dialogue state is derived from quest state:

```text
INTRO
ACTIVE
COMPLETE
```

### Shop Dialogue

Shop interaction is routed through the NPC's `shop_id` and vendor system.

---

## Manager State

`DialogueManager` owns the current interaction session:

```text
current_npc_id
current_player
is_open
pending_turn_in_quest_id
ui
```

Supporting controllers:

```text
QuestOfferController
QuestUIAdapter
```

---

## Signals

| Signal            | Purpose                            |
| ----------------- | ---------------------------------- |
| `dialogue_opened` | NPC dialogue session opened        |
| `dialogue_closed` | NPC dialogue session closed        |
| `options_built`   | Interaction options were generated |

---

## Dependencies

`DialogueManager` coordinates with:

* `NPCDatabase`
* `DialogueDatabase`
* `DialogueRuntime`
* `QuestManager`
* `QuestOfferController`
* `QuestUIAdapter`
* `UIManager`
* `ItemRemovalManager`
* Vendor / Shop system

---

## Boundary

`DialogueManager` is the **interaction orchestration layer**.

It does not own:

* NPC definitions
* Quest progression state
* Dialogue resources
* Dialogue runtime execution
* Quest reward processing
* Inventory state
* Shop inventory

Instead, it resolves the current interaction and routes actions to the systems responsible for them.
