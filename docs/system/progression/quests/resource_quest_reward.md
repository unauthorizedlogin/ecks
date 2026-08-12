# 🎁 Quest Reward

## Overview

`QuestReward` defines a single reward granted when a quest is completed.

It specifies the reward category, optional database identifier, and quantity/value.

---

# Resource Structure

```text
QuestReward
│
├── type
├── display_name
├── reward_id
└── amount
```

---

# Reward Types

| Type         | Purpose          |
| ------------ | ---------------- |
| `AMMO`       | Ammunition       |
| `CONSUMABLE` | Consumable items |
| `CURRENCY`   | Currency         |
| `EQUIPMENT`  | Equipment        |
| `GEM`        | Gems             |
| `INGREDIENT` | Ingredients      |
| `ITEM`       | General items    |
| `METAL`      | Metals           |
| `MINERAL`    | Minerals         |
| `POTION`     | Potions          |
| `QUEST`      | Quest items      |
| `STONE`      | Stones           |
| `XP`         | Experience       |

---

# Reward Data

| Property       | Description                       |
| -------------- | --------------------------------- |
| `type`         | Determines the reward category    |
| `display_name` | Display name for the reward       |
| `reward_id`    | Optional database/item identifier |
| `amount`       | Quantity or numeric reward value  |

```text
QuestReward
    │
    ├── type ───────► Reward category
    ├── reward_id ──► Item/database ID
    └── amount ─────► Quantity / XP / currency value
```

`QuestReward` defines the reward data; the quest/reward system handles actually granting it.
