# 🌡️ Environment Panel

### Purpose

This `Control` provides the UI control for selecting the displayed temperature unit and synchronizes the selection with `EnvironmentManager`.

### Architecture Role

```text
Temperature Display UI
        ↓
EnvironmentManager
        ↓
Temperature System
```

### Responsibilities

* Initialize the temperature unit from `EnvironmentManager`.
* Reflect the current unit in the toggle control.
* Allow the player to switch between Fahrenheit and Celsius.
* Forward unit changes to `EnvironmentManager`.

### Initialization

On `_ready()`:

* Reads `EnvironmentManager.use_celsius`.
* Synchronizes the checkbox state.
* Connects the toggle signal if not already connected.

### Unit Selection

When the toggle changes:

```text
Toggle
  ↓
use_celsius
  ↓
EnvironmentManager.set_temperature_unit()
```

The UI does not perform temperature conversion itself. Unit handling remains centralized in `EnvironmentManager` and the temperature calculation layer.

### File(s) Affected

* Temperature display UI script
* `EnvironmentManager`
* `TemperatureCalculator`

### Notes

This component is strictly a **presentation/input layer** for temperature units. It maintains the UI state and delegates the actual temperature-unit configuration to `EnvironmentManager`.
