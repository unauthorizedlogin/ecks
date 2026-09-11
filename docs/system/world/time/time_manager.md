# Time Manager

The `TimeManager` provides the public API and runtime orchestration layer for the world time system.

It maintains simulated world time, exposes calendar and phase state, advances time, manages time scaling, broadcasts time changes, and provides temporal state to other world systems.

Calendar calculations are delegated to `TimeCalendarHelper`, while season and day-phase calculations are delegated to `TimeDayPhaseHelper`. Moon-phase calculation is delegated to `TimeMoonPhaseHandler`.

## Responsibilities

* Maintain runtime world time.
* Select and manage the active `TimeData` configuration.
* Advance simulated time.
* Convert between elapsed time and calendar time.
* Expose calendar state.
* Calculate derived calendar state.
* Manage time scale and pause/resume.
* Broadcast time changes.
* Provide season, day-phase, and moon-phase state.
* Provide formatted date/time values.
* Provide calendar and age calculations.
* Serialize and deserialize runtime time state.

## Architecture

```text
TimeManager
├── Time Configuration
│   ├── TimeDatabase
│   └── TimeData
│
├── Calendar
│   └── TimeCalendarHelper
│       ├── Calendar Conversion
│       ├── Month Lengths
│       ├── Leap Years
│       ├── Week State
│       └── Total Days
│
├── Time Phases
│   ├── TimeDayPhaseHelper
│   │   ├── Seasons
│   │   └── Day Phases
│   │
│   └── TimeMoonPhaseHandler
│       └── Moon Phases
│
└── Runtime State
    ├── Elapsed Time
    ├── Calendar State
    ├── Time Scale
    └── Simulation State
```

## Public API

### Time Control

* `advance_time(seconds)` — advances simulated time.
* `skip_time(seconds)` — skips forward by a specified duration.
* `skip_days(days)` — skips forward by whole days.
* `set_time_scale(scale)` — changes simulation speed.
* `pause_time()` — sets time scale to zero.
* `resume_time()` — restores the configured time scale.
* `is_paused()` — returns whether time is currently paused.

### Calendar Control

* `set_calendar_time(year, month, day, hour, minute, second)` — sets world time using calendar values.
* `set_elapsed_time(seconds)` — sets world time using elapsed simulation time.
* `get_days_in_current_month()`
* `get_days_in_month(month, year)`
* `get_days_in_current_year()`
* `get_total_days()`
* `get_age_from_origin(origin_day)`

### Calendar Information

* `get_month_name()`
* `get_day_name()`
* `get_season()`
* `get_day_phase()`
* `get_moon_phase()`

### Formatting

* `get_time_string()`
* `get_date_string()`
* `get_datetime_string()`

## Time Simulation

When simulation is active, `_process()` advances world time based on:

* Calendar seconds per game day.
* Configured real seconds per game day.
* Current time scale.

Frame delta is capped before advancing simulated time to prevent large frame delays from producing excessive time jumps.

Time advancement is represented internally by `elapsed_time`.

The calendar state is recalculated from elapsed time through `TimeCalendarHelper`.

## Calendar State

Runtime calendar state includes:

```text
year
month
day
hour
minute
second
week
day_of_week
total_days
```

Derived state includes:

```text
season
day_phase
moon_phase
```

`TimeManager` maintains these values as runtime state while delegating their calculations to specialized helpers.

## Time Configuration

`set_time()` selects a `TimeData` resource through `TimeDatabase`.

The selected configuration provides:

* Starting calendar date/time.
* Calendar structure.
* Month names and lengths.
* Day names.
* Season configuration.
* Day-phase configuration.
* Moon-phase configuration.
* Time simulation settings.
* Default time scale.

The default configuration is:

```text
default
```

## Time Signals

```text
time_changed
second_changed(second)
minute_changed(minute)
hour_changed(hour)
day_changed(day)
week_changed(week)
month_changed(month)
season_changed(season)
year_changed(year)
day_phase_changed(phase)
moon_phase_changed(phase)
```

`TimeManager` captures the previous runtime state before changes and only emits granular signals for values that actually changed.

`time_changed` is emitted after change processing.

## Derived State

After calendar changes, `TimeManager` updates:

1. Total days
2. Week/day-of-week
3. Season
4. Day phase
5. Moon phase

This keeps all derived temporal state synchronized with the current calendar position.

## Time Configuration Validation

`_validate_time_data()` verifies the integrity of the selected `TimeData` configuration, including:

* Month names and month lengths.
* Day names and days per week.
* Season names and season start months.
* Day-phase names and start hours.
* Real-time/game-day conversion values.

## Persistence

`serialize_time()` stores:

* Active time configuration ID.
* Current elapsed simulation time.

```text
{
    "time_id": "...",
    "elapsed_time": ...
}
```

`deserialize_time()` restores the appropriate `TimeData` configuration and elapsed world time.

Calendar state is reconstructed from elapsed time rather than independently serialized.

## Debug Controls

`_unhandled_input()` provides debug time skipping:

```text
debug_time_day
debug_time_month
```

These invoke `skip_days()` for rapid calendar testing.

## External Integration

`TimeManager` acts as the temporal state provider for other world systems.

Systems such as Environment, NPCs, quests, events, and other time-dependent features can subscribe to the granular signals rather than implementing their own time tracking.

## Design Principle

`TimeManager` is the **centralized public API and runtime authority for world time**.

It owns runtime temporal state and simulation while delegating calendar, phase, and moon calculations to specialized components.

It does not own calendar configuration, UI, persistence systems, or the behavior of systems that consume time state.
