# 💾 Save System

The Save System provides the centralized persistence framework for storing and restoring game state.

`SaveManager` provides the public save/load API while `SaveData` defines the save schema and gameplay systems retain ownership of their own serialization.

The system provides:

* Save and load operations
* Expandable save slots
* Autosaves and quicksaves
* Manual saves
* Runtime state serialization
* Persistent world and gameplay state
* Save version migration
* Save data encryption and verification
* Secure save key management
* Save UI integration

---

# 🧠 Save Architecture

```text id="p8m4xq"
                       Save System

                            |
                       Save Manager
                      (Public API)
                            |
             ┌──────────────┼──────────────┐
             ↓              ↓              ↓
         SaveData      Save Security   Save Key Provider
             |              |              |
       Save Schema     Encrypt / Verify   Keys
             |              |
             └──────────────┘
                    |
                    ↓
              Save File Storage
                    |
              user://saves/
```

`SaveManager` provides the public API and controls the save/load process.

`SaveData` defines the persistent save schema.

`SaveSecurity` serializes, encrypts, verifies, decrypts, and deserializes save data.

`SaveKeyProvider` owns the encryption and authentication keys used by save security.

Gameplay systems retain ownership of their own serialization and provide their state to the Save System.

---

# 🔗 Save System Documentation

The following documents cover the Save System components.

| System               | Purpose                                                                       | Documentation                             |
| -------------------- | ----------------------------------------------------------------------------- | ----------------------------------------- |
| 💾 Save Manager      | Public API and central authority for save/load operations and slot management | [Save Manager](save_manager.md)           |
| 📦 Save Data         | Defines the persistent save file schema and stored game state                 | [Save Data](save_data.md)                 |
| 🔐 Save Security     | Serializes, encrypts, verifies, decrypts, and deserializes save data          | [Save Security](save_security.md)         |
| 🔑 Save Key Provider | Owns the encryption and authentication keys used by Save Security             | [Save Key Provider](save_key_provider.md) |

---

# 🔗 System Integration

The Save System integrates with:

* 🎮 Game Systems
* 🧍 Player Systems
* 🌍 World System
* 🧱 Chunk System
* 🎒 Inventory System
* ⚔️ Equipment System
* 🎯 Quest System
* 📈 Progression Systems
* 🖥️ UI System

Gameplay systems provide their serialized state while the Save System owns persistence, security, storage, and restoration.

---

# 📌 Design Rule

**`SaveManager` provides the public save/load API.**
**`SaveData` defines the save schema.**
**Gameplay systems own their serialization.**
**`SaveSecurity` protects the save pipeline.**
**`SaveKeyProvider` owns the security keys.**

The Save System keeps gameplay serialization, persistence management, and save security separated so new systems can add persistent state without expanding the core save architecture.
