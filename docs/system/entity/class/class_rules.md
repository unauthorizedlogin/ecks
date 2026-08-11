---

# 📜 ClassRuleResource — Class Behavior Authority

`ClassRuleResource` defines everything about what a class **can do**.

It does not contain stat values.

It controls:

* visuals
* combat team
* weapon access
* armor access
* starting equipment
* class tags
* class restrictions

---

# ⚔️ Combat Rules

Defines combat identity:

```gdscript
damage_team
```

Controls:

* faction alignment
* damage relationships
* combat targeting rules

---

# 🗡️ Weapon Permissions

ClassRuleResource defines allowed weapon categories.

Examples:

```gdscript
weapon_sword
weapon_bow
weapon_staff
weapon_shield
weapon_dual_wield
```

Allows classes to have unique loadout restrictions.

Example:

```text
Warrior
 ✓ Sword
 ✓ Shield
 ✓ Axe
 ✗ Staff

Mage
 ✓ Staff
 ✓ Wand
 ✗ Heavy Weapons
```

---

# 🛡️ Armor Permissions

Defines allowed armor tiers:

```gdscript
armor_cloth
armor_light
armor_medium
armor_heavy
```

Examples:

```text
Mage
 ✓ Cloth

Ranger
 ✓ Light
 ✓ Medium

Tank
 ✓ Heavy
```

---

# 🎒 Starting Equipment

ClassRuleResource controls initial loadout.

Equipment slots:

```text
Head
Shoulders
Arms
Hands
Body
Waist
Legs
Feet

Weapon
Offhand

Wrist
Neck
Ring
Back
Unique

Custom Slots
```

Generated through:

```gdscript
get_starting_equipment()
```

Returns a complete equipment dictionary:

```gdscript
{
    EquipSlot.WEAPON: "iron_sword",
    EquipSlot.BODY: "leather_armor"
}
```

This allows starting gear to be data-driven.

---

# 🏷️ Class Tags

Tags define class identity.

Available categories:

```text
Arcane
Beast
Fighter
Healer
Hybrid
Nature
Ranged
Spellcaster
Stealth
Support
Tank
Undead
```

Example:

```text
Paladin
 ✓ Fighter
 ✓ Healer
 ✓ Tank
 ✓ Support

Necromancer
 ✓ Arcane
 ✓ Spellcaster
 ✓ Undead
```

Tags can later drive:

* abilities
* item restrictions
* quests
* factions
* dialogue
* progression systems

---

# 🚫 Class Restrictions

Negative tags provide exclusion rules.

Example:

```gdscript
forbid_healer
forbid_spellcaster
forbid_heavy
```

Allows systems to prevent incompatible choices.

Examples:

```text
Berserker
 ✗ Spellcaster

Holy Knight
 ✗ Undead

Assassin
 ✗ Heavy Armor
```
