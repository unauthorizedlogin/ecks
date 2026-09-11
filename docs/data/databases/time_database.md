# ⏰ Time Database

## 🧠 Overview

The **Time Database** is the central runtime repository for all `TimeData` resources used by the framework.

Time configurations define reusable world-time settings that can be referenced by the Time System and other world systems. The database provides centralized access to these configurations while keeping time-definition data separate from the runtime systems responsible for advancing and interpreting world time.

The Time Database is responsible for resource loading, storage access, identifier lookup, and localized display-name resolution. It does not simulate time, advance the world clock, calculate calendar phases, or manage temporal runtime state.

---

# Responsibilities

The Time Database is responsible for:

* Loading all registered `TimeData` resources.
* Providing time configuration lookup by Time ID.
* Exposing all registered Time IDs.
* Exposing all registered time configurations.
* Resolving localized time configuration display names.
* Providing a centralized repository for reusable time configurations.
* Reporting initialization and loading performance diagnostics.

---

# Does Not

The Time Database does **not**:

* Advance world time.
* Track the current world date or time.
* Manage time scaling.
* Calculate seasons.
* Calculate day phases.
* Calculate moon phases.
* Broadcast time-change events.
* Manage temporal runtime state.
* Control environmental lighting.
* Execute time-based gameplay behavior.

Those responsibilities belong to the Time Manager and its supporting calendar, day-phase, moon-phase, and other temporal runtime systems.

---

# Resource Structure

Each registered time configuration is represented by a:

```gdscript
TimeData
```

A `TimeData` resource defines reusable configuration data for the world-time system.

Time configurations are referenced by a unique Time ID, allowing runtime systems to retrieve the appropriate definition without depending on the resource's physical file path.

---

# Initialization Flow

```text
Time System Access
        │
        ▼
TimeDatabase.initialize()
        │
        ▼
TimeResourceDatabase.load_all()
        │
        ▼
Load TimeData Resources
        │
        ▼
Register Time Configurations
        │
        ▼
Time Database Ready
```

Initialization is protected by `is_initialized` to ensure the resource database is loaded only once through the standard initialization path.

The initialization process also records the number of loaded time configurations and total loading duration for runtime diagnostics.

---

# Runtime Access

Primary runtime lookups include:

* `get_time(id)`
* `get_all_ids()`
* `get_all_times()`
* `get_display_name(id)`

Example:

```gdscript
TimeDatabase.get_time(time_id)
```

Missing Time IDs are reported as errors and return `null`.

---

# Display Name Resolution

The Time Database provides centralized display-name resolution through:

```gdscript
get_display_name(id)
```

The database retrieves the corresponding `TimeData` resource and passes its `display_name` through the engine's translation system.

If the requested configuration does not exist, the Time ID itself is returned as the fallback display name.

This allows time configuration resources to remain localization-aware without making the database responsible for broader localization management.

---

# Resource Organization

Time configurations are organized as reusable world-time definitions.

Example:

```text
time/

├── default/
│   └── standard_time.tres
│
├── fantasy/
│   ├── long_days.tres
│   └── short_days.tres
│
└── custom/
    └── campaign_time.tres
```

The physical organization of resources is primarily for content management. Runtime systems access configurations through their registered Time IDs.

---

# Resource Database Dependency

The public `TimeDatabase` delegates resource loading and storage to:

```text
TimeResourceDatabase
```

The architecture separates the framework-facing database interface from the underlying resource repository.

```text
TimeDatabase
      │
      ▼
TimeResourceDatabase
      │
      ▼
TimeData Resources
```

`TimeDatabase` therefore provides the centralized runtime access layer while `TimeResourceDatabase` manages the actual collection of loaded time resources.

---

# Relationship to Other Systems

The Time Database serves as a shared data source for systems requiring time configuration data.

Typical consumers include:

* Time Manager
* Calendar Systems
* Day Phase Systems
* Moon Phase Systems
* Season Systems
* World Systems
* Environment Systems
* UI and Localization Systems

Consumers retrieve time configurations by ID rather than maintaining independent copies of temporal configuration data.

---

# Design Goals

The Time Database follows several architectural principles:

* Centralized time configuration.
* Reusable `TimeData` resources.
* Separation of time definitions from time simulation.
* Identifier-based runtime access.
* Centralized localization of time configuration names.
* Separation between public database access and resource loading.
* Shared configuration across temporal world systems.

---

# Benefits

This architecture provides:

* A single source of truth for time configurations.
* Reusable temporal definitions across worlds and systems.
* Consistent time configuration throughout the framework.
* Clear separation between configuration data and time simulation.
* Centralized localization support.
* Lightweight runtime access through Time IDs.
* Runtime loading diagnostics.
* A clean architectural boundary between data storage and temporal behavior.

The Time Database functions as the framework's canonical repository for `TimeData` definitions, providing the Time System and other world systems with shared temporal configuration while leaving time progression, calendar calculation, and temporal behavior to dedicated runtime systems.
