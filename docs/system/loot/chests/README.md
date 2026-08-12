# 📦 Chest Loot System

The Chest Loot System provides the centralized framework for interactive world containers.

The system supports both temporary world rewards and persistent player storage while maintaining a shared interaction foundation.

The system provides:

* Dynamic loot chests
* Persistent storage chests
* World interaction
* Loot spawning
* Storage inventory integration
* Quest interaction events
* Chest state management

---

# 🧠 Chest Architecture

```text
                    Chest System

                          |
                 ┌────────┴────────┐
                 ↓                 ↓
          Dynamic Loot        Storage Chest
              Chest                |
                 |                 |
          ┌──────┴──────┐          ↓
          ↓             ↓      Inventory
        Loot          Quest       System
       Spawn         Events
```

Dynamic Loot Chests provide one-time world rewards.

Storage Chests provide persistent item storage through the Inventory System.

---

# 🔗 Chest System Documentation

The following documents cover the major parts of the Chest Loot System.

| System                | Purpose                                                                         | Documentation                       |
| --------------------- | ------------------------------------------------------------------------------- | ----------------------------------- |
| 🎁 Dynamic Loot Chest | Defines one-time world containers, loot spawning, interaction, and quest events | [Dynamic Loot Chest](chest_dynamic.md) |
| 🏦 Storage Chest      | Defines persistent storage containers and inventory integration                 | [Storage Chest](chest_storage.md)   |

---

# 🔗 System Integration

The Chest Loot System integrates with:

* 🎒 Inventory System
* 📜 Quest System
* 🖥️ UI System
* 🧍 Player System
* 🌍 World System

Dynamic Loot Chests primarily integrate with the Quest and World systems.

Storage Chests primarily integrate with the Inventory, UI, and Player systems.

---

# 📌 Design Rule

**Dynamic Loot Chests handle temporary world rewards.**

**Storage Chests handle persistent player storage.**

Both container types use the shared chest interaction foundation while keeping reward generation and persistent inventory responsibilities separate.

The Chest Loot System provides the world-container layer without owning inventory, quest, or UI logic.
