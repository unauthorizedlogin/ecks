# 🌦️ Environment Resource Architecture

### Purpose

`ResourceEnvironment` defines the data model for a world environment state, including weather conditions, seasonal eligibility, climate ranges, visual presentation, audio, and temperature effects.

### Architecture Role

```text
Environment Database
        ↓
ResourceEnvironment
├── Identity
├── Eligibility
├── Environment
├── Climate
└── Meteo Temperature Effects
```

`ResourceEnvironment` is a data-only `Resource` consumed by the Environment Manager and environment database.

### Responsibilities

* Define environment identity and presentation.
* Define seasonal, day-phase, and moon-phase eligibility.
* Define weighted environment selection.
* Define available meteorological conditions.
* Define environment music and visual color gradients.
* Define temperature ranges by season.
* Define Celsius/Fahrenheit climate configuration.
* Define temperature modifiers for each meteorological condition.
* Provide temperature-range and meteo-effect lookup helpers.
* Provide compact debug information.

### Meteo System

`Meteo` defines the supported environmental conditions:

* Rain: light, normal, heavy
* Snow: light, normal, heavy
* Fog
* Cloud coverage: partly cloudy, cloudy, overcast
* Foliage
* Sun: partly sunny, sunny
* Heat wave
* Cold front

Each condition can apply an independent temperature effect through `get_meteo_temperature_effect()`.

### Eligibility & Selection Data

Environment availability is controlled through:

* `seasons`
* `day_phases`
* `moon_phases`
* `weight`

`weight` provides the selection weighting used by higher-level environment selection logic.

### Climate Data

`temperature_ranges` stores seasonal minimum/maximum temperatures through `TemperatureRange` resources.

`get_temperature_range()` performs a case-insensitive seasonal lookup and returns the configured range as a `Vector2`.

`use_celsius` determines the environment's configured temperature unit.

### Temperature Effects

Weather conditions modify the environment temperature through dedicated effect values.

```text
Base Seasonal Temperature
        ↓
Meteo Condition
        ↓
get_meteo_temperature_effect()
        ↓
Temperature Adjustment
```

Effects are grouped into Heat, Clouds, Rain, and Cold categories for editor organization.

### Debug Information

`get_debug_info()` produces a compact display string containing:

* Environment display name
* Music filename
* Active meteo conditions

This is intended for runtime/editor diagnostic presentation.

### File(s) Affected

* `resource_environment.gd`
* `TemperatureRange`
* Environment Database
* Environment Manager
* Environment resource generator

### Notes

`ResourceEnvironment` is the authoritative serialized definition for an individual environment. It contains configuration and lookup helpers but does not manage runtime environment state, selection, weather effects, or world transitions.
