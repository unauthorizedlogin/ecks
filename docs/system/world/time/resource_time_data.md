# Time Data Resource

`TimeData` is the data resource that defines a complete world-time and calendar configuration.

It provides the static configuration consumed by `TimeManager` and its supporting calculation helpers.

## Responsibilities

* Define world-time simulation settings.
* Define clock structure.
* Define calendar structure.
* Define starting world time.
* Define year and leap-year rules.
* Define seasons.
* Define day phases.
* Define moon-phase configuration.
* Provide display metadata for time configurations.

## Identity

```text id="0l7v3w"
time_id
display_name
description
```

`time_id` uniquely identifies the time configuration used by `TimeDatabase` and `TimeManager`.

## Time Configuration

```text id="m8q1pk"
time_enabled
real_seconds_per_game_day
time_scale
```

Defines whether time simulation is enabled and controls the relationship between real-time and simulated world time.

`real_seconds_per_game_day` determines how many real-world seconds represent one complete game day.

`time_scale` controls the default simulation multiplier.

## Clock

```text id="e7x3na"
hours_per_day
minutes_per_hour
seconds_per_minute
```

Defines the structure of the in-game clock.

The clock is configurable rather than hard-coded to a 24/60/60 structure.

## Calendar

```text id="j5v2rc"
calendar_name
days_per_week
day_names
first_day_of_week
month_names
days_per_month
```

Defines the world calendar structure.

The resource supports custom:

* Calendar names
* Week lengths
* Day names
* Week starting day
* Month names
* Month lengths

This allows worlds to use calendar systems other than the default Gregorian-style configuration.

## Year Rules

```text id="p2k9fd"
use_leap_years
leap_year_interval
leap_year_offset
leap_year_extra_days
leap_year_extra_month
```

Defines optional leap-year behavior.

When enabled, the configured interval and offset determine eligible leap years. The additional days are inserted into the configured month.

## Starting Time

```text id="v6n4bx"
starting_year
starting_month
starting_day
starting_hour
starting_minute
starting_second
```

Defines the initial calendar position used when the time configuration is selected.

`TimeManager` converts this calendar position into its runtime elapsed-time representation.

## Seasons

```text id="r3c8mw"
seasons_enabled
season_names
season_start_months
```

Defines the world's seasonal calendar.

Each season corresponds to a starting month.

Season calculation is performed by `TimeDayPhaseHelper`.

## Day Phases

```text id="q9d5ks"
day_phases_enabled
day_phase_names
day_phase_start_hours
```

Defines named periods of the day and their starting hours.

Examples include:

```text
Night
Twilight
Dawn
Morning
Afternoon
Dusk
Evening
```

Day-phase calculation is delegated to `TimeDayPhaseHelper`.

## Moon Phases

```text id="t4w7hz"
moon_phases_enabled
lunar_cycle_length
moon_phase_names
```

Defines the lunar cycle and available moon phases.

`lunar_cycle_length` determines the length of the simulated lunar cycle in days.

Moon-phase calculation is delegated to `TimeMoonPhaseHandler`.

## Data Flow

```text id="k2m6qs"
TimeData
    │
    ├── TimeManager
    │     ├── Runtime Clock
    │     └── Calendar State
    │
    ├── TimeCalendarHelper
    │     └── Calendar Calculations
    │
    ├── TimeDayPhaseHelper
    │     ├── Seasons
    │     └── Day Phases
    │
    └── TimeMoonPhaseHandler
          └── Moon Phases
```

## Design Principle

`TimeData` contains **configuration, not runtime state**.

It defines how a world's calendar and clock behave, while `TimeManager` owns the active runtime position and simulation state.

This separation allows multiple time configurations to be created and selected without embedding calendar rules directly into the time manager.
