# 🧭 Event Filter Registry

## Overview

`event_filter_registry.gd` is the **central definition registry for Event Viewer channels and filters**.

It defines:

* Which event channels exist.
* Which channels map to which filters.
* The display label for each filter.
* Default filter state.
* Default display color.

The Event Viewer UI and `EventManager` use this registry rather than maintaining their own copies of channel/filter definitions.

---

## Architecture Role

```text
Event Source
     │
     │ channel
     ▼
FilterRegistry
     │
     ├── Channel Mapping
     │
     └── Filter Definition
            │
            ├── Label
            ├── Default Enabled
            └── Color
            │
            ▼
      Event Viewer UI
```

The registry acts as the **static configuration layer** for Event Viewer filtering.

---

# Channel Mapping

`channel_map` translates raw event channels into filter IDs.

For example:

```text
"objective" → "quest"
"mission"   → "quest"
"warning"   → "system"
"error"     → "system"
```

This allows multiple event channels to share a single UI filter.

The Event Viewer can therefore receive distinct event sources while presenting them under a common filter category.

---

# Filter Definitions

`filters` contains the actual filter configuration.

Each filter defines:

```text
Filter
├── label
├── default
└── color
```

For example:

```text
combat
├── "Combat"
├── enabled by default
└── configured display color
```

These definitions are consumed by the Event Viewer filter popup and rendering system.

---

# UI Integration

`EventViewerUI` reads the registry when constructing its filter controls.

```text
FilterRegistry.filters
        │
        ▼
_build_filter_popup()
        │
        ├── CheckButton
        │     └── Filter label
        │
        └── ColorPickerButton
```

The registry therefore determines which filters appear in the UI.

The UI does not need individual controls hardcoded for every channel.

---

# Color Integration

The registry provides the **default color** for each filter.

`EventManager` can subsequently provide an overridden color for the active configuration.

The effective flow is:

```text
FilterRegistry
     │
     │ default color
     ▼
EventManager
     │
     │ optional user override
     ▼
EventViewerUI
     │
     ▼
Rendered Event Color
```

This allows the registry to provide sensible defaults while still supporting user customization.

---

# Filter Categories

The current registry organizes filters conceptually into:

### World

* Global
* Local
* Recruitment
* Trade
* Whisper

### Personal

* Clan
* Raid
* Party
* Guild

### Player

* Achievements
* Combat
* Loot
* Player
* Quest
* Help

### System

* Boot
* Data
* System
* Warning
* Error

These categories are currently represented through code organization rather than a separate data structure.

---

# Current Architecture

The registry is currently code-defined:

```text
event_filter_registry.gd
        │
        ├── channel_map
        └── filters
```

It is **not yet part of the CSV → generated data pipeline**.

That conversion is planned.

---

# Planned Data Generation

The intended direction is to move the filter definitions into generated data, similar to the other Event Viewer and Credits data systems.

Conceptually:

```text
CSV
 │
 ▼
Generator
 │
 ▼
Generated Filter Data
 │
 ├── Channel mappings
 ├── Filter labels
 ├── Default states
 └── Colors
 │
 ▼
FilterRegistry
 │
 ▼
Event Viewer
```

This would remove the filter definitions from the manually maintained registry code while preserving `FilterRegistry` as the runtime access point.

---

# Architectural Boundary

### `FilterRegistry` owns

* Channel → filter mapping.
* Filter definitions.
* Default filter states.
* Default filter colors.

### `EventManager` owns

* Active filter state.
* User color overrides.
* Persistent Event Viewer configuration.
* Applying filters to event views.

### `EventViewerUI` owns

* Filter popup presentation.
* CheckButton state.
* Color picker presentation.
* User interaction.

### Event Data owns

* Individual event messages.
* Event channels.
* Event severity.
* Event content.

---

# Core Design

`FilterRegistry` is the **definition layer**, not the runtime state layer.

```text
WHAT FILTERS EXIST?
        │
        ▼
FilterRegistry

WHAT IS CURRENTLY ENABLED?
        │
        ▼
EventManager

HOW DOES THE USER CONTROL IT?
        │
        ▼
EventViewerUI
```

Its eventual CSV/data-generation conversion fits directly into the existing data-driven architecture without changing the responsibilities of the Event Viewer UI or `EventManager`.
