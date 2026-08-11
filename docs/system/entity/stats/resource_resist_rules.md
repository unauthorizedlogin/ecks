# 🛡️ ResistRules Resource

The `ResistRules` Resource defines configuration rules used by the resistance calculation system.

It currently provides the resistance **soft-cap threshold** associated with a formula.

---

## 🧱 Resource Definition

**Resource:** `resist_rules.gd`
**Class:** `ResistRules`
**Type:** `Resource`

The resource contains:

* Formula identity
* Resistance soft-cap percentage

---

## 🆔 Formula Identity

```gdscript
@export var formula_id: String = ""
```

`formula_id` identifies the resistance rule configuration and allows it to be associated with the corresponding formula/data pipeline.

---

## 📈 Soft Cap

```gdscript
@export var soft_cap_percent: float = 0.75
```

Defines the resistance threshold at which the resistance calculation can apply its soft-cap behavior.

The default value of `0.75` represents **75%**.

The resource stores this value as configuration; the actual resistance calculation is performed by the stat calculation system.

---

## 🔗 Architecture Role

`ResistRules` separates resistance-specific rules from the general `StatFormula` definition.

```text
StatFormula
    │
    └── Resistance Formula
             │
             ▼
        ResistRules
             │
             └── Soft Cap
                    │
                    ▼
              StatCalculator
                    │
                    ▼
              Final Resistance
```

This keeps resistance rules data-driven while allowing the calculation architecture to determine how those rules are applied.

---

## 🔮 Extensibility

`ResistRules` is intentionally small so additional resistance-specific configuration can be introduced without expanding `StatFormula` with unrelated properties.

Future resistance rules may be added here as the resistance system develops.
