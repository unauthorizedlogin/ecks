# 💬 Dialogue Resource Generator System — CSV → Runtime Dialogue Resources

## 🧠 Overview

The Dialogue Generator System converts human-editable dialogue CSV data into runtime-ready Godot dialogue resources.

This separates:

* dialogue authoring
* dialogue organization
* runtime presentation
* export-safe loading

Architecture:

```text
Dialogue CSV
      ↓
Dialogue Resource Generator
      ↓
QuestDialogueResource / FlavorDialogueResource
      ↓
Dialogue Index Generator
      ↓
Runtime Dialogue Database
      ↓
Dialogue System
```

---

# 🎭 Dialogue Resource Types

The dialogue system uses specialized resources depending on the dialogue purpose.

---

# 📜 Quest Dialogue Resource

## `QuestDialogueResource`

Purpose:

Stores quest-specific NPC conversations.

Used for:

* quest offers
* quest progress conversations
* quest completion dialogue
* quest state changes

Resource:

```gdscript
extends Resource
class_name QuestDialogueResource
```

Data:

```gdscript
@export var quest_id: String = ""
var category: String = ""
var subcategory: String = ""

@export var dialogue: Array[QuestDialogueStateBlock]
```

---

## Quest Dialogue Structure

Quest dialogue is organized by quest ID:

```text
Quest ID
    │
    ├── State
    │     │
    │     ├── Dialogue Line
    │     ├── Dialogue Line
    │     └── Dialogue Line
    │
    └── State
          │
          └── Dialogue Lines
```

Example:

```text
quest_blacksmith_help

    ACCEPTED
        Blacksmith:
        "I need materials."

    ACTIVE
        Blacksmith:
        "Any luck finding them?"

    COMPLETE
        Blacksmith:
        "Excellent work."
```

---

# 💬 Flavor Dialogue Resource

## `FlavorDialogueResource`

Purpose:

Stores reusable NPC conversations that are not tied to quests.

Used for:

* NPC chatter
* world flavor
* repeat conversations
* optional dialogue

Resource:

```gdscript
extends Resource
class_name FlavorDialogueResource
```

Data:

```gdscript
@export var dialogue_id: String = ""

var category: String = ""
var subcategory: String = ""

@export var dialogue: Array[QuestDialogueStateBlock]
```

---

# 🗂️ Dialogue State Architecture

Both quest and flavor dialogue use:

```gdscript
QuestDialogueStateBlock
```

Each state contains:

```text
State
 |
 └── QuestDialogueLine[]
```

Each line contains:

```text
NPC ID
Speaker ID
Text
Animation Tag
```

Example:

```text
START

    npc_id:
        blacksmith

    speaker_id:
        blacksmith

    text:
        "Welcome traveler."

    animation:
        wave
```

---

# 🏗️ Quest Dialogue Generator

## `QuestDialogueTRESGenerator`

Purpose:

Converts:

```text
dialogue_quests.csv
```

into:

```text
QuestDialogueResource.tres
```

---

# Input CSV Requirements

Required columns:

```text
quest_id
category
subcategory
state
npc_id
speaker_id
text
```

Optional:

```text
animation_tag
```

---

# Generation Pipeline

```text
dialogue_quests.csv

        ↓

Read Headers

        ↓

Validate Required Columns

        ↓

Group Rows By quest_id

        ↓

Group Lines By State

        ↓

Sort Dialogue Entries

        ↓

Build QuestDialogueResource

        ↓

Save .tres

        ↓

Rebuild Dialogue Index
```

---

# Dialogue Grouping Logic

Rows are grouped by:

```gdscript
quest_id
```

The generator preserves all lines belonging to the same quest.

Example:

CSV:

```text
quest_id,state,text

blacksmith_help,START,"Hello"

blacksmith_help,ACTIVE,"Need more ore"

blacksmith_help,COMPLETE,"Thanks"
```

Produces:

```text
QuestDialogueResource

blacksmith_help

    START
        Hello

    ACTIVE
        Need more ore

    COMPLETE
        Thanks
```

---

# 🏘️ Flavor Dialogue Generator

## `FlavorDialogueTRESGenerator`

Purpose:

Converts:

```text
dialogue_flavor.csv
```

into:

```text
FlavorDialogueResource.tres
```

---

# Input CSV Requirements

Required:

```text
dialogue_id
category
subcategory
state
talk_label
npc_id
speaker_id
text
```

Optional:

```text
animation_tag
```

---

# Flavor Dialogue Differences

Flavor dialogue adds:

```gdscript
talk_label
```

This allows custom interaction labels.

Examples:

```text
Talk

Ask About Rumors

Discuss Trade

Hear Story
```

The generator extracts the label from dialogue state data.

If no label exists:

```gdscript
"Talk"
```

is used.

---

# Flavor Dialogue Structure

Example:

```text
dialogue_id:

villager_rumors


IDLE

    Talk Label:
        Ask About Rumors

    Lines:

        Villager:
        "Have you heard..."
```

---

# 🔄 Shared Generator Architecture

Both generators follow the same pipeline:

```text
CSV

 ↓

Validate

 ↓

Parse Rows

 ↓

Group Dialogue

 ↓

Sort Entries

 ↓

Create Resources

 ↓

Save .tres

 ↓

Generate Index
```

---

# 📦 Generated Output

## Quest Dialogue

Output:

```text
res://data/databases/dialogue/quests/
```

Example:

```text
quest_blacksmith_help.tres
quest_intro.tres
quest_bandit_attack.tres
```

---

## Flavor Dialogue

Output:

```text
res://data/databases/dialogue/flavor/
```

Example:

```text
villager_rumors.tres
merchant_chat.tres
guard_warning.tres
```

---

# 📇 Dialogue Index Generator

## `QuestDialogueIndexGenerator`

Purpose:

Creates export-safe deterministic dialogue loading.

Input:

```text
Generated .tres dialogue resources
```

Output:

```text
dialogue_index.gd
```

---

# Index Generation

Scans:

```text
data/databases/dialogue/flavor/

data/databases/dialogue/quests/
```

Generates:

```gdscript
const FLAVOR_DIALOGUE_PATHS := []

const QUESTS_DIALOGUE_PATHS := []
```

---

# Why Index Generation Exists

The dialogue system does not scan directories at runtime.

Instead:

```text
Dialogue Resources

        ↓

Generated Index

        ↓

Runtime Database
```

Benefits:

* export-safe
* deterministic loading
* faster initialization
* no filesystem dependency
* guaranteed resource inclusion

---

# Runtime Flow

```text
NPC Interaction

        ↓

Dialogue Manager

        ↓

Dialogue Database

        ↓

Dialogue Resource

        ↓

Dialogue State

        ↓

Dialogue Lines

        ↓

Dialogue UI
```

---

# Design Philosophy

Dialogue authoring remains separated from runtime behavior.

```text
CSV
 |
 | Human editable
 |
 ↓
Dialogue Resource
 |
 | Runtime optimized
 |
 ↓
Dialogue System
```

The generator owns conversion.

The runtime system only consumes generated resources.

---

# ✅ Summary

The Dialogue Generator System provides a complete content pipeline:

```text
Quest CSV
      ↓
QuestDialogueTRESGenerator
      ↓
QuestDialogueResource


Flavor CSV
      ↓
FlavorDialogueTRESGenerator
      ↓
FlavorDialogueResource


Generated Resources
      ↓
DialogueIndexGenerator
      ↓
Runtime Dialogue Loading
```

The system supports scalable quest dialogue, reusable NPC flavor conversations, custom interaction labels, state-driven dialogue progression, and deterministic export-safe loading.
