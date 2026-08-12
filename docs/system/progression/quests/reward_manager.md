# ⭐ Reward Manager

## Overview

The **Reward Manager** is the centralized routing system for applying rewards granted by quests.

It receives a `QuestDefinition`, iterates through its configured rewards, determines each reward type, and routes the reward to the appropriate gameplay manager.

The Reward Manager separates **reward definition** from **reward application**.

Quest data defines **what rewards are granted**, while the Reward Manager determines **which system applies them**.

This includes:

* ⭐ Experience
* 💰 Currency
* 🏹 Ammunition
* 🎒 Equipment
* 📦 Items
* 🍱 Consumables
* 🧪 Potions
* 🔷 Gems
* ⛏️ Metals
* 🪨 Minerals
* 🌿 Ingredients
* 🪨 Stones
* 📜 Quests

---

# 🧠 Core Responsibilities

The Reward Manager provides:

* Quest reward processing
* Reward type routing
* XP reward application
* Currency reward application
* Ammunition reward application
* Inventory reward application
* Consumable reward application
* Potion reward application
* Material reward application
* Quest reward activation
* Reward ID normalization where required

The manager does not define reward content itself.

Reward behavior is driven by `QuestReward` data contained within a `QuestDefinition`.

---

# ⚙️ Initialization

```gdscript
func initialize() -> void
```

Registers the Reward Manager as an active framework system.

Initialization is guarded so the manager cannot be initialized more than once.

Output:

```text
⭐ RewardManager initialized
```

---

# 🎁 Quest Reward Entry Point

```gdscript
func grant_rewards(def: QuestDefinition) -> void
```

`grant_rewards()` is the primary entry point for quest rewards.

The manager receives the completed quest definition and processes each configured reward.

```text
QuestDefinition
      │
      └── rewards[]
            │
            ▼
      RewardManager
            │
            ▼
       Reward Router
```

Each reward is processed independently through `_apply_reward()`.

If no quest definition is provided, the reward process exits without applying anything.

---

# 🎁 Reward Routing

```gdscript
func _apply_reward(reward: QuestReward) -> void
```

The reward router examines `QuestReward.RewardType` and delegates the reward to the appropriate subsystem.

```text
                    QuestReward
                         │
                         ▼
                  Reward Manager
                         │
              ┌──────────┼──────────┐
              ▼          ▼          ▼
             XP       Currency      Ammo
              │          │          │
              ▼          ▼          ▼
         XPManager   CurrencyMgr  AmmoManager
```

Additional reward types route through their corresponding systems:

```text
QuestReward
    │
    ├── XP          → XPManager
    ├── CURRENCY    → CurrencyManager
    ├── AMMO        → AmmoManager
    ├── EQUIPMENT   → InventoryManager
    ├── ITEM        → InventoryManager
    ├── CONSUMABLE  → ConsumableManager
    ├── POTION      → PotionManager
    ├── GEM         → GemManager
    ├── METAL       → MetalManager
    ├── MINERAL     → MineralManager
    ├── INGREDIENT  → IngredientManager
    ├── STONE       → StoneManager
    └── QUEST       → QuestManager
```

Unsupported reward types generate a warning rather than being silently ignored.

---

# ⭐ Experience Rewards

```gdscript
func _grant_xp(amount: int) -> void
```

XP rewards are routed through `XPManager`.

The Reward Manager first resolves the player and their `LevelComponent`.

```text
Quest Reward
     │
     ▼
RewardManager
     │
     ▼
Player LevelComponent
     │
     ▼
XPManager.grant_xp()
     │
     ▼
XP Gain / Level Progression
```

The Reward Manager does not calculate XP modifiers itself.

`XPManager` remains responsible for XP application and XP gain modifiers.

This keeps reward routing separate from progression logic.

---

# 💰 Currency Rewards

```gdscript
func _grant_currency(currency_id: String, amount: int) -> void
```

Currency rewards are routed to `CurrencyManager`.

Supported currency identifiers include:

* `coin`
* `gem`
* `token`

Common aliases are normalized before routing.

For example:

```text
gold_coin
gold
coins
    ↓
coin
    ↓
CurrencyManager.add_coin()
```

This allows reward definitions to use compatible identifiers without requiring separate reward implementations.

---

# 🏹 Ammunition Rewards

```gdscript
func _grant_ammo(ammo_id: String, amount: int) -> void
```

Ammunition rewards are routed through `AmmoManager`.

Supported identifiers currently include:

```text
arrow
arrows
arrow_wood
        ↓
AmmoManager.add_arrows()

bomb
bombs
bomb_basic
        ↓
AmmoManager.add_bombs()
```

Unknown ammunition identifiers generate a warning.

---

# 🎒 Inventory Rewards

Equipment and generic item rewards are routed through `InventoryManager`.

## Equipment

```gdscript
func _grant_equipment(item_id: String) -> void
```

Equipment rewards grant a single inventory item.

```text
Equipment Reward
       ↓
InventoryManager
       ↓
add_item(item_id, 1)
```

## Items

```gdscript
func _grant_item(item_id: String, amount: int) -> void
```

Generic item rewards support a configurable quantity.

```text
Item Reward
     ↓
InventoryManager
     ↓
add_item(item_id, amount)
```

The Reward Manager therefore acts as the bridge between quest reward definitions and the inventory system.

---

# 🍱 Consumable Rewards

```gdscript
func _grant_consumable(
    consumable_id: String,
    amount: int
) -> void
```

Consumable rewards are routed through `ConsumableManager`.

Supported categories include:

* Food
* Drink
* Snack

```text
Consumable Reward
       ↓
RewardManager
       ↓
ConsumableManager
       ↓
Consumable Inventory
```

Unknown consumable identifiers generate a warning.

---

# 🧪 Potion Rewards

```gdscript
func _grant_potion(
    potion_id: String,
    amount: int
) -> void
```

Potion rewards are routed through `PotionManager`.

Supported potion categories include:

* Health
* Mana
* Stamina

Example:

```text
potion_health_small
health
hp
      ↓
PotionManager.add_health_potions()
```

The Reward Manager translates reward identifiers into the corresponding potion manager operation.

---

# 🔷 Material Rewards

Material rewards are delegated to their specialized managers.

```text
Gem       → GemManager
Metal     → MetalManager
Mineral   → MineralManager
Ingredient→ IngredientManager
Stone     → StoneManager
```

Each material retains its own specialized manager rather than requiring the Reward Manager to maintain material storage logic.

For example:

```gdscript
func _grant_gem(gem_id: String, amount: int) -> void:
    GemManager.add_gem(gem_id, amount)
```

The same routing model is used for metals, minerals, ingredients, and stones.

---

# 📜 Quest Rewards

```gdscript
func _grant_quest(quest_id: String) -> void
```

Quest rewards can activate another quest through `QuestManager`.

```text
Quest Completion
      ↓
RewardManager
      ↓
Quest Reward
      ↓
QuestManager.start_quest()
      ↓
New Quest Active
```

This allows quest chains and reward-driven quest activation without embedding quest progression logic inside the Reward Manager.

---

# 🔄 Reward Processing Pipeline

The complete reward pipeline is:

```text
Quest Completed
      │
      ▼
QuestManager
      │
      ▼
RewardManager.grant_rewards()
      │
      ▼
QuestDefinition.rewards
      │
      ▼
For Each QuestReward
      │
      ▼
_apply_reward()
      │
      ├──────────────┬───────────────┬──────────────┐
      ▼              ▼               ▼              ▼
     XP          Currency          Items         Materials
      │              │               │              │
      ▼              ▼               ▼              ▼
 XPManager    CurrencyManager  InventoryManager  Material Managers
```

The Reward Manager therefore functions as a **routing layer**, not as a storage or progression system.

---

# 🔗 System Relationships

```text
                    QuestManager
                         │
                         │ QuestDefinition
                         ▼
                  ┌──────────────┐
                  │ RewardManager│
                  └──────┬───────┘
                         │
                    QuestReward
                         │
        ┌────────────────┼─────────────────┐
        │                │                 │
        ▼                ▼                 ▼
   XP / Currency      Inventory        Materials
        │                │                 │
        ▼                ▼                 ▼
   XPManager       InventoryManager   GemManager
   CurrencyManager                      MetalManager
   AmmoManager                          MineralManager
                                        IngredientManager
                                        StoneManager
```

Additional routing:

```text
RewardManager
     │
     ├── ConsumableManager
     ├── PotionManager
     └── QuestManager
```

---

# 🧭 System Boundaries

| Responsibility     | System             |
| ------------------ | ------------------ |
| Quest completion   | Quest Manager      |
| Reward definition  | `QuestReward`      |
| Reward routing     | Reward Manager     |
| XP application     | XP Manager         |
| Currency storage   | Currency Manager   |
| Ammo storage       | Ammo Manager       |
| Item storage       | Inventory Manager  |
| Consumable storage | Consumable Manager |
| Potion storage     | Potion Manager     |
| Gem storage        | Gem Manager        |
| Metal storage      | Metal Manager      |
| Mineral storage    | Mineral Manager    |
| Ingredient storage | Ingredient Manager |
| Stone storage      | Stone Manager      |
| Quest activation   | Quest Manager      |

The Reward Manager coordinates these systems but does not replace them.

---

# 🌱 Data-Driven Reward Architecture

The reward system separates **reward definition** from **reward execution**.

```text
QuestDefinition
      │
      └── QuestReward[]
              │
              ▼
        RewardManager
              │
              ▼
       Reward Type Router
              │
       ┌──────┴──────┐
       ▼             ▼
 Gameplay Manager   Gameplay Manager
```

Quest resources therefore define reward content without requiring quests to directly know how XP, currency, inventory items, or materials are stored.

This allows additional reward types to be added by extending the reward enum and routing layer rather than rewriting quest completion logic.

---

# 🔁 Quest Completion Integration

The Reward Manager is invoked by the quest completion pipeline.

```text
QuestManager.complete_quest()
            │
            ▼
    RewardManager.grant_rewards()
            │
            ▼
       Apply Rewards
```

This establishes a clean separation:

**Quest Manager**

> Determines that the quest has been completed.

**Reward Manager**

> Determines how each configured reward is routed.

**Gameplay Managers**

> Apply and store the actual reward.

---

# 🚀 Launch Flow Integration

The Reward Manager is initialized as part of the framework's centralized manager initialization.

It remains available as the central reward routing layer throughout gameplay.

Reward processing itself occurs when another system explicitly calls:

```gdscript
RewardManager.grant_rewards(def)
```

This prevents individual quest definitions or UI systems from directly manipulating player rewards.

---

# ✅ Design Rule

**RewardManager is the central authority for routing quest rewards into the appropriate gameplay systems.**

`QuestDefinition` and `QuestReward` define **what is awarded**.

The Reward Manager determines **where the reward goes**.

Specialized managers remain responsible for **actually applying and storing the reward**.

Future reward types should extend this routing pipeline rather than embedding reward application directly into `QuestManager`, quest resources, or UI systems.
