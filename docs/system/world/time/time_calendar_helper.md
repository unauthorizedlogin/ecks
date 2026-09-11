# Time Calendar Helper

`TimeCalendarHelper` provides the calendar calculation layer for the Time System.

It operates entirely from `TimeData` configuration and handles calendar arithmetic, elapsed-time conversion, week calculations, and day-index calculations without owning runtime time state.

## Responsibilities

* Calculate seconds per game day.
* Calculate days per year.
* Calculate days per month.
* Evaluate leap-year rules.
* Convert calendar dates to elapsed time.
* Convert elapsed time to calendar dates.
* Calculate total days before a date.
* Calculate week and day-of-week state.
* Calculate age from day indices.
* Convert day indices back into calendar dates.

## Architecture

```text
TimeCalendarHelper
├── Clock Calculations
│   └── Seconds Per Day
│
├── Calendar Calculations
│   ├── Days Per Month
│   ├── Days Per Year
│   ├── Leap Years
│   └── Total Day Index
│
├── Time Conversion
│   ├── Calendar → Elapsed Time
│   └── Elapsed Time → Calendar
│
├── Week Calculations
│   ├── Week Number
│   └── Day Of Week
│
└── Day Index Calculations
    ├── Age
    └── Day Index → Calendar
```

## Clock Calculations

`get_seconds_per_day()` calculates the number of simulated seconds in a complete game day from the active `TimeData` clock configuration.

```text
hours_per_day
× minutes_per_hour
× seconds_per_minute
```

## Calendar Calculations

### Days Per Month

`get_days_in_month()` retrieves the configured month length and applies leap-year adjustments when the target month matches `leap_year_extra_month`.

### Days Per Year

`get_days_in_year()` totals all configured month lengths and adds leap-year days when applicable.

### Leap Years

`is_leap_year()` evaluates the configured leap-year rules:

```text
use_leap_years
leap_year_interval
leap_year_offset
```

The calculation uses modular year intervals, allowing custom calendar leap-year systems.

## Day Index

`get_total_days_before_date()` converts a calendar position into a zero-based day index relative to the configured `TimeData` starting date.

This provides a common day-based reference for:

* Week calculations
* Moon calculations
* Age calculations
* Other systems requiring elapsed calendar days

## Time Conversion

### Calendar → Elapsed

`calendar_to_elapsed_time()` converts a calendar date/time into elapsed simulated seconds from the `TimeData` starting date.

The calculation accounts for:

* Complete years
* Complete months
* Days
* Hours
* Minutes
* Seconds

### Elapsed → Calendar

`elapsed_to_calendar()` treats the `TimeData` starting date as the calendar epoch and converts elapsed seconds back into:

```text
year
month
day
hour
minute
second
```

This makes elapsed time the canonical runtime representation while preserving configurable calendar rules.

## Week State

`get_week_state()` derives:

```text
week
day_of_week
```

from the total day index and `TimeData.first_day_of_week`.

The calculation supports configurable week lengths through `days_per_week`.

## Age Calculation

`get_age_between_day_indices()` calculates an elapsed calendar duration between two day indices and returns:

```text
{
    "years": ...,
    "months": ...,
    "days": ...
}
```

Years and months are consumed according to the active calendar's configured lengths rather than fixed Gregorian assumptions.

## Day Index Conversion

`_day_index_to_calendar()` converts a day index back into a calendar date.

This provides an internal utility for systems that work with absolute day indices while still respecting:

* Variable month lengths
* Leap years
* Custom starting dates
* Custom calendar structures

## Data Dependency

`TimeCalendarHelper` does not maintain its own calendar configuration.

All calendar rules are read from the supplied `TimeData` resource:

```text
TimeData
    │
    ▼
TimeCalendarHelper
    │
    ├── Calendar Arithmetic
    ├── Time Conversion
    ├── Week State
    └── Day Index Calculations
```

## Design Principle

`TimeCalendarHelper` is a **stateless calendar calculation service**.

`TimeManager` owns runtime world time and exposes the public API, while `TimeCalendarHelper` performs the underlying calendar mathematics using the active `TimeData` configuration.
