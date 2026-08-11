## 🧬 Class Database — Class Identity & Rule Separation

The Class Database separates **class statistics** from **class behavior rules** by using two dedicated resources:

* `StatBlock` → class identity and stat foundation
* `ClassRuleResource` → class restrictions, visuals, equipment, and gameplay rules

This keeps numerical progression separate from class logic and allows classes to share stat structures while maintaining unique behaviors.

---

# 📊 StatBlock — Class Stat Authority

`StatBlock` is the source of truth for class-based attributes.

Each class references its own stat configuration through:

```gdscript
class_id
```

The StatBlock defines:

* Base attributes
* Derived combat values
* Resource pools
* Resistances
* Offensive statistics
* Defensive statistics
* Growth values

Examples:

```text
Warrior
 ├── Strength
 ├── Vitality
 ├── Armor
 ├── HP
 └── Physical Damage

Mage
 ├── Intelligence
 ├── Wisdom
 ├── MP
 ├── Spell Power
 └── Elemental Resistance
```

The class database does not manually manage stats; it delegates all numerical values to StatBlock.

---

# 🏗️ Class Loading Structure

Final relationship:

```text
Class Database
        |
        |
        +----------------+
        |                |
        ↓                ↓

   StatBlock       ClassRuleResource

   Numbers         Behavior
   Growth          Restrictions
   Combat Stats    Equipment
   Resources       Visuals
                   Tags
```

Player chooses Class
        ↓
ClassDatabase
        ↓
StatBlock + ClassRuleResource
        ↓
Player Entity
        ↓
StatManager builds final stats
        ↓
Gameplay

---

# Framework Role

The Class Database provides:

✅ data-driven classes
✅ independent stat scaling
✅ reusable rule definitions
✅ equipment restrictions
✅ weapon/armor permissions
✅ future subclass support
✅ clean separation between numbers and behavior

Classes are no longer hard-coded entities. They are assembled from **stat identity + gameplay rules**, allowing the framework to support large numbers of playable classes without code duplication.
