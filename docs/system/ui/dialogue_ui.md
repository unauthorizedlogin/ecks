# 💬 Dialogue UI System

## Menu Overview

`DialogueUI` is the **visual dialogue interface** for NPC conversations.

The Dialogue Menu provides the player-facing interface for NPC conversations.

![Dialogue Menu](../../../../media/screenshots/quest_dialogue_menu.png)

It is responsible for:

* Displaying the current speaker
* Displaying speaker portrait and title
* Displaying the current dialogue line
* Rendering available dialogue options
* Separating options into Talk, Quest, Special, and Leave sections
* Handling dialogue advance input
* Opening and closing the dialogue presentation
* Refreshing options when dialogue or quest state changes
* Forwarding selected options back to `DialogueManager`

The UI does **not** determine dialogue state, quest state, or available options. It receives those results from the runtime and manager layers.

For dialogue flow, NPC data, dialogue runtime, and quest integration:

See:

[Dialogue System Documentation](../progression/dialogue/)

---

## UI Structure

```text
DialogueUI
│
├── Panel
│   └── Header
│       ├── Portrait
│       └── NameContainer
│           ├── NPCName
│           └── NPCTitle
│
├── DialogueText
│
└── OptionContainer
    │
    ├── TalkButtons
    ├── QuestButtons
    ├── SpecialButtons
    └── LeaveButton
```

The UI is therefore divided into three primary areas:

1. **Speaker Header**
2. **Dialogue Text**
3. **Dialogue Options**

---

# 🧩 Architecture Role

The Dialogue UI is responsible for rendering active dialogue states and forwarding player selections.

It does not:

- Control dialogue flow
- Determine available dialogue options
- Manage NPC data
- Start quests
- Complete quests
- Validate dialogue requirements

Data flow:

```

DialogueManager
|
v
DialogueRuntime
|
v
DialogueUI
|
v
UI Rendering

```

---

# 🎭 Dialogue Display

The main dialogue panel displays:

- Speaker portrait
- Speaker name
- Speaker title
- Dialogue text
- Available options

---

# 👤 Speaker Display

The UI supports multiple speaker types.

## NPC Speaker

NPC dialogue displays:

- NPC name
- NPC title
- NPC portrait

Data source:

```

NPCData

```

---

## Player Speaker

Player dialogue displays:

- Player name
- Selected class title
- Class portrait

Data sources:

```

GameManager
ClassDatabase

```

---

# 📝 Dialogue Text

Dialogue text is displayed through:

```

RichTextLabel

```

The UI receives dialogue lines from:

```

DialogueRuntime.dialogue_line_changed

```

When a new dialogue line is received:

- Text is updated
- Speaker information is refreshed
- Portrait is updated

All displayed dialogue supports localization.

---

# 🎯 Dialogue Options

Dialogue options are dynamically generated from the active dialogue state.

Options are grouped by type:

```

OptionType
|
+-- TALK
|
+-- QUEST
|
+-- SPECIAL
|
+-- LEAVE

```

---

# 💬 Talk Options

Talk options represent standard dialogue choices.

Examples:

```

Ask about the town
Tell me about yourself

```

Displayed in:

```

TalkButtons

```

---

# 📜 Quest Options

Quest options represent quest-related interactions.

Examples:

```

Accept Quest
Complete Quest
Discuss Quest

```

Displayed in:

```

QuestButtons

```

Quest availability and requirements are controlled by the Dialogue System.

---

# ⭐ Special Options

Special options represent unique dialogue actions.

Examples:

```

Trade
Open Shop
Trigger Event

```

Displayed in:

```

SpecialButtons

```

---

# 🚪 Leave Option

Leave options close the current conversation.

Displayed separately from other dialogue choices.

Example:

```

Leave

```

---

# 🧱 Dialogue Option Component

Dialogue options are created from:

```

dialogue_option.tscn

```

Each option:

- Receives option data
- Displays option text
- Emits selection events

Selection flow:

```

Dialogue Option
|
v
Dialogue UI
|
v
DialogueManager.select_option()

```

---

# 🔄 Dialogue Refresh Pipeline

The UI updates through DialogueRuntime signals.

Connected signals:

```

dialogue_line_changed

dialogue_ended

active_state_changed

```

Refresh flow:

```

Dialogue Runtime Event
|
v
Dialogue UI Update
|
+--> Update Text
|
+--> Update Speaker
|
+--> Refresh Options

```

---

# 🧹 Option Management

When options refresh:

1. Existing options are cleared
2. New options are grouped
3. Option buttons are created
4. Selection signals are connected

Option containers:

```

TalkButtons

QuestButtons

SpecialButtons

LeaveButton

```

---

# 🎬 Menu Lifecycle

## Open

Opening the dialogue UI:

- Shows dialogue panel
- Plays opening animation
- Plays dialogue UI audio

---

## Close

Closing the dialogue UI:

- Hides dialogue panel
- Clears active UI state

---

## Animated Closing

The UI supports closing animations through:

```

AnimationPlayer

```

The menu waits for the animation to complete before hiding.

---

# 🎮 Input Handling

The Dialogue UI supports:

## Keyboard Advance

```

ui_accept

```

Advances active dialogue.

---

## Mouse Advance

Left mouse click advances dialogue.

---

# 📡 Signals

## DialogueRuntime Signals

### dialogue_line_changed

Updates:

- Dialogue text
- Speaker information
- Portrait

---

### dialogue_ended

Handles:

- Final dialogue state
- Quest offer finalization
- Option refresh

---

### active_state_changed

Refreshes available options.

---

## QuestManager Signals

### quest_canceled

Refreshes dialogue options when quest availability changes.

---

# 🏗 System Dependencies

| System | Responsibility |
|-|-|
| Dialogue System | Dialogue flow and option generation |
| DialogueRuntime | Active dialogue state |
| DialogueManager | Option selection and dialogue control |
| NPCDatabase | NPC information |
| QuestManager | Quest state changes |
| ClassDatabase | Player class display |
| UIManager | UI state handling |

---

# Current Features

✅ NPC name/title display  
✅ NPC portrait display  
✅ Player speaker display  
✅ Localized dialogue text  
✅ Dynamic option generation  
✅ Talk options  
✅ Quest options  
✅ Special options  
✅ Leave options  
✅ Dialogue animations  
✅ Dialogue audio  
✅ Keyboard advancement  
✅ Mouse advancement  
✅ Quest state refresh handling
