# ⏰ Time System

The Time System provides the centralized framework for managing simulated world time and calendar state.

Time configuration is data-driven through `TimeData`, while runtime time is exposed through `TimeManager` and specialized helpers handle calendar, day-phase, and moon-phase calculations.

The system provides:

* World time simulation
* Calendar and date management
* Time scaling and pause/resume
* Seasons and day phases
* Moon phases
* Calendar calculations
* Time formatting
* Time change signals
* Time persistence
* Data-driven time configuration

---

# 🧠 Time Architecture

```text
                      Time System

                           |
                       TimeData
                           |
                           ↓
                      TimeManager
                     (Public API)
                           |
          ┌────────────────┼────────────────┐
          ↓                ↓                ↓
 TimeCalendarHelper  TimeDayPhaseHelper  TimeMoonPhaseHelper
          |                |                |
      Calendar          Seasons /         Moon
      Calculations      Day Phases         Phases
                           |
                           ↓
                    Runtime Time State
```

`TimeData` defines the time configuration.

`TimeManager` provides the public API and owns runtime world time.

`TimeCalendarHelper` handles calendar calculations.

`TimeDayPhaseHelper` handles seasons and day phases.

`TimeMoonPhaseHelper` handles moon-phase calculations.

---

# 🔗 Time System Documentation

The following documents cover the Time System components.

| System                    | Purpose                                                                            | Documentation                                       |
| ------------------------- | ---------------------------------------------------------------------------------- | --------------------------------------------------- |
| ⏰ Time Manager            | Public API and runtime authority for world time                                    | [Time Manager](time_manager.md)                     |
| 📄 Time Data              | Defines calendar structure, time settings, seasons, phases, and moon configuration | [Time Data](resource_time_data.md)                  |
| 📅 Time Calendar Helper   | Handles calendar conversion, month lengths, leap years, weeks, and total days      | [Time Calendar Helper](time_calendar_helper.md)     |
| 🌅 Time Day Phase Helper  | Calculates seasons and day phases from calendar time                               | [Time Day Phase Helper](time_day_phase_helper.md)   |
| 🌙 Time Moon Phase Helper | Calculates moon phase from world calendar time                                     | [Time Moon Phase Helper](time_moon_phase_helper.md) |

---

# 🔗 System Integration

The Time System integrates with:

* 🌍 World System
* 🌦️ Environment System
* 👤 NPC System
* 🎯 Quest System
* 🎉 Event System
* 💾 Save System
* 🖥️ UI System

The Time System owns world temporal state while integrated systems consume time state through the `TimeManager` public API and time signals.

---

# 📌 Design Rule

**`TimeData` defines the time configuration.**

**`TimeManager` owns runtime time and provides the public API.**

**`TimeCalendarHelper` handles calendar calculations.**

**`TimeDayPhaseHelper` handles seasons and day phases.**

**`TimeMoonPhaseHelper` handles moon phases.**

The Time System keeps configuration, runtime state, and temporal calculations separated while providing a single authoritative source of world time to the rest of the framework.
