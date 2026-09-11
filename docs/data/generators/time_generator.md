# 🏷️ Time Resource Generator

The Time Resource Generator System provides a data-driven pipeline for creating and validating world time configurations.

Time systems are authored through CSV data and automatically converted into `TimeData` `.tres` resources, allowing calendars, time progression, seasons, day phases, moon phases, and world starting dates to be configured without manually creating resource definitions.

The system is responsible for:

* time resource generation
* calendar configuration
* calendar naming and structure
* day and month definitions
* leap-year configuration
* time progression configuration
* world starting date and time
* season configuration
* day-phase configuration
* moon-phase configuration
* time-system validation
* database category routing
* generated time indexing

---

## 🧱 Time Resource Architecture

Each generated time configuration is represented by a `TimeData` resource.

The resource defines the complete calendar and temporal ruleset used by a world.

Time resources contain:

* Time identity
* Display information
* Description
* Database folder metadata
* Calendar name
* Days per week
* Day names
* First day of week
* Month names
* Month lengths
* Leap-year rules
* Hours per day
* Minutes per hour
* Seconds per minute
* Time enablement
* Time scale
* Real-time duration of a game day
* Starting year
* Starting month
* Starting day
* Starting hour
* Starting minute
* Starting second
* Season definitions
* Day-phase definitions
* Moon-phase definitions
* Lunar cycle length

The generator establishes the temporal rules.

The Time System uses those rules to calculate and advance runtime world time.

---

## 📅 Calendar Configuration

Time resources define the calendar structure used by a world.

Supported calendar configuration includes:

* Calendar name
* Days per week
* Day names
* First day of the week
* Month names
* Days per month

Example:

```text
Calendar:
Gregorian

Days Per Week:
7

Day Names:
Monday;Tuesday;Wednesday;Thursday;Friday;Saturday;Sunday

Months:
January;February;March;...

Days Per Month:
31;28;31;30;...
```

This allows different worlds to use calendars with structures that do not have to conform to real-world calendar rules.

---

## 🗓️ Leap-Year Configuration

The generator supports configurable leap-year behavior.

Supported settings include:

* Leap years enabled
* Leap-year interval
* Leap-year offset
* Additional leap-year days
* Month receiving additional days

Example:

```text
Leap Years:
Enabled

Interval:
4

Offset:
0

Extra Days:
1

Extra Month:
2
```

The resource therefore contains the rules required by the calendar helper to determine leap-year behavior.

The generator defines the calendar rules.

The runtime time system performs the actual calendar calculations.

---

## ⏱️ Time Progression Configuration

Time resources define the fundamental units and runtime progression settings.

Supported values include:

* Hours per day
* Minutes per hour
* Seconds per minute
* Time enabled state
* Time scale
* Real seconds per game day

Example:

```text
Hours Per Day:
24

Minutes Per Hour:
60

Seconds Per Minute:
60

Time Enabled:
true

Time Scale:
1.0

Real Seconds Per Game Day:
1200
```

This allows worlds to use different temporal speeds while maintaining a consistent calendar architecture.

---

## 🕰️ World Starting Time

Each time resource defines the initial world calendar state.

Supported starting values:

* Year
* Month
* Day
* Hour
* Minute
* Second

Example:

```text
Starting Date:
Year: 1
Month: 1
Day: 1

Starting Time:
08:00:00
```

The starting date represents the configured temporal epoch for the world.

The generator validates the starting month and day against the generated calendar structure before saving the resource.

---

## 🌱 Season Configuration

Time resources can define custom seasonal structures.

Supported configuration includes:

* Seasons enabled state
* Season names
* Season starting months

Example:

```text
Seasons:
Enabled

Names:
spring;summer;autumn;winter

Starting Months:
3;6;9;12
```

The generator stores the season definitions as synchronized arrays.

The runtime calendar and season systems use these definitions to determine the active season from world time.

---

## 🌅 Day Phase Configuration

Time resources support custom day-phase definitions.

Supported configuration includes:

* Day phases enabled state
* Phase names
* Phase starting hours

Example:

```text
Day Phases:
Enabled

Names:
night;morning;day;evening

Starting Hours:
0;6;12;18
```

The generator stores the phase names and corresponding starting hours as synchronized arrays.

This allows worlds to define their own temporal presentation boundaries without hardcoding a fixed day-phase structure.

---

## 🌙 Moon Phase Configuration

Time resources can define lunar behavior independently from the calendar.

Supported configuration includes:

* Moon phases enabled state
* Lunar cycle length
* Moon phase names

Example:

```text
Moon Phases:
Enabled

Lunar Cycle:
29.5 days

Phases:
new;waxing;full;waning
```

The generator provides the configuration.

The Moon Phase Helper performs the runtime lunar calculations.

---

## 🧮 Time Data Validation

The generator performs structural validation before a `TimeData` resource is saved.

Validation includes:

* Days-per-week validity
* Day-name count
* Month-name availability
* Month-name/month-length count
* Month-length validity
* Hours-per-day validity
* Minutes-per-hour validity
* Seconds-per-minute validity
* Real-time game-day duration
* Season array synchronization
* Day-phase array synchronization
* Starting month validity
* Starting day validity

Invalid time definitions are rejected before resource generation completes.

This prevents malformed calendar configurations from entering the runtime database.

---

## 🔄 Array Synchronization

Several time properties operate as paired arrays.

Season configuration:

```text
season_names
        ↕
season_start_months
```

Day-phase configuration:

```text
day_phase_names
        ↕
day_phase_start_hours
```

Calendar configuration:

```text
month_names
        ↕
days_per_month
```

The generator validates these relationships before saving the resource.

This ensures that runtime helpers can treat the corresponding arrays as deterministic configuration pairs.

---

## 🧩 Data Parsing

The generator converts CSV values into strongly typed time resource properties.

Supported conversions include:

* Strings
* String arrays
* Integers
* Integer arrays
* Floating-point values
* Boolean values

Boolean fields accept:

```text
true
false
1
0
yes
no
```

List-based values use semicolon-separated definitions.

Example:

```text
spring;summer;autumn;winter
```

Numeric arrays use the same delimiter:

```text
31;28;31;30
```

Malformed numeric and boolean values generate errors rather than silently producing invalid resource data.

---

## 🗂️ Time Database Routing

Generated time resources are organized using folder and subfolder metadata.

Example:

```text
data/databases/time/

├── worlds/
│   ├── standard/
│   │   └── standard_time.tres
│
├── fantasy/
│   ├── custom_calendar/
│   │   └── fantasy_time.tres
│
├── misc/
│   └── general/
```

If no folder is specified, the generator uses:

```text
misc
```

If no subfolder is specified, it uses:

```text
general
```

Directories are created automatically during generation.

---

## 🆔 Time Resource Identity

Every generated time configuration requires a `time_id`.

The generator validates that the ID exists before continuing.

The ID is assigned to the resource name:

```text
time_id
   ↓
resource_name
```

The same ID becomes the generated resource filename:

```text
time_id.tres
```

This provides a consistent identity between CSV definitions, generated resources, database organization, and runtime lookup.

---

## ⚙️ Generation Pipeline

The generation process:

1. Reads the Time CSV
2. Parses CSV headers and values
3. Creates a `TimeData` resource
4. Validates the `time_id`
5. Applies calendar configuration
6. Applies leap-year configuration
7. Applies time progression settings
8. Applies the world starting date and time
9. Applies season configuration
10. Applies day-phase configuration
11. Applies moon-phase configuration
12. Validates the complete time definition
13. Routes the resource into its database folder
14. Saves the `.tres` resource
15. Rebuilds the time index

---

## 🔎 Generated Time Index

After resource generation completes, the generator invokes the Time Index Generator.

The index provides deterministic discovery of generated time resources for runtime loading.

The complete pipeline is:

```text
Time CSV
 |
 ↓
Time Resource Generator
 |
 ↓
TimeData.tres
 |
 ↓
Time Index Generator
 |
 ↓
Time Index
 |
 ↓
Time Database
 |
 ↓
Time System
```

This keeps runtime resource discovery separate from filesystem scanning and allows additional time configurations to be added without changing runtime loading logic.

---

## 🔗 Time System Integration

The Time Resource Generator defines the ruleset used by the world time system.

It does not advance time or perform runtime calendar calculations.

Responsibilities are separated:

```text
CSV
 |
 ↓
Time Resource Generator
 |
 ↓
TimeData
 |
 ↓
Time Database
 |
 ↓
Time Manager
 |
 ├── Calendar Helper
 ├── Day Phase Helper
 └── Moon Phase Helper
```

The generated resource supplies the configuration.

The runtime Time Manager orchestrates simulated time.

The helper systems perform specialized temporal calculations.

---

## 🧠 Temporal System Architecture

The generated `TimeData` resource acts as the configuration foundation for multiple temporal systems.

```text
TimeData
 |
 ├── Calendar Rules
 │    ├── Days
 │    ├── Months
 │    └── Leap Years
 │
 ├── Time Progression
 │    ├── Clock
 │    ├── Scale
 │    └── Game-Day Duration
 │
 ├── Seasons
 │
 ├── Day Phases
 │
 └── Moon Phases
```

This allows the world time system to remain configurable while the runtime architecture remains shared across worlds.

---

## ⚠️ Generator Scope

The Time Resource Generator creates and validates time configuration resources.

It does **not**:

❌ Advance world time
❌ Maintain runtime clock state
❌ Calculate the current calendar date
❌ Determine the active season at runtime
❌ Determine the current day phase
❌ Calculate lunar phase state
❌ Manage time scaling during gameplay
❌ Broadcast time changes

Those responsibilities belong to:

* Time Database System
* Time Manager System
* Time Calendar Helper
* Time Day Phase Helper
* Time Moon Phase Helper

---

## ✅ System Responsibilities

The Time Resource Generator System:

✅ Creates `TimeData` resources
✅ Converts CSV data into time definitions
✅ Configures custom calendar structures
✅ Configures leap-year rules
✅ Configures time progression
✅ Configures world starting dates
✅ Configures seasons
✅ Configures day phases
✅ Configures moon phases
✅ Validates calendar structure
✅ Validates temporal configuration
✅ Validates synchronized configuration arrays
✅ Routes time resources into database categories
✅ Generates the runtime time index

The Time Resource Generator System does **not**:

❌ Manage runtime world time
❌ Advance the clock
❌ Perform calendar calculations
❌ Calculate seasons
❌ Calculate day phases
❌ Calculate moon phases
❌ Manage time-change events

Those responsibilities belong to:

* **Time Database System** — Resource discovery and runtime access
* **Time Manager System** — Runtime time orchestration and state
* **Time Calendar Helper** — Calendar calculations
* **Time Day Phase Helper** — Day-phase calculations
* **Time Moon Phase Helper** — Lunar calculations
