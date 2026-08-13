# 🖥️ Player HUD System

The Player HUD provides the centralized in-game interface for displaying player status, progression, resources, abilities, and gameplay tracking information.

The HUD presents player-facing gameplay information while specialized HUD components remain responsible for their respective data and interaction.

The system provides:

* Player vital displays
* Experience and level display
* Currency display
* Potion display
* Ammunition display
* Skill bar presentation
* Main quest tracking
* Side quest tracking

---

# 🧠 Player HUD Architecture

```text
                     Player HUD

                          |
             ┌────────────┼────────────┐
             ↓            ↓            ↓
          Vitals       Progression   Gameplay
             |            |            |
       HP / MP /      XP / Level     Resources
        Resource                    / Abilities
                                      |
              ┌───────────────────────┴───────────────────────┐
              ↓                                               ↓
       Main Quest HUD                                   Side Quest HUD
              |
       Quest Tracking
```

The Player HUD provides the overall player-facing presentation layer.

Individual HUD components consume data from their owning gameplay systems rather than duplicating gameplay state.

---

# 🔗 Player HUD Documentation

The following documents cover specialized Player HUD components.

| SystemPurposeDocumentation |                                               |                |
| -------------------------- | --------------------------------------------- | -------------- |
| 💰 Currency Panel            | Displays player currency and currency values  | [Currency Panel](currency_panel.md)   |
| 🧪 Potion Panel              | Displays available potion resources           | [Potion Panel](potion_panel.md)     |
| 🔫 Ammo Panel                | Displays available ammunition                 | [Ammo Panel](ammo_panel.md)       |
| ⚡ Skill Bar                | Displays player abilities and skill actions   | [Skill Bar](skill_bar.md)      |
| 🎯 Main Quest HUD          | Displays the active main quest and objectives | [Main Quest HUD](quest_tracker.md) |
| 📜 Side Quest HUD          | Displays tracked side quests and objectives   | [Side Quest HUD](side_quest_tracker.md) |

---

# 🔗 System Integration

The Player HUD integrates with:

* ❤️ Vital System
* 📈 Progression System
* 💰 Economy System
* 🧪 Consumable System
* 🔫 Combat System
* ⚡ Ability System
* 🎯 Quest System
* 🖥️ UI System

The Player HUD presents gameplay state without owning the underlying player, economy, ability, or quest logic.

---

# 📌 Design Rule

**The Player HUD presents player gameplay state.**

**Specialized HUD components display their respective systems.**

**Gameplay systems remain the authority for their underlying data.**

The Player HUD should remain a presentation layer, allowing vitals, progression, currency, consumables, abilities, and quest tracking systems to evolve independently from the main HUD.
