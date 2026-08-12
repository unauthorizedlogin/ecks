# 🎛️ Event Viewer UI

## Overview

`EventViewerUI` is the **presentation and interaction layer for the Event Viewer**.

It displays events from `EventManager` and provides the UI for managing event views, filters, filter colors, and view names.

The UI does **not route or process incoming events**. `EventManager` owns the event data, buffers, views, filters, and configuration; `EventViewerUI` reads that state and renders it.

---

## Architecture Role

```text
EventManager
     │
     ├── Views
     ├── View Order
     ├── View Buffers
     ├── Filters
     └── Filter Colors
            │
            ▼
     EventViewerUI
            │
     ├── Tabs
     ├── Event Log
     ├── Filter Popup
     ├── Add / Rename / Delete
     └── Auto Scroll
```

The UI acts as the **visual controller** between the Event Manager and the player.

---

# Responsibilities

`EventViewerUI` handles:

* Building and selecting event-view tabs.
* Displaying the active view's buffered events.
* Rendering severity and channel information.
* Applying channel-specific colors.
* Opening and updating the filter popup.
* Enabling/disabling filters.
* Changing filter colors.
* Adding custom views.
* Renaming views.
* Deleting views.
* Rebuilding the display when EventManager state changes.
* Automatically scrolling to the newest event.

---

# Scene Structure

The UI expects the following primary structure:

```text
EventViewerUI
└── PanelContainer
    └── MarginContainer
        └── VBoxContainer
            ├── HBoxContainer_Tabs
            │   └── VBoxContainer
            │       ├── TabBar
            │       └── HBoxContainer
            │           ├── Button_Add
            │           ├── Button_Rename
            │           └── Button_Delete
            │
            ├── HBoxContainer_Filters
            │   ├── FilterButton
            │   └── FilterPopup
            │
            └── ScrollContainer
                └── RichTextLabel

└── TabRenameOverlay
```

The main log is a `RichTextLabel` inside a `ScrollContainer`, allowing the Event Viewer to display formatted event history.

---

# EventManager Relationship

The UI relies heavily on `EventManager` for state.

It reads:

```text
EventManager.views
EventManager.view_order
EventManager.view_buffers
EventManager.filter_color_overrides
```

It also uses manager APIs for mutations:

```text
create_view()
rename_view()
delete_view()
set_view_filter()
get_view_filters()
get_filter_color()
save_config()
```

This keeps view and filter state outside the UI.

---

# Tab System

Tabs are generated from `EventManager.view_order`.

```text
EventManager.view_order
        │
        ▼
    _build_tabs()
        │
        ▼
      TabBar
```

Each tab represents a view ID stored by the manager.

Changing tabs updates:

```text
active_view_id
```

and rebuilds the event log for that view.

```text
Tab Changed
    │
    ▼
active_view_id
    │
    ▼
_rebuild()
```

The UI therefore treats the TabBar as a **view selector**, not as the owner of view state.

---

# Event Display

The active view's event buffer comes from:

```text
EventManager.view_buffers[active_view_id]
```

`_rebuild()` clears the current `RichTextLabel` and renders every message in the active buffer.

New events are received through:

```text
EventManager.log_event
```

The UI checks whether the incoming message belongs to the active view's buffer before rendering it.

This means the UI performs **no event routing**.

```text
Event
  │
  ▼
EventManager
  │
  ├── Routes / buffers event
  │
  ▼
EventViewerUI
  │
  └── Displays if present in active buffer
```

---

# Event Rendering

Each `EventViewerMessage` is converted into a BBCode-formatted log line.

The rendered structure is:

```text
[Severity] [CHANNEL] Message
```

For example:

```text
[OK] [COMBAT] Player defeated Skeleton
```

Channel colors are resolved through:

```text
FilterRegistry.channel_map
```

which maps channels to filter IDs.

The final color comes from:

```text
EventManager.get_filter_color(filter_id)
```

This allows the Event Viewer to use configurable colors without embedding channel colors directly into the UI.

---

# Severity Display

Severity is converted into display labels:

```text
ERROR   → [ERROR]
WARN    → [WARN]
SUCCESS → [OK]
OTHER   → ""
```

Severity and channel are therefore presentation concerns handled by the UI.

---

# Filter System

The filter popup is dynamically constructed from:

```text
FilterRegistry.filters
```

Each filter receives:

```text
CheckButton
ColorPickerButton
```

The resulting structure is effectively:

```text
Filter Popup
├── Filter A    [✓] [Color]
├── Filter B    [✓] [Color]
├── Filter C    [ ] [Color]
└── ...
```

The UI reads the active view's filter state from:

```text
EventManager.get_view_filters(active_view_id)
```

and writes changes back through:

```text
EventManager.set_view_filter(...)
```

---

# Filter Colors

Each filter can have its own configurable display color.

Changing a color:

```text
ColorPickerButton
      │
      ▼
EventManager.filter_color_overrides
      │
      ▼
save_config()
      │
      ▼
_rebuild()
```

The Event Viewer therefore supports **persistent user-configurable event colors**.

---

# View Management

The UI provides three view-management operations.

### Add

Creates a new custom view through:

```text
EventManager.create_view()
```

The new view becomes active immediately.

### Rename

The current view is passed to `TabRenameOverlay`, which handles the rename interaction.

Once confirmed:

```text
TabRenameOverlay
      │
      ▼
EventViewerUI
      │
      ▼
EventManager.rename_view()
```

### Delete

The active view is removed through:

```text
EventManager.delete_view()
```

If the deleted view was active, the UI falls back to:

```text
MAIN
```

---

# Rebuild Model

`_rebuild()` is the primary display refresh operation.

```text
_rebuild()
   │
   ├── Clear RichTextLabel
   │
   ├── Get active view buffer
   │
   └── Render each EventViewerMessage
```

The UI does not maintain a second copy of the event history.

The EventManager's buffer is the source of truth.

---

# Auto Scroll

When `auto_scroll` is enabled, each rendered event schedules:

```text
_scroll()
```

which moves the `ScrollContainer` to the bottom of the log.

```text
New Event
   │
   ▼
_render()
   │
   ▼
_scroll()
   │
   ▼
Latest Event Visible
```

---

# Persistence

View and filter configuration changes are saved through:

```text
EventManager.save_config("user://event_config.json")
```

The UI initiates the save after:

* Adding a view
* Renaming a view
* Deleting a view
* Changing a filter
* Changing a filter color

The UI does not directly manage the configuration file.

---

# UI State

The primary local UI state is:

```text
active_view_id
max_lines
auto_scroll
```

`active_view_id` determines which EventManager view is currently displayed.

`max_lines` and `auto_scroll` control presentation behavior.

---

# Architectural Boundary

### `EventViewerUI` owns

* Event Viewer presentation
* Tab interaction
* Filter popup UI
* View-management controls
* Event formatting
* Channel color presentation
* Auto scrolling
* Rebuilding the visible log

### `EventManager` owns

* Event routing
* Event buffers
* View definitions
* View ordering
* Filter state
* Filter color configuration
* View creation/deletion/renaming
* Configuration persistence

### `FilterRegistry` owns

* Available filter definitions
* Filter labels
* Default filter states
* Channel → filter mapping

### `TabRenameOverlay` owns

* Rename interaction UI
* Rename input
* Rename confirmation/cancellation

---

# Core Design

The Event Viewer follows a straightforward **manager → UI** architecture:

```text
        EventManager
             │
     ┌───────┼────────┐
     ▼       ▼        ▼
   Views   Buffers  Filters
     │       │        │
     └───────┼────────┘
             ▼
       EventViewerUI
             │
     ┌───────┼──────────┐
     ▼       ▼          ▼
   Tabs    Log Text   Filters
```

`EventViewerUI` is therefore responsible for **displaying and interacting with EventManager state**, while the manager remains the authoritative owner of the Event Viewer system.
