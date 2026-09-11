# 🏷️ Environment Resource Generator System

The Environment Resource Generator System provides a data-driven pipeline for creating and managing environment definitions.

Environments are authored through CSV data and automatically converted into `EnvironmentResource` `.tres` resources, allowing environmental conditions, eligibility rules, weather behavior, temperature ranges, presentation data, and gradients to be defined without manually creating resources.

The system is responsible for:

* environment identity and metadata
* environment presentation data
* day and night icon references
* music references
* category and subcategory routing
* seasonal eligibility
* day-phase eligibility
* moon-phase eligibility
* environment selection weighting
* meteorological condition assignment
* seasonal temperature ranges
* color gradient generation
* generated environment indexing

---

## 🧱 Environment Resource Architecture

Each generated environment is represented by an `EnvironmentResource`.

The generator populates the environment resource from CSV data and additionally generates any environment-specific gradient resource.

Environment resources contain:

* Environment identity
* Display information
* Description
* Day icon
* Night icon
* Music reference
* Database folder metadata
* Seasonal eligibility
* Day-phase eligibility
* Moon-phase eligibility
* Selection weight
* Meteorological conditions
* Temperature ranges
* Color gradient reference

The resulting environment definition is fully data-driven.

---

## 🌦️ Environment Eligibility

Environment definitions can specify the world conditions under which they are eligible for selection.

Supported eligibility dimensions include:

* Seasons
* Day phases
* Moon phases
* Selection weight

Example:

```text
Environment:
Temperate Forest

Seasons:
spring;summer;autumn

Day Phases:
morning;day;evening

Moon Phases:
new;waxing;full;waning

Weight:
1.0
```

Eligibility values are normalized to lowercase during generation, allowing consistent matching by runtime environment-selection systems.

---

## ⚖️ Environment Selection Weight

Each environment can define a selection weight.

The generator:

* defaults unspecified weights to `1.0`
* accepts floating-point values
* clamps negative weights to `0`
* reports invalid weight values as errors

Weight provides the environment system with a data-driven mechanism for controlling relative selection frequency.

Example:

```text
Clear Forest
Weight: 5.0

Storm Forest
Weight: 1.0
```

This allows clear conditions to be selected more frequently than storm conditions without hardcoding selection probabilities.

---

## 🌡️ Temperature Range Configuration

Environments can define seasonal temperature ranges through structured CSV data.

Format:

```text
SEASON:MINIMUM:MAXIMUM
```

Multiple ranges can be supplied using semicolon-separated entries.

Example:

```text
spring:10:24;
summer:20:35;
autumn:8:22;
winter:-5:12
```

The generator converts each entry into a `TemperatureRangeResource`.

Generated structure:

```text
EnvironmentResource
 |
 └── Temperature Ranges
      ├── Spring
      │    ├── Minimum
      │    └── Maximum
      ├── Summer
      │    ├── Minimum
      │    └── Maximum
      └── ...
```

Temperature ranges are validated during generation.

The generator rejects:

* malformed range definitions
* missing seasons
* invalid numeric values
* minimum values greater than maximum values

The resulting ranges provide seasonal environmental temperature boundaries for runtime systems.

---

## 🌧️ Meteorological Configuration

Environment resources support multiple meteorological conditions.

Meteorological values are provided as a semicolon-separated CSV list and resolved against the `EnvironmentResource.Meteo` enumeration.

Example:

```text
meteo_list:

CLEAR;CLOUDY;RAIN
```

Generated:

```text
Meteo List
[
    CLEAR,
    CLOUDY,
    RAIN
]
```

Invalid meteorological values generate errors and are excluded from the generated list.

This allows an environment to define which weather conditions are valid for that environment without embedding those relationships in runtime code.

---

## 🎨 Environment Color Gradients

The generator supports environment-specific color gradients directly from CSV data.

A gradient definition consists of:

* Gradient ID
* Gradient offsets
* Gradient colors

Example:

```text
Gradient ID:
forest_day

Offsets:
0.0;0.5;1.0

Colors:
0.2,0.4,0.2,1;
0.4,0.6,0.3,1;
0.8,0.7,0.4,1
```

The generator creates:

```text
Gradient
 |
 ├── Offsets
 └── Colors
       |
       ↓
GradientTexture1D
```

The resulting `GradientTexture1D` is saved as its own `.tres` resource.

The environment then receives the generated gradient resource path.

---

## 🧪 Gradient Validation

Gradient definitions are validated before resources are created.

The generator verifies:

* Gradient ID exists when gradient data is supplied
* Gradient offsets exist
* Gradient colors exist
* Offset values are numeric
* Offset values remain within `0.0–1.0`
* Color values contain RGBA components
* Color channels remain within `0.0–1.0`
* Offset and color counts match
* At least one gradient stop exists

Invalid gradient data prevents that gradient from being generated and prevents the environment from receiving an invalid gradient reference.

This establishes the gradient generator as part of the environment data validation boundary rather than treating gradients as arbitrary presentation data.

---

## 🖼️ Environment Presentation

Environment resources can define presentation assets independently from environmental behavior.

Supported presentation data includes:

* Day icon
* Night icon
* Music reference
* Color gradient

Day and night icons are loaded from resource paths supplied by the CSV.

If an icon path cannot be loaded, the generator reports a warning while continuing environment generation.

This allows environment selection and environmental presentation to remain part of the same data definition without coupling asset loading to runtime environment logic.

---

## 🗂️ Environment Database Routing

Generated environments are organized using folder and subfolder metadata.

Example:

```text
data/databases/environments/

├── forest/
│   ├── temperate/
│   │   ├── forest_day.tres
│   │   └── forest_night.tres
│
├── desert/
│   ├── hot/
│
├── tundra/
│   ├── arctic/
│
├── misc/
│   ├── general/
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

## 🆔 Environment Identity

Every generated environment requires a unique `environment_id`.

The generator validates that the ID is present before continuing.

The ID is also assigned as the resource name:

```text
environment_id
      ↓
resource_name
```

The environment ID becomes the filename for the generated resource:

```text
environment_id.tres
```

This establishes a consistent identity across CSV definitions, generated resources, database organization, and runtime lookup.

---

## ⚙️ Generation Pipeline

The generation process:

1. Reads the environment CSV
2. Parses CSV headers and values
3. Creates an `EnvironmentResource`
4. Validates the environment ID
5. Applies identity and presentation data
6. Loads day and night icons
7. Applies eligibility configuration
8. Applies meteorological conditions
9. Generates seasonal temperature ranges
10. Determines database routing
11. Generates the environment color gradient
12. Assigns the generated gradient reference
13. Saves the environment `.tres` resource
14. Rebuilds the environment index

The gradient is therefore generated as part of the environment resource pipeline rather than requiring a separate manual resource-authoring step.

---

## 🔎 Generated Environment Index

After environment generation completes, the generator invokes the Environment Index Generator.

The index provides the runtime environment database with deterministic discovery of generated environment resources.

The complete pipeline is:

```text
Environment CSV
 |
 ↓
Environment Resource Generator
 |
 ├── EnvironmentResource
 │
 └── GradientTexture1D
 |
 ↓
Environment Database
 |
 ↓
Environment Index
 |
 ↓
Runtime Environment System
```

The index separates runtime resource discovery from filesystem scanning and allows the environment database to expand without modifying runtime loading logic.

---

## 🔗 Environment System Integration

The Environment Resource Generator defines environmental data.

It does not determine which environment is currently active or manage environmental runtime state.

Responsibilities are separated:

```text
CSV
 |
 ↓
Environment Resource Generator
 |
 ↓
EnvironmentResource
 |
 ├── Eligibility
 ├── Meteo
 ├── Temperature
 └── Presentation
 |
 ↓
Environment Database
 |
 ↓
Environment Runtime System
 |
 ├── Selection
 ├── World Conditions
 ├── Weather
 ├── Temperature
 └── Presentation
```

The generator establishes the available environment definitions and their configuration.

Runtime systems determine how those definitions are selected and applied to the world.

---

## 🧩 Data-Driven Environmental Architecture

The generator allows environmental behavior to be expanded through data rather than new resource scripts.

A single environment definition can describe:

```text
Identity
   +
Eligibility
   +
Weather Conditions
   +
Temperature Rules
   +
Presentation
   +
Color Gradient
```

This provides a common data layer for environmental systems while keeping runtime responsibilities outside the generator.

For example, a new seasonal environment can be introduced by adding a CSV definition containing its:

* eligibility conditions
* weather possibilities
* temperature ranges
* presentation assets
* gradient configuration
* selection weight

No new environment resource class is required.

---

## ⚠️ Generator Scope

The generator creates and configures environment resources.

It does **not**:

* select the active environment
* calculate current world weather
* calculate current world temperature
* manage seasons
* manage day/night progression
* manage moon phases
* apply environmental effects
* control environmental presentation at runtime

Those responsibilities belong to the corresponding World and Environment runtime systems.

---

## ✅ System Responsibilities

The Environment Resource Generator System:

✅ Creates environment resources
✅ Converts CSV data into `EnvironmentResource` definitions
✅ Validates environment identity
✅ Configures environmental eligibility
✅ Configures selection weighting
✅ Assigns meteorological conditions
✅ Generates seasonal temperature ranges
✅ Generates environment color gradients
✅ Loads day/night presentation resources
✅ Routes environments into database categories
✅ Generates gradient resources
✅ Rebuilds the environment index

The Environment Resource Generator System does **not**:

❌ Select active environments
❌ Manage world environmental state
❌ Calculate weather
❌ Calculate runtime temperature
❌ Manage seasonal progression
❌ Manage day/night progression
❌ Apply environmental gameplay effects
❌ Control runtime environmental presentation

Those responsibilities belong to:

* Environment Database System
* Environment Manager/System
* World Time System
* Weather System
* Temperature System
* World Presentation Systems
