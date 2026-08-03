# 🔑 Requirements Manager

## Overview

The **Requirements Manager** is the central authority for validating gameplay requirements throughout the framework.

It provides a single validation pipeline used by multiple gameplay systems, ensuring that items, quests, and abilities all evaluate progression rules consistently.

Rather than allowing each system to implement its own requirement checks, validation is centralized into one manager responsible for determining whether an action is permitted and communicating any failure to the player.

This includes:

* 🛡 Equipment requirements
* 📜 Quest requirements
* ⚡ Ability requirements
* 👤 Class restrictions
* 📈 Level requirements
* 📊 Stat requirements
* 🎒 Equipment requirements
* ✅ Quest progression requirements
* 🗺️ Location requirements

---

# 🧠 Core Responsibilities

The Requirements Manager provides:

* Requirement validation
* Progression rule enforcement
* Requirement failure reporting
* Player requirement context
* Shared validation logic
* Gameplay access control

Every gameplay system uses the same validation philosophy regardless of what is being unlocked or activated.

---

# ⚙️ Initialization

```gdscript
func initialize()
```

Registers the Requirements Manager as an active gameplay system.

Prevents duplicate initialization.

Output:

```text
🔑 RequirementsManager initialized
```

---

# 👤 Player Context

The manager maintains a reference to the active player.

```gdscript
set_player()
```

This allows validation routines to evaluate:

* Character class
* Player level
* Character statistics
* Equipment
* Progression

without requiring each caller to repeatedly supply player data.

---

# 🔍 Validation Pipeline

All validation follows the same high-level process.

```text
Gameplay Request
        ↓
Load Requirement List
        ↓
Evaluate Each Requirement
        ↓
Collect Failures
        ↓
Return Success / Failure
```

Validation never stops at the first failure.

Instead, every unmet requirement is evaluated so complete feedback can be presented to the player.

---

# 🛡 Item Requirements

The manager validates whether an item can be equipped.

Supported requirement types include:

* Class
* Level
* Stat
* Quest completion
* Equipped item

If validation fails, a descriptive message is sent to the Event Manager explaining every unmet requirement.

---

# 📜 Quest Requirements

Quest validation determines whether a quest may begin.

Supported requirements include:

* Class
* Level
* Stat
* Quest completion
* Equipped item
* Current location

This allows quest progression to depend on both player progression and world state.

---

# ⚡ Ability Requirements

Ability validation determines whether an ability may be used.

Supported requirements include:

* Class
* Level
* Stat
* Quest completion
* Equipped item

These checks occur before an ability is allowed to enter the execution pipeline.

---

# 📋 Supported Requirement Types

Across all supported systems, the Requirements Manager currently validates:

* 👤 Class
* 📈 Level
* 📊 Stat values
* 📜 Quest completion
* 🎒 Equipped items
* 🗺️ Current location

Additional requirement categories can be introduced without changing the gameplay systems that consume them.

---

# 📣 Failure Reporting

When validation fails, the manager automatically generates user-facing feedback.

Failure messages are routed through the Event Manager using the appropriate gameplay channel.

Examples include:

* Cannot Equip
* Cannot Start Quest
* Cannot Cast

Each message identifies the specific requirement that was not satisfied, allowing players to understand exactly why an action was denied.

---

# 🎨 Requirement Presentation

Requirement failures are formatted using the framework's colorized event system.

Displayed information includes:

* Requirement category
* Expected value
* Related game object

Examples:

```text
Cannot Equip
Requires Level: 20
```

```text
Cannot Start
Requires Quest: Into the Woods
```

```text
Cannot Cast
Requires Intelligence: 50
```

This keeps requirement feedback consistent across every gameplay system.

---

# 🔗 System Relationships

```text
              Player
                 |
                 ↓
       Requirements Manager
                 |
      ┌──────────┼──────────┐
      ↓          ↓          ↓
    Items      Quests    Abilities
      |          |          |
      └──────────┼──────────┘
                 ↓
        Validation Result
                 |
                 ↓
          Event Manager
```

---

# 🧩 Integration

The Requirements Manager acts as a shared service for progression-based systems.

It integrates with:

* Player Manager
* Quest Manager
* Equipment Manager
* World Manager
* Event Manager
* Class Database
* Item Database
* Quest Database

Rather than owning progression data itself, it queries these systems to determine whether requirements have been satisfied.

---

# ✅ Design Rule

**RequirementsManager is the single authority for gameplay requirement validation.**

Items, quests, abilities, and future progression systems should never implement their own requirement logic independently.

All gameplay restrictions should be represented as requirement resources and validated through the Requirements Manager, ensuring consistent behavior, centralized progression rules, and standardized player feedback across the framework.
