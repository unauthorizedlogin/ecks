# 🌡️ Temperature Calculator
### Purpose

`TemperatureCalculator` calculates the current environmental temperature from seasonal temperature ranges, time of day, meteorological effects, and deterministic date/time variation.

### Architecture Role

```text
ResourceEnvironment
        ↓
TemperatureCalculator
├── Seasonal Base Temperature
├── Daily Thermal Cycle
└── Meteo Temperature Effects
        ↓
Current Temperature
```

`TemperatureCalculator` is a stateless `RefCounted` calculation service. It does not own environment state or manage temperature updates.

### Responsibilities

* Calculate temperature for a specific world date/time.
* Resolve the environment's seasonal temperature range.
* Model the daily temperature cycle.
* Apply meteorological temperature effects.
* Generate deterministic meteo intensity from environment/date/time.
* Apply warming or cooling direction per meteo condition.
* Convert the final temperature from Fahrenheit to Celsius when requested.

### Temperature Calculation

The calculation pipeline is:

```text
Seasonal Range
      ↓
Daily Thermal Phase
      ↓
Base Temperature
      +
Meteo Effects
      ↓
Final Temperature
      ↓
Optional Celsius Conversion
```

The daily thermal cycle uses:

* **05:00** — daily minimum
* **15:00** — daily maximum

A smoothstep curve is applied between the minimum and maximum temperatures to avoid linear temperature transitions.

### Meteorological Effects

Each active meteo condition retrieves its configured temperature effect from `ResourceEnvironment`.

Conditions are assigned a temperature direction:

```text
Warming
├── Sunny
├── Partly Sunny
└── Heat Wave

Cooling
├── Rain
├── Snow
├── Cold Front
├── Fog
├── Clouds
└── Leaf
```

Multiple active meteo conditions contribute independently to the final temperature.

### Deterministic Meteo Variation

Meteo intensity is generated from a hash containing:

* Environment ID
* Year
* Month
* Day
* Meteo type

A phase offset is derived from that hash, while the current time drives a repeating **6-hour variation period**.

This produces deterministic but continuously varying meteo temperature influence for the same environment and world date/time.

### Temperature Units

The final calculated value remains Fahrenheit unless `use_celsius` is enabled.

Celsius conversion occurs only after the base temperature and all meteo effects have been combined.

### File(s) Affected

* `temperature_calculator.gd`
* `ResourceEnvironment`
* `TemperatureRange`
* Environment Manager

### Notes

`TemperatureCalculator` contains the **temperature mathematics layer** of the environment system.

It does not select environments, manage weather state, modify `ResourceEnvironment`, or control when temperature calculations occur. Runtime scheduling and environment state remain outside the calculator.
