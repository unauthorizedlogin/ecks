# ⚔️ Combat Manager

## Overview

The **Combat Manager** is the centralized runtime authority for resolving and applying combat damage within the framework.

It receives `DamageRequest` objects, passes combat calculations through the `CombatResolver`, applies the resulting damage to valid target life systems, processes combat-triggered effects, tracks killing blows, and broadcasts combat events.

The manager separates **combat orchestration** from the systems responsible for stats, health, effects, UI, and presentation.

The Combat Manager does **not** own target health, combat calculations, AI behavior, UI, or visual effects.

---

# 🧠 Core Responsibilities

The Combat Manager provides:

* ⚔️ Damage request processing
* 🧮 Combat resolution coordination
* 🛡️ Mitigated damage application
* ❤️ Vital damage application
* 🧱 Resource-life damage application
* 💥 Attack-triggered effect processing
* ☠️ Killing-blow detection
* 📢 Combat event broadcasting
* 📜 Combat log generation

The manager acts as the runtime orchestration layer between combat requests and the systems that actually own the affected data.

---

# ⚙️ Initialization

```gdscript
func initialize()
```

Registers the Combat Manager as an active framework system.

Initialization is protected against duplicate execution through `is_initialized`.

Output:

```text
⚔️ CombatManager initialized
```

---

# ⚔️ Combat Request Pipeline

Combat begins with a `DamageRequest`.

```text
DamageRequest
      ↓
CombatManager
      ↓
CombatContext
      ↓
CombatResolver
      ↓
DamageResult
      ↓
Damage Application
```

The Combat Manager does not calculate raw combat formulas itself.

Instead, it constructs a `CombatContext` from the request source and target and delegates combat resolution to `CombatResolver`.

This keeps combat calculation logic separate from runtime damage application.

---

# 🧮 Combat Resolution

```gdscript
var ctx := CombatContext.build_from_nodes(
    request.source,
    request.target
)

var result := CombatResolver.resolve_attack(ctx)
```

`CombatResolver` produces the finalized `DamageResult`.

The result contains the resolved combat outcome, including values such as:

* Final damage
* Critical-hit state
* Killing-blow state

If the resulting damage is zero or invalid, the Combat Manager stops the damage application pipeline.

```text
CombatRequest
      ↓
CombatContext
      ↓
CombatResolver
      ↓
DamageResult
      ↓
Final Damage
```

---

# ❤️ Vital Damage

Targets containing a `VitalComponent` receive their resolved damage through the target's vital system.

```gdscript
vitals.apply_damage(result.final_damage)
```

The Combat Manager does not store or directly manage HP.

Instead:

```text
CombatManager
      ↓
VitalComponent
      ↓
Target Vital State
```

The `VitalComponent` remains responsible for the target's actual vital state and death condition.

---

# 🧱 Resource-Life Targets

Combat is not restricted to living entities.

Targets may expose a `ResourceLife` instance through `resource_life`.

This allows combat damage to affect destructible or environmental objects.

```text
DamageRequest
      ↓
CombatManager
      ↓
ResourceLife
      ↓
Resource Damage
```

This provides a common damage pipeline for both entity-based targets and resource-based destructibles.

---

# ☠️ Killing Blow Tracking

After vital damage is applied, the Combat Manager checks whether the target entered a dead state.

```gdscript
if vitals.dead:
    result.was_killing_blow = true
    target_killed.emit(
        request.target,
        request.source
    )
```

The `target_killed` signal broadcasts the completed kill event.

This allows other systems to react to kills without requiring the Combat Manager to own progression, rewards, quests, or other downstream systems.

---

# 💥 Attack Effect Processing

Combat can trigger effects originating from equipped items.

The Combat Manager evaluates equipped item effects after damage resolution.

Supported trigger types include:

* `Immediate`
* `OnHit`
* `OnCrit`

The effects themselves are delegated to `EffectManager`.

```text
CombatManager
      ↓
EquipmentManager
      ↓
ItemDatabase
      ↓
EffectDatabase
      ↓
EffectManager
```

### Immediate

Immediate effects are applied to the attack source.

### OnHit

On-hit effects are applied when the attack successfully resolves against its target.

### OnCrit

Critical-hit effects are only processed when the resulting `DamageResult` indicates a critical hit.

This allows equipment-driven combat effects to remain part of the existing Effect System rather than being implemented directly inside combat logic.

---

# 🚫 DOT Trigger Protection

Damage originating from `DOT` does not process attack-triggered equipment effects.

```gdscript
if request.origin != DamageRequest.DamageOrigin.DOT:
    _process_attack_effects(request, result)
```

This prevents damage-over-time ticks from being treated as new attacks and repeatedly triggering attack-based effects.

---

# 📢 Combat Events

The Combat Manager exposes two primary signals.

```gdscript
signal damage_applied(result)
signal target_killed(target, killer)
```

### `damage_applied`

Emitted after the damage pipeline has completed.

Provides the resulting `DamageResult`.

### `target_killed`

Emitted when applied vital damage results in the target entering a dead state.

Provides:

* Target
* Killer/source

These signals allow other framework systems to observe combat outcomes without coupling themselves directly to the damage implementation.

---

# 📜 Combat Logging

The Combat Manager provides combat log output for player-relevant combat events.

Combat logging is only generated when either:

* The source is the player.
* The target is the player.

This prevents unrelated NPC or environmental combat from unnecessarily filling the player-facing combat log.

The generated message can include:

* Source
* Target
* Damage amount
* Damage type
* Critical-hit state
* Damage origin

Damage types receive their presentation color through:

```gdscript
EventManager.get_damage_type_color(
    request.damage_type
)
```

The Combat Manager therefore provides the combat event data while `EventManager` remains responsible for event presentation and color configuration.

---

# 🎯 Damage Origins

Combat behavior can vary according to the `DamageRequest` origin.

Supported origins include:

* `ATTACK`
* `SKILL`
* `DOT`
* `REFLECT`
* `HP_COST`

Examples:

```text
ATTACK
    ↓
Normal / Critical combat damage
```

```text
SKILL
    ↓
Normal / Critical combat damage
```

```text
DOT
    ↓
Damage only
    ↓
No attack-triggered effects
    ↓
No combat log entry
```

```text
REFLECT
    ↓
Reflected damage combat event
```

```text
HP_COST
    ↓
Health expenditure combat event
```

This allows the same damage pipeline to support multiple combat sources without requiring separate damage systems.

---

# 🧩 Actor Data

The manager provides internal actor inspection helpers for standardized combat identity.

```gdscript
func _get_actor_data(actor: Node) -> Dictionary
```

Actor data may include:

* Actor ID
* Actor category
* Actor subtype

These values are retrieved through actor-defined methods when available.

This provides a common metadata structure for future combat systems without requiring the Combat Manager to depend on a specific actor implementation.

---

# 🏷️ Display Name Resolution

```gdscript
func _get_name(node: Object) -> String
```

Combat log names are resolved through a simple priority order:

```text
get_display_name()
      ↓
node.name
      ↓
"Unknown"
```

This allows combat logging to use framework-defined display names while retaining a safe fallback for generic nodes.

---

# 🔗 System Relationships

```text
                  DamageRequest
                        |
                        ↓
                ┌───────────────┐
                │ CombatManager │
                └───────┬───────┘
                        |
                        ↓
                CombatContext
                        |
                        ↓
                CombatResolver
                        |
                        ↓
                  DamageResult
                        |
             ┌──────────┴──────────┐
             ↓                     ↓
       VitalComponent          ResourceLife
             |                     |
             ↓                     ↓
        Entity Life          Resource Life
             |
             ↓
       Kill Detection
             |
             ↓
      target_killed signal
```

Attack effects follow a separate branch:

```text
CombatManager
      ↓
EquipmentManager
      ↓
ItemDatabase
      ↓
EffectDatabase
      ↓
EffectManager
```

---

# 🧭 System Boundaries

| Responsibility              | System                 |
| --------------------------- | ---------------------- |
| Combat orchestration        | Combat Manager         |
| Combat calculations         | Combat Resolver        |
| Combat context construction | Combat Context         |
| Damage request definition   | `DamageRequest`        |
| Damage result definition    | `DamageResult`         |
| Entity vitality             | `VitalComponent`       |
| Destructible resource life  | `ResourceLife`         |
| Equipment lookup            | Equipment Manager      |
| Item effect definition      | Item / Effect Database |
| Runtime effect application  | Effect Manager         |
| Combat event logging        | Event Manager          |
| AI decisions                | AI Systems             |
| Combat UI                   | UI Systems             |
| Combat FX                   | Presentation Systems   |

This separation prevents the Combat Manager from becoming responsible for systems that consume or present combat results.

---

# 🌱 Centralized Combat Architecture

The Combat Manager establishes a single runtime entry point for damage processing.

```text
Attack / Skill / Effect / Reflection
              ↓
        DamageRequest
              ↓
       CombatManager
              ↓
       CombatResolver
              ↓
         DamageResult
              ↓
      Target Life System
              ↓
       Combat Events
```

This allows new damage sources to enter the same combat pipeline without duplicating damage application, mitigation, kill detection, or event handling.

---

# 🚀 Launch Flow Integration

The Combat Manager is initialized as part of the framework's centralized Launch Flow.

It operates as framework-level combat infrastructure and remains independent from individual actors, maps, UI scenes, and AI implementations.

Combat participants submit requests to the centralized manager rather than implementing their own independent damage-resolution pipelines.

---

# 🔮 Extensibility

The architecture is designed to support additional combat behavior without moving ownership of those systems into the Combat Manager.

Future extensions may include:

* Additional damage origins
* Additional target life models
* Combat modifiers
* Damage interception
* Combat reactions
* Additional combat event types
* Expanded combat telemetry
* Multi-target combat resolution

These extensions should preserve the central pipeline:

```text
Request
  ↓
Context
  ↓
Resolution
  ↓
Result
  ↓
Application
  ↓
Events
```

---

# ✅ Design Rule

**CombatManager is the central authority for orchestrating runtime combat damage.**

It receives combat requests, delegates calculation to `CombatResolver`, applies finalized damage through the target's life system, processes combat-triggered effects, detects killing blows, and broadcasts combat events.

It does **not** own HP, combat formulas, AI, UI, or visual effects.

**CombatManager coordinates combat. Specialized systems own the data and behavior affected by combat.**
