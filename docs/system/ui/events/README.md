# 📢 Event Viewer System

The Event Viewer System provides the centralized framework for collecting, organizing, filtering, and presenting player-facing runtime events.

Gameplay systems emit structured events through the Event Manager while the Event Viewer provides the player-facing interface for viewing and organizing that information.

The system provides:

* Centralized event logging
* Event message history
* Custom event channels
* Configurable event views
* Channel-based filtering
* Event color formatting
* Persistent viewer configuration
* Dynamic event view management
* Runtime event presentation

---

# 🧠 Event Viewer Architecture

```text
                    Event Viewer System

                         |
                   Event Manager
                         |
              ┌──────────┴──────────┐
              ↓                     ↓
        Filter Registry        Event History
              |                     |
              ↓                     ↓
       Channel Filtering      Event Viewer UI
                                    |
                         ┌──────────┼──────────┐
                         ↓          ↓          ↓
                       Views       Tabs      Channels
                                    |
                                    ↓
                               Event Messages
```

`EventManager` owns event collection, routing, history, and viewer configuration.

`FilterRegistry` defines the available event categories and channel mappings.

The Event Viewer UI consumes that data to provide customizable event presentation.

---

# 🔗 Event Viewer System Documentation

| SystemPurposeDocumentation |                                                               |                          |
| -------------------------- | ------------------------------------------------------------- | ------------------------ |
| 📢 Event Manager           | Central event collection, routing, history, and configuration | [Event Manager](event_manager.md)            |
| 🖥️ Event Viewer UI         | Main player-facing event viewer interface                     | [Event Viewer UI](event_viewer_ui.md)           |
| ✏️ Tab Rename Overlay      | Handles event viewer tab renaming                             | [Event Tab Rename Overlay](event_tab_rename_overlay.md)  |
| 📡 Event Viewer Data       | Defines presentation filtering, for channels, tabs, and views | [Event Data](event_data.md)      |
| 🔎 Filter Registry         | Central event category, channel, and filter definitions       | [Event Filter Registry](event_filter_registry.md)     |

---

# 🔗 System Integration

The Event Viewer System integrates with:

* ⚔️ Combat System
* 📜 Quest System
* 🎁 Loot System
* 🎒 Inventory System
* 👤 NPC System
* 📈 Progression System
* 🌍 World System
* 💾 Save System
* 🖥️ UI System

Gameplay systems provide event messages without directly controlling Event Viewer presentation.

---

# 📌 Design Rule

**`EventManager` owns event collection and routing.**

**`FilterRegistry` defines event categories and channel mappings.**

**The Event Viewer UI presents and organizes event data.**

Gameplay systems should emit structured events through the centralized Event Manager rather than implementing their own player-facing event history or notification systems.
