# ✨ Environment Database Architecture

## 🧠 Overview

The **Environment Database** is the central runtime repository for environment definitions and reusable environment gradient resources used throughout the framework.

Environments are represented through `ResourceEnvironment` resources, while environment gradients are maintained as `GradientTexture1D` resources. The database provides centralized access to both resource types while keeping environment data separate from the runtime systems that consume and apply it.

The Environment Database is responsible for resource storage, loading, lookup, and display-name resolution. It does not control environmental simulation, weather behavior, seasonal transitions, temperature calculations, or visual environment application.

---

# Responsibilities

The Environment Database is responsible for:

* Loading all environment resources.
* Loading all environment gradient resources.
* Providing environment lookup by Environment ID.
* Providing gradient lookup by Gradient ID.
* Exposing all registered environments.
* Exposing all registered gradients.
* Resolving localized environment display names.
* Providing a centralized runtime repository for environment definitions.
* Supporting initialization timing and resource-count diagnostics.

---

# Does Not

The Environment Database does **not**:

* Simulate weather.
* Control seasons.
* Calculate environmental temperature.
* Apply environmental effects to entities.
* Manage world time.
* Control day/night transitions.
* Modify active environment state.
* Execute environmental gameplay behavior.
* Manage weather particles or visual effects.
* Determine gameplay rules based on environmental conditions.

Those responsibilities belong to environment, weather, world-time, seasonal, and other runtime systems that consume the registered environment data.

---

# Resource Structure

The database manages two primary resource categories:

### Environment Resources

Each environment is represented by a:

```gdscript
ResourceEnvironment
```

Environment resources contain the data required to define an environmental configuration, including its identity and presentation metadata.

### Gradient Resources

Environment gradients are represented by:

```gdscript
GradientTexture1D
```

Gradients provide reusable visual/environmental interpolation data that can be referenced by environment systems.

The database keeps environment definitions and gradients in separate registries.

---

# Initialization Flow

```text
Environment System Access
        │
        ▼
EnvironmentDatabase.initialize()
        │
        ▼
EnvironmentResourceDatabase.load_all()
        │
        ├───────────────┐
        ▼               ▼
Environment Map    Gradient Map
        │               │
        └───────┬───────┘
                ▼
       Environment Database Ready
```

Initialization is guarded by an internal state flag to prevent repeated standard initialization.

The initialization process also records:

* Number of environments loaded.
* Number of gradients loaded.
* Total loading time.

This provides runtime diagnostics without making diagnostics part of the database's functional responsibilities.

---

# Disk Initialization

The database also exposes a separate disk-loading path:

```gdscript
initialize_from_disk()
```

This directly invokes the underlying resource database's `load_all()` operation without modifying the standard initialization state or producing the normal initialization diagnostic output.

This provides an alternate resource refresh path for systems that require direct reloading behavior.

---

# Runtime Access

Primary environment lookups include:

* `get_environment(id)`
* `get_all_ids()`
* `get_all_environments()`
* `get_display_name(id)`

Gradient access includes:

* `get_gradient(id)`
* `get_all_gradient_ids()`
* `get_all_gradients()`

Missing environment or gradient identifiers are reported as errors and return `null`.

---

# Display Name Resolution

The Environment Database provides a dedicated display-name lookup:

```gdscript
get_display_name(id)
```

The database retrieves the environment definition and resolves its localized `display_name` through the engine's translation system.

If the environment cannot be found, the requested ID is returned as the fallback display name.

This keeps localization access centralized without making the database responsible for broader localization management.

---

# Resource Organization

Environment resources can be organized according to environmental categories and visual configuration.

Example:

```text
environments/

├── temperate/
│   ├── spring.tres
│   ├── summer.tres
│   ├── autumn.tres
│   └── winter.tres
│
├── desert/
│   ├── day.tres
│   └── night.tres
│
└── gradients/
    ├── temperature/
    ├── sky/
    └── lighting/
```

The organization of resource files is primarily for content management. Runtime access is performed through registered identifiers rather than directory structure.

---

# Resource Database Dependency

The public `EnvironmentDatabase` delegates resource loading and storage to:

```text
EnvironmentResourceDatabase
```

The architecture separates the public database interface from the underlying resource repository.

```text
EnvironmentDatabase
        │
        ▼
EnvironmentResourceDatabase
        │
        ├── Environment Map
        │       │
        │       ▼
        │  ResourceEnvironment
        │
        └── Gradient Map
                │
                ▼
        GradientTexture1D
```

The public database therefore provides framework-facing access while the resource database maintains the actual resource collections.

---

# Relationship to Other Systems

The Environment Database serves as a shared data source for systems that require environmental configuration.

Typical consumers include:

* World System
* Weather System
* Seasonal System
* Time System
* Environment Controller
* World Generation
* Visual Environment Systems
* Temperature Systems
* UI and localization systems

These systems retrieve environment definitions and gradients from the database rather than maintaining independent copies of environmental configuration.

---

# Design Goals

The Environment Database follows several architectural principles:

* Centralized environmental configuration.
* Reusable environment definitions.
* Reusable gradient resources.
* Separation of environmental data and runtime behavior.
* Centralized localization access for environment names.
* Shared resources across world systems.
* Clear separation between public database access and resource loading.
* Scalable environment content organization.

---

# Benefits

This architecture provides:

* A single source of truth for environment definitions.
* Centralized access to reusable environment gradients.
* Consistent environmental configuration across gameplay systems.
* Separation between environmental data and environmental simulation.
* Reusable visual configuration resources.
* Centralized display-name localization.
* Runtime diagnostics for resource loading.
* A clean boundary between database storage and environment behavior.

The Environment Database functions as the framework's canonical repository for environmental definitions and gradient resources, allowing world and gameplay systems to consume shared environmental data while keeping simulation and runtime behavior outside the database itself.
