# 📦 Item System

The Item System provides the centralized data framework for defining and classifying all item objects within the RPG framework.

Items are represented through data-driven `ItemResource` definitions, allowing equipment, consumables, materials, currencies, quest items, and other item types to share a common foundation.

The system provides:

* Item identity and classification
* Visual and descriptive data
* Equipment configuration
* Item requirements
* Economy and rarity data
* Stack rules
* Stat modifiers
* Gameplay effects
* Ability assignments

---

# 🧠 Item Architecture

```text
                    Item System

                         |
                  ItemResource
                         |
        ┌────────────────┼────────────────┐
        ↓                ↓                ↓
     Identity        Equipment         Economy
        |                |                |
        ↓                ↓                ↓
    Classification   Requirements     Rarity / Value
                         |
              ┌──────────┴──────────┐
              ↓                     ↓
           Modifiers             Effects
              |                     |
              ↓                     ↓
         Stat System          Effect System
                         |
                         ↓
                  Ability System
```

`ItemResource` defines the item's data and configuration.

Other systems consume that data to handle inventory, equipment, effects, abilities, economy, and gameplay behavior.

---

# 🔗 Item System Documentation

The following document covers the item resource definition.

| System           | Purpose                                                                                                     | Documentation                     |
| ---------------- | ----------------------------------------------------------------------------------------------------------- | --------------------------------- |
| 📦 Item Resource | Defines item identity, classification, equipment data, requirements, economy, stats, effects, and abilities | [Item Resource](resource_item.md) |
| 🔑 Item Requirement | Defines class, level, stat, quest, and equipped-item requirements for item usage and equipment | [Item Resource](item_requirements.md) |
| 🧲 Loot Pickup Item | Defines the base world-space pickup behavior for items, including item initialization          | [Loot Pickup Item](loot_pickup_item.md) |
| 🎁 Loot Pickup Equipment | Handles world-space equipment pickups, inventory insertion, pickup feedback               | [Loot Pickup Equipment](loot_pickup_equipment.md) |

---

# 🔗 System Integration

The Item System integrates with:

* 🎒 Inventory System
* 🛡️ Equipment System
* 📊 Stat System
* ✨ Effect System
* ⚡ Ability System
* 💰 Economy System
* 📜 Quest System
* 🏪 Shop System
* 🎁 Loot System

Items provide configuration to these systems without owning their runtime responsibilities.

---

# 📌 Design Rule

**`ItemResource` defines the item.**

The Item System provides the shared data foundation for all item types while specialized systems handle item behavior.

Items should remain data-driven, allowing new equipment, consumables, materials, currencies, quest items, and other item types to be introduced without embedding item-specific gameplay logic into the core item resource.
