# 💬 Dialogue UI System

## Overview

The Dialogue UI provides the player-facing interface for NPC conversations and dialogue interactions.

It displays:

- NPC information
- Dialogue text
- Speaker portraits
- Dialogue options
- Quest interaction options
- Special interaction options
- Leave conversation options

The Dialogue UI acts as the presentation layer for the Dialogue System.



---

# 💬 Dialogue UI

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

## Speaker Header

The header displays the current speaker's identity.

```text
Portrait | NPCName
         | NPCTitle
```

The UI dynamically changes this information whenever a new `QuestDialogueLine` is emitted.

### NPC Speaker

For an NPC speaker, `DialogueUI` retrieves the corresponding `NPCData` from `NPCDatabase` and displays:

* NPC display name
* NPC title
* NPC portrait

### Player Speaker

When:

```gdscript
line.speaker_id == "player"
```

the UI instead displays:

* `GameManager.player_name`
* The player's selected class name
* The selected class portrait

This allows dialogue lines to alternate between NPC and player speakers without requiring a separate UI.

---

## Dialogue Text

The current dialogue line is displayed through:

```gdscript
@onready var dialogue_text: RichTextLabel
```

`DialogueRuntime` emits:

```gdscript
dialogue_line_changed
```

with a `QuestDialogueLine`.

The UI receives that line and displays:

```gdscript
line.text
```

through:

```gdscript
set_dialogue_text()
```

Localization is applied when the text is displayed.

---

## Dialogue Progression

The UI does not advance dialogue itself.

It forwards player input to:

```gdscript
DialogueRuntime.advance()
```

Supported input currently includes:

* `ui_accept`
* Left mouse click

The runtime determines whether another line exists or whether the dialogue has ended.

---

## Dialogue Options

`DialogueUI` receives its available options from:

```gdscript
DialogueManager.get_options()
```

The UI then categorizes the returned options by `OptionTypes.OptionType`.

```text
Options
├── TALK
├── QUEST
├── SPECIAL
└── LEAVE
```

Each category has its own UI container.

### Talk Options

Rendered into:

```gdscript
talk_buttons
```

### Quest Options

Rendered into:

```gdscript
quest_buttons
```

This includes quest-related interactions such as starting, tracking, or interacting with quests as determined by the manager.

### Special Options

Rendered into:

```gdscript
special_buttons
```

### Leave Option

The leave option is rendered separately into:

```gdscript
leave_container
```

Only one Leave option is expected.

---

## Option Rendering

Every option is represented by the reusable:

```text
dialogue_option.tscn
```

scene.

The UI:

1. Instantiates the option scene
2. Passes the option data to it
3. Connects its `selected` signal
4. Adds it to the appropriate container

```text
DialogueManager
      │
      ▼
   get_options()
      │
      ▼
  DialogueUI
      │
      ├── TalkButtons
      ├── QuestButtons
      ├── SpecialButtons
      └── LeaveButton
             │
             ▼
      Dialogue Option
             │
             ▼
     selected(option)
             │
             ▼
   DialogueManager
```

The UI therefore **renders and routes options**, while `DialogueManager` remains responsible for deciding what those options actually do.

---

## Dynamic Option Refresh

Options are rebuilt whenever relevant dialogue state changes.

`DialogueUI` refreshes options when:

* Dialogue runtime becomes active/inactive
* Dialogue ends
* A quest is canceled
* A dialogue controller is assigned

The refresh process is:

```text
DialogueManager.get_options()
        ↓
DialogueUI.set_options()
        ↓
Clear existing buttons
        ↓
Categorize options
        ↓
Instantiate option controls
        ↓
Add controls to their containers
```

This allows quest options to change while the player is interacting with the NPC.

---

## Quest Offer Integration

The UI also participates in the quest-offer conversation flow.

When dialogue ends, it checks the `QuestOfferController`.

If the dialogue was an active quest-offer conversation:

```gdscript
OfferState.IN_CONVERSATION
```

the UI does **not** immediately clear the dialogue.

Instead:

```text
Final dialogue line
       ↓
DialogueRuntime ends
       ↓
QuestOfferController.finalize_offer()
       ↓
DialogueUI refreshes options
```

This allows the final quest-offer dialogue line to remain visible while the resulting quest options are presented.

---

## Quest Cancellation Integration

`DialogueUI` listens for:

```gdscript
QuestManager.quest_canceled
```

When a quest is canceled while the dialogue UI is open, the available NPC options are rebuilt.

This keeps quest-related dialogue choices synchronized with the current quest state.

---

## UI Lifecycle

### Open

`show_ui()`:

* Sets the UI as open
* Shows the CanvasLayer
* Plays the opening animation
* Plays the dialogue UI audio

### Close

`close_menu()` immediately hides the UI.

`hide_ui()` optionally plays the closing animation before hiding the UI.

---

## Data Binding

The current NPC can be explicitly bound through:

```gdscript
set_npc_data(npc: NPCData)
```

This initializes the speaker display using the NPC's:

* Name
* Title
* Portrait

Dialogue lines can subsequently override that display when the active speaker changes.

---

## Architecture

```text
Dialogue Data
      │
      ▼
DialogueRuntime
      │
      │ QuestDialogueLine
      ▼
DialogueUI
      │
      ├── Speaker Header
      ├── Dialogue Text
      └── Dialogue Options
              │
              ▼
       DialogueManager
```

`DialogueUI` is therefore the **presentation and interaction layer** of the dialogue system.

It owns the actual dialogue presentation, speaker display, option containers, option instantiation, input forwarding, animations, and UI refresh behavior, while the dialogue/quest managers remain responsible for resolving the underlying state and actions.





# 🖥 Menu Overview

The Dialogue UI provides the player-facing interface for NPC conversations, dialogue progression, and interaction options.

![Quest Dialogue Menu](../../../media/screenshots/quest_dialogue_menu.png)

The menu presents:

* NPC speaker information
* Dialogue text
* Conversation options
* Quest interaction options
* Special interaction options
* Exit dialogue controls

The interface is responsible for presentation only. Dialogue flow, NPC data, quest logic, and option generation remain owned by the Dialogue and Quest systems.

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
