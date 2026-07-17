# ⚔️ Difficulty System Architecture

## 🧠 Overview

The Difficulty System provides a data-driven framework for defining global gameplay modifiers that scale player challenge, enemy power, rewards, economy, progression, and world behavior.

Difficulty is designed as a configuration layer above existing gameplay systems.

Instead of hardcoding difficulty rules inside combat, enemies, rewards, or economy managers, each difficulty definition provides:

* player modifiers
* enemy modifiers
* reward scaling
* economy adjustments
* death penalties
* world spawn scaling
* boss scaling
* progression modifiers

Difficulty definitions are generated from CSV data into runtime-ready `.tres` resources for deterministic loading.

Architecture:

```
Difficulty CSV
      ↓
Difficulty Resource Generator
      ↓
DifficultyDefinition (.tres)
      ↓
Difficulty Index Generator
      ↓
Runtime Difficulty Database
      ↓
Gameplay Systems
```

---

# 📦 DifficultyDefinition Resource

`DifficultyDefinition` is the primary runtime data object representing a selectable difficulty.

Each difficulty contains metadata, gameplay scaling rules, and references to reusable effects.

Location:

```
system/entity/difficulty/resource_difficulty.gd
```

---

## Core Identity

Each difficulty contains:

| Field            | Purpose                        |
| ---------------- | ------------------------------ |
| difficulty_id    | Unique runtime identifier      |
| display_name     | Player-facing difficulty name  |
| description_text | Difficulty description         |
| category         | Resource organization category |
| subcategory      | Resource organization subgroup |
| sort_order       | UI ordering                    |

---

# 🎯 Effect System Integration

Difficulty directly integrates with the existing Effect System through `StatEffect` resources.

Difficulty does not contain custom stat modification logic.

Instead, it references reusable effects:

```gdscript
@export var player_effects: Array[StatEffect] = []

@export var enemy_effects: Array[StatEffect] = []
```

This allows difficulty modifiers to use the same effect pipeline as other gameplay systems.

Examples:

```
Nightmare Difficulty

Player Effects:
- Reduced Resistances
- Reduced Healing

Enemy Effects:
- Increased Damage
- Increased Health
- Increased Speed
```

The Difficulty System is responsible for selecting and applying difficulty-specific effects.

The Effect System remains responsible for:

* effect definitions
* stat modification behavior
* temporary effects
* effect processing rules
* runtime application

See:

- [Effect System Architecture → StatEffect Pipeline](.docs/system/entity/effect_system.md)

---

# 💰 Reward Scaling

Difficulty can globally modify reward output.

Supported modifiers:

```gdscript
xp_multiplier
gold_multiplier
drop_rate_multiplier
magic_find_multiplier
```

Example:

```
Normal:
XP 1.0x
Gold 1.0x

Hard:
XP 1.25x
Gold 1.15x

Nightmare:
XP 2.0x
Gold 1.5x
```

Reward systems consume these values when calculating final rewards.

---

# 🏪 Economy Scaling

Difficulty can modify economic pressure.

Supported values:

```gdscript
vendor_price_multiplier
repair_cost_multiplier
```

Examples:

```
Hard:
Vendor Prices +10%

Nightmare:
Repair Costs +50%
```

Economy systems remain independent and only consume difficulty modifiers.

---

# 💀 Death Penalties

Difficulty controls additional death consequences.

Supported values:

```gdscript
death_xp_loss_percent
death_gold_loss_percent
```

Examples:

```
Normal:
0% XP loss

Hard:
5% XP loss

Nightmare:
10% XP loss
```

---

# 🌍 World Scaling

Difficulty modifies world generation and encounter density.

Supported values:

```gdscript
enemy_density_multiplier
elite_spawn_multiplier
champion_spawn_multiplier
```

Examples:

```
Hard:
Enemy Density 1.25x
Elite Spawns 1.5x

Nightmare:
Enemy Density 2x
Champion Spawns 2x
```

These values are consumed by spawning systems.

---

# 👑 Boss Scaling

Boss encounters have dedicated difficulty modifiers.

Supported values:

```gdscript
boss_hp_multiplier
boss_damage_multiplier
```

Example:

```
Boss Scaling:

Normal:
HP 1.0x
Damage 1.0x

Nightmare:
HP 3.0x
Damage 2.0x
```

---

# 📈 Progression Scaling

Difficulty can modify progression rewards.

Supported values:

```gdscript
quest_reward_multiplier
reputation_multiplier
```

Used by:

* quest rewards
* reputation systems
* progression systems

---

# 🛠️ Difficulty Resource Generator

Location:

```
system/entity/difficulty/tools/difficulty_resource_generator.gd
```

Responsibilities:

* Read difficulty CSV data
* Validate required fields
* Generate DifficultyDefinition resources
* Resolve StatEffect references
* Route resources into category folders
* Save `.tres` resources
* Trigger index generation

Does NOT:

* apply difficulty rules
* modify gameplay directly
* manage runtime difficulty state

---

# 📄 Difficulty CSV Pipeline

Difficulty data is authored through:

```
difficulty_strings.csv
```

Example:

```
difficulty_id,
display_name,
player_effects,
enemy_effects,
xp_multiplier,
enemy_density_multiplier,
boss_hp_multiplier
```

Effect references use IDs:

```
player_effects:
player_resistance_penalty;player_healing_penalty

enemy_effects:
enemy_damage_bonus;enemy_health_bonus
```

The generator resolves these IDs into actual `StatEffect` resources.

---

# 📚 Difficulty Effect References

Difficulty does not create effects.

It references existing Effect System resources:

```
StatEffect Database

res://data/databases/difficulty/effects/
```

Resolution flow:

```
Difficulty CSV
      |
      ↓
Effect ID
      |
      ↓
StatEffect Database
      |
      ↓
DifficultyDefinition
```

This allows multiple difficulty definitions to reuse the same effects.

---

# 🗂️ Difficulty Index Generation

Location:

```
system/entity/difficulty/tools/difficulty_index_generator.gd
```

Responsibilities:

* Scan generated difficulty resources
* Scan effect resources
* Create deterministic runtime paths
* Provide export-safe loading

Generated file:

```
difficulty_index.gd
```

Architecture:

```
Difficulty Database
        ↓
Directory Scanner
        ↓
Generated Index
        ↓
Runtime Loader
```

---

# Runtime Architecture

```
                 Difficulty Manager
                        |
                        |
              DifficultyDefinition
                        |
        --------------------------------
        |              |               |
 Player Effects   Enemy Effects    Scaling Values
        |              |               |
        ↓              ↓               ↓
 Effect System     Combat System   Reward Systems
                                  Economy Systems
                                  Spawn Systems
                                  Quest Systems
```

---

# Design Goals

The Difficulty System provides:

✅ Data-driven difficulty creation
✅ No hardcoded difficulty logic
✅ Shared Effect System integration
✅ Reusable gameplay modifiers
✅ Export-safe resource loading
✅ Independent scaling categories
✅ Easy addition of new difficulties

Difficulty acts as a global ruleset layer while existing systems remain responsible for their own gameplay execution.
