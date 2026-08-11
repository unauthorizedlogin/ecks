# 🧬 Class System

The Class System provides the centralized, data-driven framework for defining and accessing character classes.

Classes are assembled from specialized resources, allowing identity, rules, progression, growth, and starting configuration to remain independently defined while the Class Database provides a unified runtime access layer.

The system is responsible for:

* Class definitions and identity
* Class rules and restrictions
* Stat block assignments
* Starting abilities and equipment
* Level progression and growth
* XP progression data
* Class lookup and filtering

---

# 🧠 Class Architecture

```text
                    Class System

                         |
                  Class Database
                         |
        -----------------------------------
        |        |         |       |      |
    ClassData  Rules   LevelCurve  Growth  XP
        |
   -------------
   |     |     |
 Stats Abilities Equipment
```

The Class Database provides centralized access to the class resources, while the individual resources define their specific configuration.

---

# 🔗 Class System Documentation

| Resource        | Purpose                                                                  | Documentation                   |
| --------------- | ------------------------------------------------------------------------ | ------------------------------- |
| 🧍 Class Data   | Core class identity, archetype, stats, starting abilities, and equipment | [Class Data](class_data.md)     |
| ⚖️ Class Rules  | Class restrictions, tags, weapon/armor rules, and other gameplay rules   | [Class Rules](class_rules.md)   |
| 📈 Level Curve  | Level progression requirements and progression curves                    | [Level Curve](../xp/level_curve.md)   |
| 📊 Level Growth | Class stat growth throughout progression                                 | [Level Growth](../xp/level_growth.md) |
| ⭐ XP Data       | XP progression data used by class leveling                               | [XP System](../xp/xp_system.md)    |

---

# 🔗 System Integration

The Class System provides configuration consumed by other gameplay systems, including:

* Character System
* Player System
* Enemy System
* NPC System
* Stats System
* Ability System
* Item / Equipment System
* XP / Leveling System

The Class System defines **what a class is and how it is configured**. Other systems consume that configuration to perform their runtime responsibilities.

---

# 📌 Summary

The Class System provides a centralized, resource-driven architecture for defining character classes and their progression.

Specialized resources keep class configuration modular, while the Class Database provides a consistent runtime interface for accessing and querying class data.
