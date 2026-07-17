# 📢 Event Manager (`al_event_manager.gd`) — System Role

The **EventManager** is the centralized event logging, routing, filtering, and history authority for the entire framework.

All gameplay systems send player-facing notifications through this system. Combat, quests, loot, NPCs, progression, inventory, and world systems do not directly control the Event Viewer UI — they emit structured messages and EventManager handles distribution.

- Centralized gameplay event tracking and feedback system
- Custom event channels for organizing gameplay information
- Configurable filters and display categories
- Color-coded event messages for different gameplay events
- Localization-ready event messages
- Saveable viewer configuration

---

# 🧠 Core Responsibility

EventManager provides:

* Centralized event collection
* Message history storage
* Multi-view event routing
* User-configurable filters
* Persistent viewer configuration
* Channel-based filtering
* Color formatting support
* Runtime view creation/deletion
* Event buffer management

Architecture:

```
Gameplay Systems
        |
        ↓
EventManager.emit_log()
        |
        ↓
EventViewerMessage
        |
        ↓
Filter Routing
        |
        ↓
Event Buffers
        |
        ↓
Event Viewer UI
```

---

# 📡 Event Emission Authority

Every system communicates through:

```gdscript
EventManager.emit_log(EventViewerMessage.new(
    "CHANNEL",
    "Message"
))
```

Examples:

## Combat

```
COMBAT
🔥 Enemy took 50 damage
💚 Player healed 25 HP
```

---

## Loot

```
LOOT
Obtained Legendary Sword
Purchased Potion
Sold Item
```

---

## Quest

```
QUEST
Quest Started
Objective Completed
Reward Received
```

---

## System

```
SYSTEM
World Loaded
Save Complete
Game Initialized
```

---

# 🗂️ Message History System

All events are permanently tracked during runtime:

```gdscript
var message_history: Array[EventViewerMessage] = []
```

Purpose:

* Rebuilding filters
* Switching views
* Restoring event tabs
* Future save/load support

Flow:

```
New Event
   |
   ↓
message_history
   |
   ↓
View Filtering
```

---

# 👁️ Multi-View Event System

EventManager supports multiple independent event feeds.

Example:

```
MAIN
 ├─ Combat
 ├─ Loot
 ├─ Quest
 └─ System


COMBAT LOG
 └─ Combat only


LOOT TRACKER
 └─ Loot only
```

Each view contains:

```gdscript
{
"id",
"name",
"filters",
"ui"
}
```

---

# 🏗️ View Management

## Create View

```gdscript
create_view()
```

Creates:

* View identity
* Default filters
* Buffer storage

Example:

```
MAIN
PVP LOG
QUEST TRACKER
LOOT HISTORY
```

---

## Rename View

```gdscript
rename_view()
```

Allows player customization.

---

## Delete View

```gdscript
delete_view()
```

Protection:

```gdscript
if view_id == "MAIN":
    return
```

The primary event channel always exists.

---

# 🔎 Filter Architecture

Filtering is controlled through:

```gdscript
DEFAULT_FILTERS
```

Current baseline:

```gdscript
{
combat:true,
loot:true,
quest:true,
system:true
}
```

Each view maintains its own filter state.

Example:

```
MAIN

✓ Combat
✓ Loot
✓ Quest
✓ System


QUEST VIEW

✗ Combat
✗ Loot
✓ Quest
✗ System
```

---

# 🧩 Filter Registry Integration

EventManager does not hardcode channel colors or IDs.

Instead:

```gdscript
FilterRegistry
```

controls:

* Available filters
* Default states
* Channel mapping
* Colors

Pipeline:

```
Event Channel
      |
      ↓
FilterRegistry.channel_map
      |
      ↓
Filter ID
      |
      ↓
View Filter State
```

---

# 🎨 Event Color System

Provides centralized color formatting:

```gdscript
colorize(text, color)
```

Used by gameplay systems:

Example:

```gdscript
EventManager.colorize(
    "Legendary Sword",
    EventManager.ITEM_COLOR
)
```

Current categories:

| Category | Color |
| -------- | ----- |
| Quest    | Gray  |
| Item     | Gold  |
| XP       | Brown |
| Level    | Blue  |
| Growth   | Green |
| XP Loss  | Red   |
| NPC      | Cyan  |
| Damage   | Red   |
| Healing  | Green |

---

# 🔥 Event Routing Pipeline

When:

```gdscript
emit_log()
```

fires:

### 1. Store History

```gdscript
message_history.append(msg)
```

---

### 2. Check Every View

```gdscript
_matches_view()
```

---

### 3. Add To Matching Buffers

```gdscript
view_buffers[view_id].append(msg)
```

---

### 4. Notify UI

```gdscript
log_event.emit(msg)
```

---

Result:

```
Combat Event
      |
      |
      +---- MAIN BUFFER
      |
      +---- COMBAT BUFFER
      |
      +---- PLAYER CUSTOM VIEW
```

---

# 🔄 Dynamic Buffer Rebuilding

When filters change:

```gdscript
rebuild_buffers()
```

The system:

1. Clears all buffers
2. Replays message history
3. Applies current filters
4. Rebuilds visible logs

This allows:

* Changing filters without losing events
* Custom views
* Runtime customization

---

# 💾 Persistent Configuration

EventManager saves:

```gdscript
event_config.json
```

Stored:

* Created views
* View ordering
* Filter states
* Color overrides
* UI configuration

Example:

```
user://event_config.json

{
 views,
 view_order,
 filter_color_overrides
}
```

---

# 🎛️ UI Independence

EventManager does not know:

* EventViewer layout
* Tabs
* Buttons
* Panels
* Scroll containers

It only provides:

```
Data
Signals
Buffers
Filtering
```

The UI consumes the data.

---

# 🏛️ Current Architecture Position

```
                 EVENT MANAGER
                      |
       --------------------------------
       |              |               |
 Event Messages   Filtering       Persistence
       |
       |
 -------------------------------
 |       |        |       |      |
Combat Quest    Loot   NPC   System
```

---

# ✅ Final System Summary

The EventManager is the **global player feedback backbone**.

It provides:

✅ centralized event authority
✅ multi-channel logging
✅ customizable event views
✅ persistent filter configuration
✅ runtime event history
✅ color-coded messaging
✅ decoupled UI communication

Every gameplay system now reports events through a single standardized pipeline instead of managing its own notifications.

## 🗂️ FilterRegistry — Event Classification Authority

`event_filter_registry.gd` is the **single source of truth for all Event Viewer categories, channel mappings, default visibility, and event colors**.

All event messages are routed through this registry before reaching EventManager views.

No system creates or manages its own event categories. New event channels must be registered here.

---

# 🎯 Channel Mapping

`channel_map` translates incoming event channels into registered filter IDs.

This allows multiple event sources to share the same filter category.

Example:

```gdscript
"objective": "quest"
"mission": "quest"
```

Both objective and mission events are routed into the Quest filter.

---

# 🌎 World Communication Filters

Handles future communication systems:

| Filter        | Purpose                         |
| ------------- | ------------------------------- |
| `global`      | Global chat messages            |
| `local`       | Nearby player communication     |
| `recruitment` | Group/clan recruitment messages |
| `trade`       | Trading channel messages        |
| `whisper`     | Private messages                |

---

# 👥 Social Filters

Handles player/community communication:

| Filter   | Purpose                |
| -------- | ---------------------- |
| `clan`   | Clan communication     |
| `guild`  | Guild announcements    |
| `party`  | Party communication    |
| `raid`   | Raid communication     |
| `social` | General social systems |

---

# ⚔️ Player Activity Filters

Gameplay-facing event categories:

| Filter   | Purpose                                  |
| -------- | ---------------------------------------- |
| `combat` | Damage, healing, attacks, combat effects |
| `loot`   | Items, rewards, purchases, currency      |
| `player` | Player progression and personal events   |
| `quest`  | Quest progression, objectives, missions  |
| `help`   | Tutorials, hints, assistance messages    |

Mapped aliases:

```gdscript
"objective" → "quest"
"mission" → "quest"
```

---

# ⚙️ System Filters

Framework and engine reporting:

| Filter    | Purpose                             |
| --------- | ----------------------------------- |
| `boot`    | Startup and initialization messages |
| `data`    | Database and resource loading       |
| `system`  | General framework messages          |
| `warning` | Warning messages                    |
| `error`   | Error reporting                     |

Mapped aliases:

```gdscript
"warning" → "system"
"error" → "system"
```

---

# 🎨 Filter Metadata

Each filter contains:

```gdscript
{
    "label": Display Name,
    "default": Enabled By Default,
    "color": Event Color
}
```

Example:

```gdscript
"combat": {
    "label": "Combat",
    "default": true,
    "color": Color(170,0,0)
}
```

This provides:

* localized UI labels
* default user preferences
* consistent event coloring

---

# 🔄 Event Routing Flow

```text
Event Source
      ↓
EventViewerMessage(channel)
      ↓
FilterRegistry.channel_map
      ↓
Filter ID
      ↓
EventManager View Filters
      ↓
Event Viewer
```

Example:

```gdscript
EventViewerMessage.new(
    "OBJECTIVE",
    "Quest objective completed"
)
```

becomes:

```text
OBJECTIVE
    ↓
quest
    ↓
Quest Filter
    ↓
Displayed if enabled
```

---

# 🧩 Framework Role

FilterRegistry provides:

✅ centralized event taxonomy
✅ shared channel naming
✅ default filter states
✅ event color definitions
✅ alias routing
✅ future chat/channel expansion support

It acts as the **event category database** for the entire framework.
