# 🐾 Behavior System — Modular AI Behavior Components

The Behavior System provides reusable, attachable AI behavior modules for NPCs, enemies, and world entities.

Instead of hardcoding movement logic into individual characters, behaviors are separated into independent components that can be attached and configured through the Inspector.

Current behaviors provide foundational movement, detection, and interaction logic while allowing future expansion into more advanced AI systems.

---

## 🧠 Behavior Architecture

Behaviors are designed as modular components that control specific aspects of an entity's actions.

Responsibilities:

* Entity movement behaviors
* Target detection
* Following logic
* Patrol routes
* Trigger-based reactions
* AI expansion points

Behaviors communicate with their parent entity through shared properties such as:

* `move_vector`
* `target`
* movement callbacks
* entity signals

This allows the same behavior modules to be reused across different NPC and enemy types.

---

# 🐾 Follow Behavior

`BehaviorFollow` provides dynamic target-following movement.

Designed for NPC companions, followers, pets, and AI entities that need to maintain distance from another object.

Features:

* Enable/disable behavior through Inspector
* Configurable minimum follow distance
* Configurable maximum chase distance
* Automatically approaches distant targets
* Prevents overlapping by backing away when too close
* Returns toward stored position when target disappears
* Supports target teleport synchronization

Distance handling:

* Outside maximum range → move toward target
* Inside minimum range → move away
* Within ideal range → remain stationary

---

# 🧲 Magnet Behavior

`BehaviorMagnet` provides proximity-based attraction detection.

Used for objects or entities that need to react when entering a magnetic interaction radius.

Features:

* Area-based detection
* Automatic target recognition
* Delegated follow activation
* Compatible with pickup-style systems

Typical uses:

* Loot attraction
* Collectible interactions
* Nearby object reactions
* Future item magnet mechanics

---

# 🧭 Patrol Behavior

`BehaviorPatrol` provides configurable path-based movement.

Entities can follow predefined `Path2D` routes with customizable movement patterns.

Features:

* Path2D waypoint navigation
* Looping patrol routes
* Reverse direction patrols
* Configurable precision threshold
* Point reached events
* End reached events
* Pause/wait behaviors

Supported wait modes:

### AT_END

Pause only when reaching the beginning or end of the route.

### EACH_POINT

Pause at every waypoint.

### RANDOMLY

Random chance to pause at each waypoint.

### NEVER

Continuous movement without pauses.

Signals:

* `point_reached`
* `end_reached`
* `wait_started`
* `wait_finished`

---

# 🎯 Area Target Finder

`BehaviorAreaTargetFinder` provides detection sensing for AI entities.

This component monitors an Area2D and automatically assigns detected bodies as targets.

Features:

* Body detection
* Target assignment
* Target clearing when leaving range
* Always-active processing support

Designed as a foundation for:

* Enemy aggro systems
* NPC awareness
* AI state machines
* Combat targeting

---

# 🔧 Inspector-Driven Configuration

Behavior components expose their controls directly through the Inspector.

Current configurable properties include:

* Active state toggles
* Follow distances
* Patrol paths
* Loop settings
* Wait behavior
* Detection areas
* Movement thresholds

This allows designers to configure entity behavior without modifying scripts.

---

# 🔮 Future Expansion

The Behavior System is structured as a foundation for additional AI modules.

Potential future behaviors:

* Idle behavior
* Wander behavior
* Flee behavior
* Attack behavior
* Guard behavior
* Dialogue interaction behavior
* Schedule-based NPC behavior
* State-machine integration
* Behavior tree support

---

**Current Status:**
The Behavior System provides the initial AI foundation layer with reusable movement, detection, and interaction components designed to scale into larger NPC and enemy frameworks.
