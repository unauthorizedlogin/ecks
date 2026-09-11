# Time Moon Phase Handler

`TimeMoonPhaseHandler` provides the lunar phase calculation layer for the Time System.

It derives the current lunar day and moon phase from the world's accumulated calendar day count and the lunar cycle configuration defined by `TimeData`.

## Responsibilities

* Calculate the current lunar day.
* Calculate the active moon phase.
* Support configurable lunar cycle lengths.
* Support configurable moon phase counts.
* Provide moon phase state to `TimeManager`.

## Architecture

```text
TimeData
├── lunar_cycle_length
└── moon_phase_names
        │
        ▼
TimeMoonPhaseHandler
├── get_lunar_day()
└── get_moon_phase()
        │
        ▼
   TimeManager
```

## Lunar Day

`get_lunar_day()` calculates the current position within the configured lunar cycle.

```text
lunar_day = total_days % lunar_cycle_length
```

The result is a floating-point position within the current lunar cycle.

## Moon Phase

`get_moon_phase()` converts the lunar-day position into a configured moon phase.

The lunar cycle is divided evenly across the available entries in `moon_phase_names`.

```text
phase_index =
floor(
    lunar_day / lunar_cycle_length
    × phase_count
)
```

The resulting index is wrapped to the available phase range.

This allows the number of moon phases to be defined by the `TimeData` resource rather than hard-coded.

## Configuration

Moon phase behavior is controlled by:

```text
lunar_cycle_length
moon_phase_names
moon_phases_enabled
```

`TimeManager` determines whether moon phases are enabled through `TimeData.moon_phases_enabled` before requesting a phase calculation.

## Data Dependency

`TimeMoonPhaseHandler` does not own calendar or runtime state.

```text
TimeManager
    │
    ├── total_days
    │
    ▼
TimeMoonPhaseHandler
    │
    └── TimeData
          ├── Lunar Cycle
          └── Moon Phases
```

## Design Principle

`TimeMoonPhaseHandler` is a **stateless lunar calculation service**.

It translates the world's accumulated calendar days into a position within the configured lunar cycle and resolves that position to a named moon phase.

It does not advance time, store runtime state, control visuals or lighting, or apply gameplay effects.
