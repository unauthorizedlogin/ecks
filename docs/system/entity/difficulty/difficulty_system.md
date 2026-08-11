## ⚔️ Difficulty System

The Difficulty System provides a centralized, data-driven framework for defining and scaling gameplay challenge across the entire RPG framework.

Rather than directly modifying individual systems, difficulty settings are designed to apply global modifiers through the existing **Effect System**, allowing difficulty changes to influence stats and gameplay behavior through the same scalable pipeline used by characters, equipment, buffs, and other gameplay effects.

---

### ⚠️ Current Development Status

**The Difficulty System is currently under active development and is not fully functional yet.**

The framework, resources, and intended integration path are in place, but runtime application is still being finalized.

Current development focus is:

* ✅ Difficulty resource structure
* ✅ Player and enemy effect definitions
* ✅ Integration path with the Effect System
* 🔄 Applying difficulty effects to runtime stats
* 🔄 Validating stat modification flow (HP, damage, resistances, etc.)

Features such as loot scaling, gold modifiers, XP rates, economy changes, and spawning adjustments are already designed into the resource structure but are not currently active until the core stat modification pipeline is finalized.

---

## 🎯 Core Design

Difficulty is not designed as an isolated modifier system.

Instead, it acts as a **global gameplay rule layer** that feeds into existing systems.

Difficulty Definitions provide:

* Player stat modifiers
* Enemy stat modifiers
* Reward scaling
* Economy adjustments
* Death penalties
* Spawn scaling
* Boss scaling
* Progression adjustments

These values are stored as data resources, allowing new difficulties to be created without modifying gameplay code.

---

# 🧬 Effect System Integration

Difficulty uses the **Effect System** as its primary stat modification pipeline.

Instead of directly changing values such as:

* Health
* Damage
* Armor
* Resistances
* Movement
* Other combat statistics

difficulty applies `StatEffect` resources that are processed through the existing effect architecture.

This allows difficulty scaling to remain consistent with:

* Equipment bonuses
* Temporary buffs
* Debuffs
* Environmental effects
* Future status systems

Example:

```
Difficulty: Nightmare

Enemy Effects:
    +50% Vital
    +35% Damage
    +25% Resistance

↓

Effect System

↓

Enemy Runtime StatBlock

↓

Combat System
```

---

# 🎯 Player Effects

Difficulty can optionally modify player statistics.

Examples:

* Increased damage taken
* Reduced defenses
* Additional penalties
* Alternative challenge modifiers

Configured through:

```gdscript
@export var player_effects: Array[StatEffect] = []
```

---

# 👾 Enemy Effects

Enemy scaling is currently the primary focus of implementation.

Difficulty can apply modifiers such as:

* Increased health
* Increased damage
* Increased resistances
* Additional combat scaling

Configured through:

```gdscript
@export var enemy_effects: Array[StatEffect] = []
```

---

# 💰 Reward Scaling

The framework includes support for future reward adjustments:

```gdscript
xp_multiplier
gold_multiplier
drop_rate_multiplier
magic_find_multiplier
```

Planned usage:

* Higher difficulties provide increased rewards
* Difficulty becomes a risk/reward decision
* Loot progression scales with challenge

Currently **not active**.

---

# 🏪 Economy Scaling

Difficulty definitions include economy modifiers:

```gdscript
vendor_price_multiplier
repair_cost_multiplier
```

Future uses:

* Increased vendor costs
* Increased repair expenses
* Economic pressure on higher difficulties

Currently **not active**.

---

# 💀 Death Penalties

Supports difficulty-based penalties:

```gdscript
death_xp_loss_percent
death_gold_loss_percent
```

Designed for future hardcore / higher difficulty modes.

Currently **not active**.

---

# 🌍 World Scaling

Difficulty supports world-level adjustments:

```gdscript
enemy_density_multiplier
elite_spawn_multiplier
champion_spawn_multiplier
```

Future integration:

* More enemies
* More elite encounters
* Increased danger density

Currently **not active**.

---

# 👑 Boss Scaling

Dedicated boss modifiers:

```gdscript
boss_hp_multiplier
boss_damage_multiplier
```

Allows bosses to scale independently from normal enemies.

Currently **not active**.

---

# 📈 Progression Scaling

Difficulty can eventually influence progression systems:

```gdscript
quest_reward_multiplier
reputation_multiplier
```

Future uses:

* Increased quest rewards
* Faster faction progression
* Difficulty-based progression paths

Currently **not active**.

---

# Current Implementation Summary

✅ Data-driven DifficultyDefinition resource
✅ Player / Enemy StatEffect containers
✅ Effect System integration planned as the authoritative modifier pipeline
✅ Future support for rewards, economy, spawning, bosses, and progression

🔄 Currently being finalized:

* Runtime difficulty application
* Enemy stat scaling
* Player stat scaling
* Effect lifecycle handling

The completed system will provide a scalable difficulty framework where every difficulty mode can modify the entire RPG ecosystem without requiring hardcoded gameplay changes.
