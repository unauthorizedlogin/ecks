# 🧍 Player Manager

## Overview

The **Player Manager** is the central authority for the player lifecycle within the framework.

It is responsible for creating the player, maintaining the active player reference, coordinating player bootstrap, and managing player serialization during save and load operations.

Rather than allowing gameplay systems to instantiate or configure the player independently, the Player Manager provides a single, deterministic initialization pipeline that prepares the player for gameplay.

This includes:

* 🧍 Player creation
* 🔎 Player lookup
* 🚀 Player bootstrap
* 📍 Position management
* 💾 Player serialization
* 📂 Player deserialization
* 🔗 System integration
* 🎮 Spawn finalization

---

# 🧠 Core Responsibilities

The Player Manager provides:

* Player lifecycle management
* Player reference ownership
* Bootstrap coordination
* Save/load preparation
* Player discovery
* Position management
* System registration

The Player Manager owns the player lifecycle—not gameplay behavior.

---

# ⚙️ Initialization

```gdscript
func initialize()
```

Registers the Player Manager as an active gameplay system.

Prevents duplicate initialization.

Output:

```text
🧍 PlayerManager initialized
```

---

# 🧍 Player Lifecycle

The Player Manager guarantees a single active player instance throughout gameplay.

Player creation follows a lazy initialization model.

```text
Player Request
       ↓
Existing Player?
   ↓        ↓
 Yes       No
  ↓         ↓
Return   Instantiate
            ↓
      Apply Pending Data
            ↓
      Return Player
```

This prevents duplicate player creation while allowing save data to be restored before the player enters the world.

---

# 🔎 Player Lookup

The manager provides centralized access to the active player.

If no cached player reference exists, the manager searches the active scene hierarchy and restores the reference automatically.

This allows gameplay systems to safely request the player without maintaining their own references.

---

# 📍 Position Management

The manager owns player position persistence.

Supported operations include:

* Retrieve current position
* Restore saved position
* Spawn positioning

Position handling is isolated from save logic, allowing world loading and player spawning to remain independent systems.

---

# 🚀 Player Bootstrap

After the player has been created, the manager performs a structured bootstrap sequence to prepare gameplay.

The bootstrap process configures every major gameplay system before control is returned to the player.

```text
Create Player
      ↓
Core Initialization
      ↓
Gameplay Modules
      ↓
User Interface
      ↓
Statistics
      ↓
Equipment
      ↓
Starting Flow
      ↓
Vitals
      ↓
Finalize Stats
      ↓
Progression
      ↓
Spawn Complete
```

Each stage has a clearly defined responsibility and executes in a deterministic order.

---

# 🧱 Core Initialization

The first bootstrap stage establishes the player's identity.

This includes:

* Selected class
* Class data
* Level component configuration

Without a valid class definition, bootstrap is halted to prevent incomplete player initialization.

---

# 🔗 Gameplay Module Integration

The Player Manager connects the player to gameplay systems that require a player context.

Current integrations include:

* Inventory Manager
* Equipment Manager
* Requirements Manager
* Quest Manager

This centralizes player registration rather than requiring every manager to discover the player independently.

---

# 🖥️ User Interface Integration

The manager connects gameplay UI to the active player.

Supported initialization includes:

* Player HUD
* Player statistics panel
* UI movement events

This ensures interface components receive the correct player reference before gameplay begins.

---

# 📊 Statistics Initialization

Player statistics are initialized before equipment and progression systems begin operating.

Once initialization is complete, final runtime statistics are rebuilt and applied to the player.

This guarantees that every gameplay system works with finalized character statistics.

---

# 🛡️ Equipment Initialization

Equipment synchronization occurs after statistics have been initialized.

This allows equipment bonuses to be applied using the finalized player stat framework rather than partially initialized values.

---

# 🌍 Starting Game Flow

When beginning a completely new game, the manager performs first-time player setup.

This includes:

* Starting equipment
* Initial inventory
* Equipment synchronization
* Initial quests
* Automatic quests
* Initial save creation

These operations occur only once during a new game session and are skipped when loading an existing save.

---

# ❤️ Vital Initialization

The manager configures the player's vital systems.

Initialization includes:

* Stat ownership
* Damage team assignment
* Death event connections

This prepares the player for participation in combat while keeping combat systems independent from player creation.

---

# 📈 Progression Integration

After gameplay initialization, progression systems are connected.

Current integration includes:

* Experience initialization
* Level growth events
* Maximum level events

This stage prepares the player for ongoing character progression throughout the session.

---

# 🎮 Spawn Finalization

The final bootstrap stage completes player readiness.

Responsibilities include:

* Runtime stat application
* Final gameplay signals
* Cursor initialization
* Spawn completion

Once this stage finishes, the player is fully prepared for gameplay.

---

# 💾 Serialization

The Player Manager owns player-specific save data.

Current serialized information includes:

* Player position

The serialization pipeline is designed to expand as additional player systems become persistent.

Future support includes:

* Statistics
* Equipment
* Abilities
* Additional player progression

---

# 📂 Deserialization

During loading, player data is staged before player creation.

Pending data is applied automatically when the player is instantiated.

The manager also maintains compatibility with legacy save formats by recognizing previous data structures during deserialization.

---

# 🔗 System Relationships

```text
             Save Manager
                  |
                  ↓
          Player Manager
                  |
      ┌───────────┼────────────┐
      ↓           ↓            ↓
 Create      Bootstrap    Serialization
      |           |            |
      ↓           ↓            ↓
    Player   Gameplay Systems  Saves
                  |
      ┌───────────┼───────────────┐
      ↓           ↓               ↓
 Inventory   Equipment      Requirements
      ↓           ↓               ↓
    Quest      Statistics      User Interface
```

---

# 🧭 System Boundaries

The Player Manager coordinates player initialization but intentionally delegates specialized responsibilities.

| Responsibility  | System            |
| --------------- | ----------------- |
| World loading   | World Manager     |
| Session state   | Game Manager      |
| Saving files    | Save Manager      |
| Inventory logic | Inventory Manager |
| Equipment logic | Equipment Manager |
| Combat          | Combat Manager    |
| Statistics      | Stat Manager      |
| User interface  | UI Manager        |

This separation allows the player lifecycle to remain predictable while each gameplay system manages its own domain.

---

# ✅ Design Rule

**PlayerManager is the single authority for player lifecycle management.**

No gameplay system should instantiate, bootstrap, serialize, or maintain its own player instance.

All player creation, initialization, save restoration, and gameplay registration should flow through the Player Manager, ensuring a consistent and deterministic startup process across the framework.
