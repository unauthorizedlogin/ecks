# XP Tool Dock

## Overview

The XP Tool Dock is an editor-based progression design and balancing suite for the ecks Framework.

It provides tools for creating, visualizing, simulating, and analyzing player progression systems without requiring repeated gameplay testing.

The system is designed around data-driven XP curves, configurable growth formulas, and deterministic progression simulations.

---

# Purpose

The XP Tool Dock allows developers to design and balance progression systems directly inside the Godot editor.

It helps developers answer questions such as:

- How quickly should players level?
- How long should progression take?
- Are XP requirements scaling correctly?
- Does a progression curve remain balanced from early to end game?

---

# Core Features

## XP Curve Management

The XP system supports configurable level progression curves.

Developers can create and swap XP curve resources to test different progression models.

Supported workflows include:

- Custom level scaling
- Alternative progression paths
- Growth curve experimentation
- Maximum level tuning

---

## Growth & Formula Profiles

Progression calculations are separated into reusable resource profiles.

Formula profiles allow developers to swap progression behavior without rewriting systems.

This supports:

- Different XP scaling models
- Alternate progression philosophies
- Game mode specific progression
- Rapid balance iteration

---

## Shared Editor Context

The XP Tool Dock uses a centralized editor context as the single source of truth.

All XP tools operate from shared state including:

- Active XP curve
- Active formula profile
- Selected player data
- Simulation results
- Debug information

This keeps all editor panels synchronized and prevents conflicting progression data.

---

# XP Progression Simulator

## Overview

The XP Progression Simulator provides deterministic progression testing without requiring live gameplay.

It replays XP growth over simulated time steps and evaluates progression behavior.

---

## Simulation Features

The simulator can evaluate:

- XP gain rates
- Level progression timing
- Total progression duration
- Level milestones
- Maximum level reachability

---

## Simulation Results

Simulation output includes:

- Final level
- Total XP gained
- Levels gained
- XP timeline
- Level timeline
- Level-up events
- Estimated time to maximum level

---

# Curve Stress Testing

The XP Tool Dock includes automated curve stress testing.

Stress tests allow developers to quickly evaluate progression resources under controlled conditions.

Testing can identify:

- Curves that level too quickly
- Curves that stall progression
- Incorrect scaling behavior
- Late-game progression problems

---

# Editor Workflow

```

Create XP Curve
|
v
Assign Formula Profile
|
v
Run Simulation
|
v
Analyze Results
|
v
Adjust Growth Values
|
v
Validate Progression Balance

```

---

# Framework Integration

The XP Tool Dock integrates with the ecks Framework progression systems.

It works alongside:

- XP Manager
- Level Curve Resources
- Formula Profiles
- Player Level Components
- Progression data systems

---

# XP Curve & Growth Generators

## Overview

The ecks Framework includes dedicated editor generators for building progression resources from external data.

These tools allow developers to design progression values in spreadsheet-friendly formats and automatically convert them into framework resources.

The generator pipeline supports:

- XP curve creation
- Level growth data creation
- Resource generation
- Automatic index rebuilding

---

# XP Curve Generator

## Overview

The XP Curve Generator converts CSV-based progression data into framework-compatible XP curve resources.

This allows developers to design level progression externally while keeping runtime systems resource-driven.

---

## Workflow

```

XP Curve CSV
|
v
XP Curve Generator
|
v
LevelCurveResource
|
v
XP System Runtime Data

```

---

## Generated Data

The generator creates:

- Maximum level definitions
- Total XP requirements
- XP required between levels
- Runtime curve resources

---

## Automatic Index Generation

After creating the curve resource, the generator rebuilds the XP index.

This keeps progression lookups synchronized with the latest curve data.

---

# Level Growth Generator

## Overview

The Level Growth Generator builds character growth resources from CSV-defined progression data.

It allows developers to define how statistics scale throughout character progression without manually editing resources.

---

## Workflow

```

Growth CSV
|
v
Level Growth Generator
|
v
LevelGrowthData Resource
|
v
Stat Progression Systems

```

---

## Supported Workflow

Developers can define growth values externally and import them into the framework.

This supports:

- Class progression tuning
- Stat scaling
- Balance iteration
- Large-scale progression editing

---

# Data Driven Progression Pipeline

The XP tools are designed around a data-first workflow.

Instead of hardcoding progression values, developers can:

1. Design progression tables
2. Import data through generators
3. Produce framework resources
4. Test through simulations
5. Adjust and regenerate

---

# Creator Tool Philosophy

The XP creation pipeline separates:

## Authoring Data

External editable sources:

- CSV files
- Formula profiles
- Curve definitions

---

## Runtime Resources

Generated framework assets:

- XP curves
- Growth resources
- Indexed progression data

---

This allows progression systems to remain flexible, scalable, and easy to rebalance throughout development.
```
