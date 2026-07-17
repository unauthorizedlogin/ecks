# 🧍 Character Controllers — Entity Control Layer

The Character Controller system provides the runtime foundation for all controllable and simulated characters.

Character controllers are responsible for:

* Movement execution
* Animation state handling
* Runtime stat assignment
* Entity identity
* Component communication
* Behavior integration
* Interaction hooks
* Combat system integration

The controller layer is designed around modular expansion, allowing player characters, NPCs, followers, and enemies to share common functionality while maintaining separate identities.

---

# 🧠 Controller Architecture

Character entities are built around `CharacterBody2D` and use a component-driven approach.

Controllers provide the bridge between:

* Stat systems
* Movement systems
* Behavior modules
* Equipment systems
* Quest systems
* Dialogue systems
* Combat systems
* Progression systems

Shared controller responsibilities:

* Receive movement instructions
* Apply runtime stats
* Update animation state
* Provide entity information
* Expose stat accessors
* Communicate with gameplay managers

---

# 🧍 Player Controller

`PlayerControl`

The Player Controller manages the playable character runtime.

Responsibilities:

* Player movement input
* Class initialization
* Stat assignment
* Equipment synchronization
* Inventory binding
* Quest synchronization
* HUD integration
* Level progression
* Teleport handling

---

## 🎮 Modular Movement

Player movement is separated into an external movement module.

Supports:

* Keyboard movement
* Mouse movement
* Acceleration/deceleration
* Menu movement locking
* Runtime speed updates from stats

The controller delegates movement processing instead of containing all movement logic internally.

---

## 🧬 Class & Stat Initialization

Player stats are loaded through:

`ClassDatabase`

The selected class ID determines:

* Base stat block
* Starting equipment
* Damage team
* Progression rules

The player receives a runtime stat block that can be modified through:

* Level growth
* Equipment
* Effects
* Other gameplay modifiers

---

## 📦 Starting Character Flow

New characters automatically initialize:

* Starting equipment
* Inventory contents
* Equipment state
* Starting quest
* Initial save state
* World entry event

Existing saves bypass new character initialization.

---

## ⚔️ Combat Integration

Player controllers connect to the combat pipeline through:

* `VitalComponent`
* Damage team assignment
* Runtime stats

The controller exposes:

* Actor ID
* Category
* Subtype
* StatBlock access

allowing combat systems to interact with the player through a consistent interface.

---

## 🌀 Teleport Support

Player teleportation supports:

* Position relocation
* Camera synchronization
* Teleport cooldown protection
* Teleport event broadcasting

The `teleported` signal allows other systems such as:

* Followers
* Cameras
* Quest systems
* World systems

to react to position changes.

---

# 🤖 Character Controller

`CharacterControl`

The base NPC-style character controller provides common functionality for non-player entities.

Used for:

* Friendly NPCs
* World characters
* Followers
* Ambient entities

---

## 🚶 Movement System

Character movement is behavior-driven.

Controllers receive movement instructions through:

`move_vector`

Movement supports:

* Acceleration
* Deceleration
* Directional animation updates
* Idle states
* Moving states

External systems can control movement through:

* Patrol behaviors
* Follow behaviors
* Future AI systems

---

## 🧠 Behavior Integration

Character controllers are designed to receive instructions from modular behaviors.

Examples:

* `BehaviorFollow`
* `BehaviorPatrol`
* Target detection systems

This separates:

**How an entity moves**

from

**Why an entity moves**

---

# 👥 NPC Interaction Layer

NPC controllers provide interaction hooks for world gameplay.

Supports:

* Player proximity detection
* Interaction input
* Dialogue launching
* Shop integration
* Quest integration

NPC identity is defined through:

* NPC ID
* NPC class ID
* Interaction flags

---

## 💬 Dialogue Integration

NPCs can expose:

* Dialogue availability
* Shop availability
* Quest availability

Interaction routes through:

`DialogueManager`

allowing NPC conversations to combine:

* Flavor dialogue
* Quest dialogue
* Shop options
* Future interaction types

---

# 👹 Enemy Controller

`EnemyControl`

The enemy controller extends the character foundation with combat-oriented functionality.

Responsibilities:

* Enemy identity
* Combat team assignment
* Damage handling integration
* Knockback reactions
* AI movement support

---

## ⚔️ Combat Integration

Enemies connect directly with:

* VitalComponent
* Damage Team system
* Effect system
* Stat pipeline

Enemy stats are generated from:

`ClassDatabase`

and duplicated into runtime instances.

This allows each enemy instance to independently receive:

* Effects
* Difficulty modifiers
* Runtime changes
* Combat scaling

---

## 💥 Knockback System

Enemy controllers support temporary movement overrides through knockback.

Features:

* Directional impulse
* Force scaling
* Temporary movement priority

Knockback overrides normal movement until the impulse expires.

---

# 📊 Runtime Stat Access

All controllers expose common stat access methods.

Available information:

* Full StatBlock
* Actor ID
* Actor Category
* Actor Subtype
* Individual stat lookup

This allows external systems to remain controller-agnostic.

---

# 🔮 Future Controller Separation

The controller layer is actively being refined into more specialized roles.

Planned separation may include:

* Base character controller
* Player-specific controller
* NPC controller
* Enemy controller
* Companion controller
* AI controller
* Combat controller

The goal is to maintain shared foundations while preventing player, NPC, and combat logic from becoming tightly coupled.

---

**Current Status:**
Character Controllers provide the runtime identity layer for all living entities, connecting movement, stats, behaviors, interaction, progression, and combat into a unified but expandable framework.
