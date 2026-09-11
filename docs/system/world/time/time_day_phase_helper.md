# Time Day Phase Helper

`TimeDayPhaseHelper` provides the seasonal and daily phase calculation layer for the Time System.

It evaluates the active season and day phase from `TimeData` configuration without maintaining runtime state.

## Responsibilities

* Calculate the active season from the current month.
* Calculate the active day phase from the current hour.
* Respect season and day-phase enable settings.
* Support configurable season start months.
* Support configurable day-phase start hours.
* Return `StringName` identifiers for calculated phases.

## Architecture

```text id="u9m3kx"
TimeDayPhaseHelper
├── Season Calculation
│   ├── Season Names
│   └── Season Start Months
│
└── Day Phase Calculation
    ├── Phase Names
    └── Phase Start Hours
```

## Season Calculation

`get_season()` determines the active season from the current calendar month.

The calculation evaluates configured season start months and selects the most recent season whose start month is less than or equal to the current month.

If the current month occurs before the first configured season start month, the final configured season from the previous year is used.

Season calculation requires:

```text id="p3r8va"
seasons_enabled
season_names
season_start_months
```

The number of evaluated entries is limited to the smaller of the two configuration arrays.

## Day Phase Calculation

`get_day_phase()` determines the active day phase from the current hour.

Configured phase start hours are evaluated in order, with the active phase being the latest phase whose start hour has been reached.

Day-phase calculation requires:

```text id="x7n2qm"
day_phases_enabled
day_phase_names
day_phase_start_hours
```

The number of evaluated entries is limited to the smaller of the two configuration arrays.

## Data Dependency

```text id="j4c6tw"
TimeData
    │
    ├── Season Configuration
    └── Day Phase Configuration
           │
           ▼
    TimeDayPhaseHelper
           │
           ├── get_season()
           └── get_day_phase()
```

`TimeManager` supplies the current month and hour and stores the resulting runtime state.

## Design Principle

`TimeDayPhaseHelper` is a **stateless temporal phase calculation service**.

It owns the logic for translating calendar position into seasons and day phases while leaving configuration ownership and runtime state to `TimeData` and `TimeManager`.
