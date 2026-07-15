# ⚔️ Ecks Combat System

## Overview

The Ecks Combat System is a centralized combat framework responsible for managing the complete damage lifecycle between gameplay entities.

At the center of the system is the Combat Manager, which acts as the authoritative entry point for all combat interactions. Rather than allowing individual actors, weapons, or abilities to handle their own damage logic, all combat requests flow through a unified pipeline.

The Combat Manager is responsible for:

- Receiving combat requests
- Resolving final damage outcomes
- Applying damage to valid targets
- Processing combat-related effects
- Broadcasting combat events
- Reporting combat results to connected systems

This centralized approach provides a consistent foundation for player combat, enemy encounters, skills, equipment effects, and future combat expansions.

---

## Combat Manager Responsibility

The Combat Manager coordinates combat interactions but intentionally avoids owning systems outside of combat flow.

The Combat Manager does **not**:

- Store health values
- Manage UI presentation
- Control artificial intelligence
- Handle animations or visual effects

Instead, it communicates results outward to specialized systems responsible for their own domains.

This separation keeps combat logic modular and allows gameplay systems to respond to combat events without creating direct dependencies.

---

## Damage Processing

When combat occurs, the Combat Manager receives a damage request and determines the appropriate resolution path.

The system supports:

- Calculated damage resolution
- Direct damage application
- Living targets
- Destructible world targets
- Combat effect triggers
- Kill event tracking

After a successful damage application, the Combat Manager broadcasts the resulting combat data so other systems can react.

---

## Combat Integration

The Combat Manager serves as a connection point between multiple Ecks framework systems.

Current integrations include:

- Vital systems for applying damage to living entities
- Resource systems for destructible objects
- Equipment systems for combat effects
- Effect systems for triggered behaviors
- Quest systems through kill tracking
- Event systems for combat logging

The result is a combat architecture where damage is handled consistently while remaining independent from the systems that consume combat results.

---

## Design Goals

The Ecks Combat System is built around:

### Centralized Authority

All combat interactions follow the same controlled pipeline.

### Modular Systems

Combat calculations, health state, effects, UI, and AI remain separated.

### Expandability

New combat features can be added without requiring every actor or gameplay object to implement custom damage logic.

# Combat Resolution

## Overview

The Combat Resolver is responsible for determining the outcome of a combat interaction.

While the Combat Manager controls the overall combat flow, the Combat Resolver handles the actual combat calculations required to produce a finalized damage result.

The resolver receives a prepared combat context and evaluates:

- Whether the attack is valid
- Damage output
- Critical hit behavior
- Defensive mitigation
- Final damage values

---

## Resolution Responsibilities

The Combat Resolver handles:

- Target validation
- Team relationship checks
- Base damage calculation
- Critical hit calculations
- Critical damage scaling
- Armor mitigation
- Final damage generation

The resolver does **not**:

- Apply damage directly
- Modify health
- Handle UI feedback
- Trigger visual effects
- Manage combat events

Those responsibilities remain with other framework systems.

---

# Combat Validation

Before damage is calculated, every attack passes through a universal targeting validation layer.

The resolver verifies:

- Attacker statistics exist
- Defender statistics exist
- Team information is available
- The attack is allowed based on combat relationships

Invalid combat interactions return a failed damage result instead of applying damage.

---

# Damage Calculation Pipeline

Combat resolution follows this flow:

```text
Combat Context
      |
      v
Target Validation
      |
      v
Base Damage
      |
      v
Critical Calculation
      |
      v
Defense Mitigation
      |
      v
Final Damage Result

Got it. We are continuing the **same single combat system MD**, adding the next sections only. This batch covers:

* CombatContext
* DamageRequest
* DamageResult
* VitalComponent
* ResourceDamageTeam

````md
---

# Combat Data Structures

## Overview

The Ecks Combat System separates combat intent, combat context, and combat outcomes into dedicated data structures.

This keeps combat logic centralized while allowing different gameplay systems to request, resolve, and react to combat interactions without tightly coupling to each other.

The primary data flow is:

```text
DamageRequest
       |
       v
CombatContext
       |
       v
CombatResolver
       |
       v
DamageResult
````

---

# Damage Request

## Overview

`DamageRequest` represents a requested combat interaction.

It is a lightweight data container used to describe an attempted action before combat resolution occurs.

DamageRequest contains no combat logic.

---

## Supported Sources

DamageRequest is designed to support:

* Melee attacks
* Projectiles
* Area attacks
* Damage over time effects
* Skills
* Future combat abilities

---

## Request Data

A damage request contains:

### Source

The actor causing the damage.

Examples:

* Player
* Enemy
* Ability caster
* Environmental source

---

### Target

The entity receiving the interaction.

Examples:

* Enemy
* Player
* Destructible object

---

### Damage Information

Includes:

* Damage amount
* Damage type
* Critical permissions
* Combat tags

---

### Ability Information

Optional identifiers allow combat actions to track their origin.

Examples:

* Skill ID
* Projectile ID

---

# Damage Result

## Overview

`DamageResult` represents the finalized outcome of a combat interaction.

The Combat Manager returns this object after resolution.

It provides a standardized result that other systems can consume without needing to understand the calculation process.

---

## Result Data

DamageResult contains:

* Source actor
* Target actor
* Original damage
* Final damage
* Damage type

---

## Combat States

The result tracks combat outcomes including:

### Critical Hits

Indicates whether the attack received critical damage.

---

### Killing Blows

Indicates whether the attack defeated the target.

---

### Invalid Combat States

Supports tracking:

* Blocked attacks
* Dodged attacks
* Missed attacks
* Invalid hits

---

# Combat Context

## Overview

`CombatContext` provides the Combat Resolver with all required information to calculate an interaction.

Rather than having the resolver directly inspect actors, CombatContext gathers the required combat data before resolution begins.

---

## Context Responsibilities

CombatContext provides:

* Attacker reference
* Defender reference
* Attacker statistics
* Defender statistics
* Attacker team information
* Defender team information

---

## Actor Data Collection

CombatContext builds itself from combat participants.

The system retrieves:

* VitalComponent data
* StatBlock references
* Damage team assignments

This keeps combat calculations independent from actor implementation.

---

## Class Based Combat Rules

Damage teams are resolved through class data.

The Class Database acts as the authority for determining combat relationships.

This allows:

* Character classes
* NPC types
* Enemy categories

to define their own combat behavior without hardcoded checks.

---

# Vital Component

## Overview

`VitalComponent` manages the runtime combat state of living entities.

It acts as the connection between combat results and an entity's current survival state.

VitalComponent does not calculate damage.

---

## Responsibilities

VitalComponent manages:

* Current health
* Current mana
* Class resources
* Healing
* Damage application
* Death state
* Revival
* Regeneration

---

## Does Not Handle

VitalComponent does not:

* Calculate attacks
* Determine damage values
* Perform mitigation
* Resolve critical hits
* Control combat rules

Those responsibilities belong to the Combat Manager and Combat Resolver.

---

# Runtime State

VitalComponent tracks mutable combat values.

## Health

Tracks:

* Current HP
* Maximum HP
* Health regeneration

---

## Mana

Tracks:

* Current MP
* Maximum MP
* Mana regeneration

---

## Class Resources

Supports custom class resources.

Examples:

* Rage
* Energy
* Stamina
* Other class-specific mechanics

---

# Damage Application

Damage is applied after combat resolution.

The flow is:

```text
DamageResult
      |
      v
VitalComponent
      |
      v
Health Updated
      |
      v
Death Check
```

VitalComponent receives finalized damage values and updates runtime state.

---

# Healing

VitalComponent provides controlled healing behavior.

Healing:

* Cannot restore dead entities
* Cannot exceed maximum health
* Broadcasts health updates

---

# Death Handling

When health reaches zero:

```text
Current HP <= 0
        |
        v
Death State
        |
        v
died Signal
```

Death events can be consumed by:

* Quest systems
* Loot systems
* Enemy systems
* Game state systems

Player deaths are routed through the Game Manager.

---

# Revival

Revival is handled through data-driven effects.

Supported restoration:

* Percentage based HP restoration
* Flat HP restoration
* Mana restoration
* Resource restoration

Revival effects allow abilities and items to define their own recovery behavior.

---

# Resource Costs

VitalComponent supports ability resource management.

Supported costs:

* Health costs
* Mana costs
* Class resource costs

Before applying costs, the system verifies that the entity can afford the action.

---

# Regeneration

VitalComponent includes automatic regeneration support.

Supported regeneration:

* HP regeneration
* MP regeneration
* Resource regeneration

Regeneration:

* Runs on timed intervals
* Respects maximum values
* Stops while dead

---

# Runtime Synchronization

VitalComponent references external StatBlock data instead of owning statistics.

This allows the system to respond to:

* Equipment changes
* Level changes
* Stat rebuilding
* Character progression

without recreating runtime state.

---

# Damage Teams

## Overview

`ResourceDamageTeam` defines combat relationships between entities.

Instead of hardcoding friendly or hostile relationships, Ecks uses configurable team resources.

---

## Supported Teams

Default teams include:

* Neutral
* Player
* NPC
* Enemy

---

## Team Rules

Teams define:

* Allied teams
* Enemy relationships
* Friendly fire behavior

Example:

```text
Player Team
    |
    +-- Enemy = Hostile
    |
    +-- NPC = Friendly
    |
    +-- Neutral = Configurable
```

---

## Benefits

Team resources allow combat relationships to change without modifying combat code.

This supports future systems such as:

* Factions
* PvP rules
* Temporary alliances
* Special encounters
* Custom enemy groups

```
