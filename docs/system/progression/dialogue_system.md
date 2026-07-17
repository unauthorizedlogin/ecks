# 🎭 Dialogue System Overview

- NPC interaction framework
- Quest-aware dialogue states
- Flavor dialogue support
- Data-driven dialogue resources

## Dialogue Manager

The Dialogue Manager is the central NPC interaction controller.

It is responsible for:
- Opening and closing conversations
- Resolving NPC dialogue context
- Building available player options
- Routing dialogue actions
- Connecting dialogue with quests and shops

The Dialogue Manager acts as the bridge between:
- NPC Database
- Dialogue Database
- Quest Manager
- Shop System
- Dialogue UI
- Dialogue Runtime

---

## Core Responsibilities

### NPC Interaction

The Dialogue Manager receives an NPC interaction request:

NPC ID
↓
NPCDatabase lookup
↓
Dialogue context resolution
↓
Available options generated
↓
Dialogue UI populated

---

## Dialogue Option Pipeline

Available options are generated dynamically:

1. Flavor Dialogue
2. Quest Dialogue
3. Shop Options
4. Leave Option

The player never receives hardcoded dialogue buttons.

Options are created based on current game state.

---

## Quest Integration

The Dialogue Manager handles:

- Quest inquiries
- Quest offers
- Accepting quests
- Declining quests
- Active quest dialogue
- Quest turn-in conversations
- Quest completion routing

Quest state determines which dialogue options are available.

---

## Flavor Dialogue

Flavor dialogue provides NPC personality interactions.

Supports:

- GREET states
- SHOP_GREET states
- Custom talk states

Reserved states are filtered from normal talk options.

---

## Shop Integration

NPC shops are exposed through dialogue options.

The Dialogue Manager:
- Detects vendor availability
- Opens shop interaction
- Handles shop greeting dialogue

---

## Conversation State

The Dialogue Manager tracks:

- Current NPC
- Current player
- Dialogue open state
- Pending quest turn-ins

DialogueRuntime controls active conversation progression.

---

## Design Philosophy

The Dialogue Manager does not store dialogue content.

It coordinates systems.

Content ownership remains with:
- Dialogue Resources
- NPC Data
- Quest Definitions
- Database Managers

The Dialogue Manager is the interaction authority.

# 🎬 Dialogue Runtime

## Overview

DialogueRuntime is the active conversation state controller.

While DialogueManager handles:
- NPC interaction
- Option generation
- System routing

DialogueRuntime handles:
- Active dialogue state
- Current dialogue block
- Line progression
- Conversation lifecycle

---

# Responsibilities

DialogueRuntime manages:

- Starting dialogue sessions
- Tracking current dialogue resource
- Tracking active NPC
- Tracking current dialogue state block
- Advancing dialogue lines
- Ending conversations
- Broadcasting dialogue events

---

# Dialogue Lifecycle

Dialogue flow:

```

DialogueManager
|
v
DialogueRuntime.start()
|
v
Load dialogue resource
|
v
Find matching state block
|
v
Display first line
|
v
Advance through lines
|
v
Dialogue ends

```

---

# Dialogue States

Runtime tracks the current dialogue category:

## Flavor Dialogue

Used for:
- NPC personality conversations
- Ambient interactions
- Optional talk options

Tracked with:

```

is_flavor_active

```

---

## Quest Dialogue

Used for:
- Quest introductions
- Active quest conversations
- Completion dialogue

Tracked with:

```

is_quest_active

```

---

## Shop Dialogue

Reserved for:

```

is_shop_active

```

Shop greetings are handled through the dialogue pipeline but shop ownership remains with the shop system.

---

# Active Dialogue Data

Runtime stores the current conversation context:

- Active dialogue resource
- Active NPC ID
- Active dialogue block
- Current line index

Example:

```

NPC
|
Dialogue Resource
|
State Block
|
Dialogue Lines

```

---

# Dialogue Progression

Lines are advanced sequentially.

Current line:

```

active_block.lines[line_index]

```

When the final line is reached:

```

DialogueRuntime.end()

```

is called automatically.

---

# Signals

DialogueRuntime broadcasts state changes:

## Dialogue Started

```

dialogue_started(npc_id)

```

Used when a conversation begins.

---

## Dialogue Ended

```

dialogue_ended()

```

Used by systems requiring post-dialogue actions.

Example:
- Quest turn-ins waiting for completion dialogue

---

## Line Updates

```

line_changed(text)

```

Updates displayed dialogue text.

```

dialogue_line_changed(line)

```

Provides full dialogue line data.

---

# Design Philosophy

DialogueRuntime owns the conversation state.

It does not:
- Generate options
- Resolve quests
- Open shops
- Search NPC databases

Those responsibilities remain with:

- DialogueManager
- QuestManager
- Shop System
- Dialogue UI

DialogueRuntime is the playback and state authority for active conversations.

# 🎯 Quest Offer Controller

## Overview

QuestOfferController manages the temporary state of NPC quest offers during dialogue.

It acts as the bridge between:

```

DialogueManager
|
v
QuestOfferController
|
v
QuestManager

```

Its purpose is to track when a player is currently discussing a quest offer before the quest is accepted or declined.

---

# Responsibilities

QuestOfferController manages:

- Active quest offer data
- NPC associated with the offer
- Offer conversation state
- Clearing cancelled offers

It does not:
- Start quests
- Complete quests
- Validate quest requirements
- Store quest definitions

Those remain owned by:

- QuestManager
- QuestDefinition
- DialogueManager

---

# Offer State Flow

Quest offers move through three states:

```

NONE
|
|
v
IN_CONVERSATION
|
|
v
OFFERED

````

---

## NONE

Default state.

No quest offer is currently being discussed.

---

## IN_CONVERSATION

The player has selected a quest inquiry.

The controller stores:

- Quest ID
- NPC ID

The quest dialogue is now active.

---

## OFFERED

The quest dialogue has completed and the player can:

- Accept
- Decline

The DialogueManager uses this state to build the correct quest buttons.

---

# Offer Data

Stored offer information:

```gdscript
{
    "quest_id": quest_id,
    "npc_id": npc_id
}
````

This allows the dialogue system to maintain context between:

```
Quest Inquiry
      |
      v
Quest Dialogue
      |
      v
Accept / Decline
```

---

# Lifecycle

## Begin Offer

Called when the player selects a quest inquiry.

```
begin_offer()
```

Sets:

```
state = IN_CONVERSATION
```

---

## Finalize Offer

Called when the quest dialogue has reached the offer stage.

```
finalize_offer()
```

Sets:

```
state = OFFERED
```

---

## Clear Offer

Called when:

* Quest accepted
* Quest declined
* Dialogue cancelled
* Conversation closed

Resets:

```
state = NONE
```

---

# Design Philosophy

QuestOfferController is a temporary dialogue state tracker.

It separates:

* Dialogue progression
* Quest availability
* Quest ownership

This allows quest conversations to have their own flow without modifying QuestManager state until the player accepts the quest.

# 🔌 Quest UI Adapter

## Overview

QuestUIAdapter is the translation layer between quest state and dialogue UI behavior.

It converts:

```

QuestManager State
|
v
Quest UI State
|
v
Dialogue Options

```

The adapter does not own quest logic.

It only determines how a quest should appear during NPC interaction.

---

# Responsibilities

QuestUIAdapter handles:

- Resolving dialogue state from quest status
- Determining available quest button states
- Filtering quests by NPC relationship

It does not:

- Start quests
- Complete quests
- Track objectives
- Modify quest state

Those remain owned by:

- QuestManager
- QuestInstance
- QuestDefinition

---

# Dialogue State Resolution

Quest dialogue state is determined from the current quest status.

Mapping:

```

NOT_STARTED
|
v
INTRO

IN_PROGRESS
|
v
ACTIVE

COMPLETED
|
v
COMPLETE

```

This allows quest dialogue resources to respond dynamically to progression.

---

# Quest Button State Resolution

The adapter determines which quest interaction button should be generated.

Pipeline:

```

Quest Definition
|
v
NPC Relationship Check
|
v
Quest Status Check
|
v
Dialogue Button State

```

---

# Button States

## QUEST_START

Displayed when:

- Quest exists
- NPC is connected to the quest
- Player has not accepted it yet

---

## QUEST_TRACK

Displayed when:

- Player has accepted the quest
- Quest is currently active

---

## QUEST_OFFERED

Displayed when:

- Quest is not started
- A pending dialogue offer exists

This separates:

```

Available Quest

```

from:

```

Currently Being Offered

```

---

# NPC Filtering

Quests are only exposed through NPC dialogue when the NPC is linked to the quest definition.

Validation:

```

QuestDefinition.dialogue_keys
|
v
Current NPC ID

```

This prevents unrelated NPCs from showing quest options.

---

# Design Philosophy

QuestUIAdapter keeps dialogue presentation separate from quest authority.

The Dialogue System asks:

> "How should this quest appear to the player?"

The Quest System remains responsible for:

> "What is the actual quest state?"

This keeps UI decisions flexible without duplicating quest logic.
```

At this point the dialogue flow documentation chain is:

```
DialogueManager
        |
        v
DialogueRuntime
        |
        v
QuestOfferController
        |
        v
QuestUIAdapter
        |
        v
QuestManager

# 🎭 Quest Dialogue Resource

## Overview

The Quest Dialogue Resource is the runtime dialogue container generated from CSV dialogue data.

Each resource represents all dialogue states for a single quest and provides the dialogue content used by the Dialogue Manager and Dialogue Runtime systems.

Dialogue resources are generated automatically through the Dialogue TRES Generator pipeline.

---

# 📦 Resource Structure

```

QuestDialogueResource
│
├── quest_id
├── category
├── subcategory
│
└── dialogue[]
│
└── QuestDialogueStateBlock
│
├── state
│
└── lines[]
│
└── QuestDialogueLine
│
├── npc_id
├── speaker_id
├── text
└── animation_tag

```

---

# QuestDialogueResource

## Purpose

Primary quest dialogue container.

Stores all dialogue states and dialogue lines associated with a quest.

Generated as:

```

quest_id.tres

```

Example:

```

bandit_cleanup.tres

```

---

## Properties

| Property | Description |
|---|---|
| quest_id | Unique quest identifier |
| category | Dialogue grouping category |
| subcategory | Dialogue grouping subcategory |
| dialogue | Array of dialogue state blocks |

---

# QuestDialogueStateBlock

## Purpose

Represents a dialogue state within a quest.

States allow dialogue content to change based on quest progression.

---

## Supported States

| State | Usage |
|---|---|
| INTRO | Quest has not started |
| ACTIVE | Quest is currently active |
| COMPLETE | Quest has been completed |
| Custom | Additional quest-specific states |

---

## Properties

| Property | Description |
|---|---|
| state | Dialogue state identifier |
| lines | Array of dialogue lines for this state |

---

# QuestDialogueLine

## Purpose

Single dialogue entry displayed during a conversation.

---

## Properties

| Property | Description |
|---|---|
| npc_id | NPC associated with the dialogue |
| speaker_id | Character delivering the line |
| text | Dialogue text content |
| animation_tag | Optional presentation/animation hook |

---

# Generation Pipeline

```

dialogue_quests.csv
|
v
QuestDialogueTRESGenerator
|
v
QuestDialogueResource (.tres)
|
v
DialogueDatabase
|
v
DialogueManager
|
v
DialogueRuntime

```

---

# CSV Source Format

Required columns:

| Column | Purpose |
|---|---|
| quest_id | Quest identifier |
| category | Dialogue category |
| subcategory | Dialogue grouping |
| state | Dialogue progression state |
| npc_id | NPC reference |
| speaker_id | Speaker reference |
| text | Dialogue content |

Optional:

| Column | Purpose |
|---|---|
| animation_tag | Future presentation hooks |

---

# Runtime Usage

The Dialogue Manager resolves the correct dialogue resource based on:

- Active quest state
- NPC interaction
- Quest progression
- Dialogue state mapping

The Dialogue Runtime handles:

- Current dialogue block
- Line progression
- Dialogue signals
- Conversation lifecycle

---

# Design Goals

- Data-driven dialogue authoring
- CSV-based content creation
- Runtime optimized resources
- Quest-state aware conversations
- Expandable presentation hooks
- Automatic resource generation
- Indexed database lookup
