# 🌦️ Environment System

The Environment System provides a data-driven framework for defining and applying world environments through reusable resources and configurable zones.

Environment resources define weather, atmosphere, lighting, audio, and visual behavior while Environment Areas and Shapes control where those environments are applied.

The system provides:

* Environment definitions and presets
* Zone-based environment switching
* Weather and particle effects
* Ambient music transitions
* Fog and atmospheric effects
* Lighting and color grading
* Temperature calculation and display
* Celsius and Fahrenheit support
* Camera environment integration
* Editor visualization and configuration
* Reusable world environment regions

---

# 🧠 Environment Architecture

```text id="i7xyio"
                    Environment System

                          |
                 ResourceEnvironment
                          |
                          ↓
                 Environment Area
                          |
                   Environment Shape
                          |
                          ↓
                 Environment Manager
                          |
        ┌─────────┬─────────┬─────────┬─────────┬───────────┐
        ↓         ↓         ↓         ↓         ↓           ↓
     Weather    Audio      Fog     Lighting   Camera   Temperature
        |         |         |         |         |           |
     Particles  Music     Atmosphere  Visuals   Control   Calculator
                                                           |
                                                           ↓
                                                   Environment Panel
```

`ResourceEnvironment` defines the environment.

`Environment Area` and `Environment Shape` define where environments are applied.

`EnvironmentManager` provides the runtime environment API and coordinates environmental state.

Dedicated systems handle weather, audio, atmosphere, lighting, camera behavior, and temperature.

---

# 🔗 Environment System Documentation

The following documents cover the Environment System components.

| System                     | Purpose                                                               | Documentation                                       |
| -------------------------- | --------------------------------------------------------------------- | --------------------------------------------------- |
| 🌦️ Environment Manager    | Public runtime API and environment state coordination                 | [Environment Manager](environment_manager.md)       |
| 📄 Environment Resource    | Defines environment presets, weather, audio, and visual configuration | [Environment Resource](resource_environment.md)     |
| 🔷 Environment Area        | Defines environment regions within a world                            | [Environment Area](environment_area.md)             |
| 🔶 Environment Shape       | Defines the boundaries and environment assignment for a zone          | [Environment Shape](environment_shape.md)           |
| 🛠️ Environment Generator  | Generates environment resources from source data                      | [Environment Generator](env_resource_generator.md)  |
| 🗄️ Environment Database   | Provides runtime access to environment resources                      | [Environment Database](env_database.md)             |
| 🌧️ Weather System         | Controls environment weather and particle effects                     | [Weather System](weather_system.md)                 |
| 🎵 Environment Audio       | Controls environmental music and audio transitions                    | [Environment Audio](environment_audio.md)           |
| 🌫️ Environment Fog        | Controls environment fog and atmospheric effects                      | [Environment Fog](environment_fog.md)               |
| 🎥 Environment Camera      | Provides environment-driven camera behavior                           | [Environment Camera](environment_camera.md)         |
| 🌡️ Temperature Calculator | Calculates environment temperature values                             | [Temperature Calculator](temperature_calculator.md) |
| 🌡️ Environment Panel      | Displays temperature and controls Celsius/Fahrenheit selection        | [Environment Panel](environment_panel.md)           |

---

# 🔗 System Integration

The Environment System integrates with:

* 🌍 World System
* 🗺️ Map System
* 🎥 Camera System
* 🔊 Audio System
* 🖥️ UI System
* 📄 Data Resources

The Environment System owns environmental state while dedicated systems handle their respective weather, audio, visual, camera, and temperature responsibilities.

---

# 📌 Design Rule

**`ResourceEnvironment` defines the environment.**
**`EnvironmentManager` owns the runtime environment state and public API.**
**`EnvironmentArea` and `EnvironmentShape` define where environments apply.**
**`TemperatureCalculator` determines temperature values.**
**`EnvironmentPanel` controls temperature display and unit selection.**

**Dedicated systems handle weather, audio, atmosphere, visuals, and camera behavior.**

The Environment System should keep environment data, zone configuration, runtime coordination, temperature handling, and individual environmental effects separated so reusable environments can be applied throughout the world without custom scripting.
