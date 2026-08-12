# 📢 Event Manager

## Overview

`EventManager` is the central event logging and routing system for the Event Viewer.

It receives structured `EventViewerMessage` events from game systems, maintains global message history, routes messages into configurable event views, applies per-view filters, and persists Event Viewer configuration.

The manager separates **event production** from **event presentation**, allowing gameplay systems to emit events without needing to know how the Event Viewer is configured or displayed.

---

## Architecture

```text
Game Systems
     │
     │ EventViewerMessage
     ▼
┌─────────────────────┐
│    EventManager     │
│                     │
│  Message History    │
│  Event Routing      │
│  View Management    │
│  Filter Resolution  │
│  Buffer Management  │
│  Configuration      │
└─────────┬───────────┘
          │
          ├──────────────► Event Views
          │                  │
          │                  └── Filtered Buffers
          │
          ├──────────────► Event Viewer UI
          │
          └──────────────► Config File
                             │
                             └── user://event_config.json
```

---

## Responsibilities

### Event Collection

`EventManager` provides the central event entry point:

```text
emit_log(EventViewerMessage)
```

When an event is emitted, the manager:

1. Stores the message in global history.
2. Evaluates the message against every registered view.
3. Routes matching messages into the appropriate view buffers.
4. Emits `log_event` for listeners.

This allows gameplay systems to remain independent of Event Viewer UI state.

---

### Message History

The manager maintains:

```text
message_history
```

This acts as the authoritative runtime history of emitted `EventViewerMessage` objects.

History is intentionally separate from individual view buffers.

This allows filters and views to change without losing previously emitted events.

```text
Event History
     │
     ├── MAIN View
     ├── Combat View
     ├── Quest View
     └── Custom View
```

When view filters change, buffers can be rebuilt from the existing message history.

---

## View System

Event Manager supports multiple independent Event Viewer views.

Each view contains:

```text
id
name
filters
ui
```

Runtime storage is maintained separately through:

```text
views
view_order
view_buffers
```

### View Responsibilities

A view defines:

* Display identity
* Filter configuration
* UI-specific configuration
* Ordering within the Event Viewer
* Its filtered event buffer

The manager owns the data and routing; the UI is responsible for presenting it.

---

## Default View

`MAIN` is the protected default Event Viewer view.

The manager initializes it as:

```text
MAIN
```

with the localized display name:

```text
Main
```

The `MAIN` view cannot be deleted.

Custom views can be created, renamed, and deleted.

---

## View Lifecycle

```text
Create View
    │
    ▼
Register View
    │
    ├── Create Filter State
    ├── Create Event Buffer
    └── Add to View Order
          │
          ▼
       Active View
          │
     ┌────┴────┐
     ▼         ▼
 Rename      Delete
```

### Create

`create_view()` creates the view definition and its runtime buffer.

### Rename

`rename_view()` changes the user-facing display name without changing the view identity.

### Delete

`delete_view()` removes the view, its buffer, and its ordering entry.

`MAIN` is protected from deletion.

---

## Filter Architecture

Filtering is handled independently from event generation.

Each view maintains its own filter state:

```text
View
 └── filters
      ├── combat
      ├── loot
      ├── quest
      └── system
```

The manager resolves the appropriate filter using `FilterRegistry`.

```text
Event Channel
      │
      ▼
FilterRegistry.channel_map
      │
      ▼
Filter ID
      │
      ▼
View Filter State
      │
      ├── Enabled → Route
      └── Disabled → Ignore
```

This allows the event channel names used by gameplay systems to remain decoupled from the configurable filter categories presented to the user.

---

## Filter Color Resolution

Event Manager also provides centralized filter color resolution.

Resolution order:

```text
Custom Color Override
        │
        ▼
FilterRegistry Color
        │
        ▼
White Fallback
```

Custom filter colors are stored in:

```text
filter_color_overrides
```

This allows Event Viewer presentation colors to be customized without modifying the underlying filter registry.

---

## Event Routing

Every emitted event passes through the view routing layer.

```text
EventViewerMessage
        │
        ▼
message_history
        │
        ▼
For Each View
        │
        ▼
_matches_view()
        │
   ┌────┴────┐
   ▼         ▼
 MATCH     NO MATCH
   │         │
   ▼         └── Ignore
View Buffer
```

The routing decision is based on:

```text
Event Channel
      │
      ▼
FilterRegistry.channel_map
      │
      ▼
View Filter State
```

---

## Buffer Architecture

Each view has its own runtime buffer:

```text
view_buffers[view_id]
```

Buffers contain only messages currently matching that view's filter configuration.

This creates a separation between:

### Global History

```text
message_history
```

The complete runtime event stream.

### View Buffers

```text
view_buffers
```

Filtered projections of that event stream.

This architecture allows the Event Viewer to rebuild filtered views without requiring gameplay systems to re-emit events.

---

## Filter Rebuild

When a view's filter configuration changes, Event Manager calls:

```text
rebuild_buffers()
```

The rebuild process:

```text
Clear All Buffers
      │
      ▼
Iterate Message History
      │
      ▼
Evaluate Each Message
      │
      ▼
Route Matching Messages
      │
      ▼
Updated View Buffers
```

This makes the filter system dynamically reconfigurable while preserving event history.

---

## Configuration Persistence

Event Manager persists Event Viewer configuration to:

```text
user://event_config.json
```

Persisted data includes:

```text
views
view_order
filter_color_overrides
```

View UI configuration is also retained through the view's `ui` dictionary.

---

## Configuration Lifecycle

```text
initialize()
     │
     ▼
load_config()
     │
     ├── Restore Views
     ├── Restore View Order
     ├── Restore Filters
     ├── Restore UI State
     └── Restore Color Overrides
              │
              ▼
       Rebuild Buffers
```

Runtime changes such as creating, renaming, or modifying filters can trigger configuration persistence.

---

## Signals

| Signal          | Purpose                                                       |
| --------------- | ------------------------------------------------------------- |
| `log_event`     | Broadcasts a newly emitted `EventViewerMessage`               |
| `views_updated` | Notifies listeners that view configuration or buffers changed |

### `log_event`

Used by Event Viewer consumers that need to react immediately to new events.

### `views_updated`

Used when the Event Viewer needs to refresh its view structure or filtered data.

---

## Core Data Model

```text
EventManager
│
├── views
│    └── view_id
│         ├── id
│         ├── name
│         ├── filters
│         └── ui
│
├── view_order
│
├── view_buffers
│    └── view_id
│         └── EventViewerMessage[]
│
├── message_history
│    └── EventViewerMessage[]
│
└── filter_color_overrides
```

---

## Dependencies

`EventManager` coordinates with:

* `EventViewerMessage`
* `FilterRegistry`
* Event Viewer UI
* `FileAccess`
* JSON configuration storage

Gameplay managers and systems interact with Event Manager primarily through:

```text
EventManager.emit_log()
```

They do not need to manage Event Viewer views, filters, buffers, or configuration.

---

## Architectural Boundary

`EventManager` owns the **event presentation data pipeline**, not the gameplay events themselves.

### Event Manager Owns

* Event collection
* Event history
* View definitions
* View ordering
* Filter state
* Filter routing
* View buffers
* Filter color overrides
* Event Viewer configuration persistence

### Event Manager Does Not Own

* Combat state
* Quest state
* Loot generation
* Player state
* Gameplay event creation
* Event Viewer visual layout
* Gameplay system logic

Gameplay systems produce structured events.

`EventManager` determines **where those events are routed and how they are filtered**.

---

## Design Principle

The Event Manager acts as the boundary between **gameplay telemetry and UI presentation**.

```text
Gameplay Systems
      │
      │ "Something happened"
      ▼
EventManager
      │
      │ "Where should this event appear?"
      ▼
Filtered Event Views
      │
      ▼
Event Viewer UI
```

This keeps gameplay systems independent of Event Viewer implementation while providing a centralized, persistent, configurable event stream.
