# Environment Manager

The `EnvironmentManager` provides the public API and orchestration layer for the Environment System.

It manages active environment state, environment selection, weather and visual effects, environmental audio, temperature calculation, time-driven refreshes, world attachment, and environment persistence.

## Responsibilities

* Maintain the active `ResourceEnvironment`.
* Resolve environments from `EnvironmentShape` conditions.
* Apply weather, fog, music, and color-correction effects.
* Track and calculate current temperature.
* React to `TimeManager` changes.
* Manage the active environment shape and refresh rules.
* Attach environment effects to the active world.
* Follow the active camera with world environment FX.
* Register environment areas.
* Serialize and deserialize environment state.
* Emit environment and temperature state changes.

## Architecture

```text
EnvironmentManager
├── Environment Selection
│   ├── EnvironmentShape
│   ├── Season
│   ├── Day Phase
│   ├── Moon Phase
│   └── Weighted Selection
│
├── Environment State
│   ├── Current Environment
│   ├── Active Environment Shape
│   └── Environment Areas
│
├── Environment Effects
│   ├── Rain
│   ├── Snow
│   ├── Clouds
│   ├── Leaves
│   ├── Fog
│   ├── Heatwave
│   ├── Music
│   └── Color Correction
│
├── Temperature
│   └── TemperatureCalculator
│
└── Persistence
    ├── serialize_environment()
    └── deserialize_environment()
```

## Public API

### World

* `attach_world(world, map)` — attaches environment FX and world references.
* `set_active_environment_shape(environment_shape)` — sets the active environment selection source.
* `refresh_environment()` — re-resolves the active environment shape.
* `apply_environment(environment)` — applies an environment's effects.

### Environment Selection

* `resolve_environment_shape(environment_shape)` — filters and weighted-selects an eligible environment.
* `set_active_environment_shape(environment_shape)` — establishes the active selection shape.
* `refresh_environment()` — re-evaluates the active shape.

Selection considers:

* Season
* Day phase
* Moon phase
* Environment weight

Season filtering is applied for dynamic seasonal shapes, while configured day-phase and moon-phase filters are always evaluated.

## Time Integration

`EnvironmentManager` listens to `TimeManager` signals:

* Second
* Minute
* Hour
* Day
* Week
* Month
* Year
* Season
* Day phase
* Moon phase

`EnvironmentShape.refresh_modes` determines which time changes trigger environment re-selection.

Temperature is recalculated on:

* Minute changes
* Season changes
* Environment changes
* Temperature-unit changes

## Environment Effects

`apply_environment()` maps `ResourceEnvironment.Meteo` entries to world FX nodes.

Supported effects include:

* Light rain
* Rain
* Heavy rain
* Light snow
* Snow
* Heavy snow
* Partly cloudy
* Cloudy
* Overcast
* Leaves
* Fog
* Partly sunny
* Sunny
* Heatwave

Environment resources may additionally control:

* Background/environment music
* Color-correction gradients

## Temperature

Temperature is calculated through `TemperatureCalculator` using:

* Current environment
* Season
* World year
* Month
* Day
* Hour
* Minute
* Second
* Selected temperature unit

`use_celsius` controls Celsius/Fahrenheit output.

`temperature_changed` is emitted only when the calculated value changes.

## Signals

```text
environment_changed(environment)
temperature_changed(temperature)
```

`environment_changed` is emitted when the active environment changes.

`temperature_changed` is emitted when the calculated temperature changes or when persisted temperature state is restored.

## Camera Integration

World environment FX follow the active `Camera2D`.

Each frame:

```text
EnvironmentFX.global_position = Camera.global_position
EnvironmentFX.global_rotation = Camera.global_rotation
```

The active camera is acquired from `CameraManager`.

This keeps screen-space environmental particle effects aligned with the player's active camera.

## Environment Areas

The manager maintains registered `EnvironmentArea` instances.

```text
register_environment_area(area)
unregister_environment_area(area)
```

Duplicate registrations are prevented.

## Persistence

`serialize_environment()` stores:

* Active environment ID
* Current temperature
* Temperature unit

`deserialize_environment()` restores the environment through `EnvironmentDatabase`, reapplies its effects, restores temperature settings, and emits the appropriate state signals.

## Initialization

`initialize()` is idempotent and establishes all `TimeManager` signal connections.

The manager does not initialize more than once.

## Design Principle

`EnvironmentManager` is the **public environment API and orchestration boundary**.

It coordinates environment selection, runtime effects, temporal updates, temperature, world attachment, and persistence while relying on dedicated resources and calculators for environment data and temperature logic.
