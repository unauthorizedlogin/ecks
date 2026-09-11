# 🏞️ Environment Shape

### Purpose

`EnvironmentShape` defines an environmental zone and its available `ResourceEnvironment` candidates. It provides the Environment Manager with a resolved environment pool that can respond to world time and seasonal state.

### Architecture Role

```text
World / Level
    ↓
EnvironmentShape
    ├── ResourceEnvironment[]
    └── Season Selection
            ↓
    EnvironmentManager
            ↓
    TimeManager State
```

`EnvironmentShape` is a `CollisionShape2D` used as the spatial definition for an environment zone.

### Responsibilities

* Define refresh conditions for dynamic environment resolution.
* Store explicitly assigned environment resources.
* Select environments by season.
* Resolve unique environment candidates.
* Provide selected season data.
* Maintain editor-only environment debug information.

### Refresh Modes

`RefreshMode` defines world-state changes that can trigger environment reevaluation:

* Second
* Minute
* Hour
* Day
* Week
* Month
* Year
* Season
* Day Phase
* Moon Phase

`NONE` disables refresh-based reevaluation.

The actual refresh processing is handled by the Environment Manager rather than the shape itself.

### Environment Resolution

`get_environments()` builds the available environment pool from two sources:

```text
Explicit Resource Environments
        +
Season Environment Selection
        ↓
Unique ResourceEnvironment[]
```

Explicitly assigned resources are added first.

For each selected season, the shape queries `EnvironmentDatabase` and adds environments whose configured `seasons` contain a case-insensitive match.

Duplicate resources are removed.

### Season Selection

`season_environments` stores season identifiers used to dynamically expand the environment pool.

`get_selected_seasons()` returns a duplicate of the configured selection, preventing callers from directly modifying the exported array.

### Editor Debug Display

When running in the editor, `update_debug_info()` creates an internal `Label` and displays the resolved environment information inside the collision shape.

Each environment contributes its `ResourceEnvironment.get_debug_info()` output.

The debug label updates when referenced environment resources emit their `changed` signal.

### File(s) Affected

* `environment_shape.gd`
* `ResourceEnvironment`
* Environment Database
* Environment Manager

### Notes

`EnvironmentShape` owns **zone configuration and candidate resolution**, not runtime environment state.

The Environment Manager remains responsible for selecting and applying the active environment based on `TimeManager` state and the shape's configured refresh modes.
