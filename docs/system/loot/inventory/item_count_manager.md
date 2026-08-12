# 📦 Item Count Manager Architecture

## Overview

The Item Count Manager is the centralized **item quantity resolution layer** for the game's item ecosystem.

It provides a single interface for querying how many of a given item the player currently possesses, regardless of which underlying inventory or specialized item manager owns that quantity.

The manager abstracts the differences between currencies, ammunition, inventory items, consumables, potions, equipment, gems, metals, minerals, ingredients, and stones.

Rather than requiring consumers to know which manager owns a particular item type, they provide an `item_id` to `ItemCountManager` and receive the resolved quantity.

---

## Architectural Role

`ItemCountManager` acts as an **aggregation and routing layer** between the unified item identity system and the specialized managers that own item quantities.

```text
                    ItemDatabase
                         │
                         │ item_id → ItemResource
                         ▼
                 ItemCountManager
                         │
              ┌──────────┼──────────┐
              │          │          │
              ▼          ▼          ▼
        CurrencyManager  AmmoManager  InventoryManager
              │
        ┌─────┼─────┬─────────┬──────────┐
        ▼     ▼     ▼         ▼          ▼
      Potion  Gem  Metal    Mineral   Ingredient
                                      │
                                      ▼
                                    Stone
```

The manager does **not** become the owner of these quantities. It resolves the correct owner and delegates the query.

---

## Core Responsibility

The primary public interface is:

```text
get_item_count(item_id)
```

This creates a unified quantity lookup regardless of the item's underlying storage model.

A caller does not need to determine whether an item is:

* Stored in the normal inventory.
* Managed as currency.
* Managed as ammunition.
* Managed as a potion.
* Managed as a material.
* Stored through another specialized item manager.

The Item Count Manager handles that resolution centrally.

---

## Item Identity Resolution

Item identity is resolved through `ItemDatabase` before quantity lookup.

```text
item_id
   │
   ▼
ItemDatabase
   │
   ▼
ItemResource
   │
   └── ItemType
          │
          ▼
   ItemCountManager Router
```

`ItemDatabase` therefore remains the authoritative source for determining what an item is.

`ItemCountManager` uses that classification to determine which subsystem should answer the quantity request.

This prevents item-count consumers from maintaining their own item-type classification logic.

---

## Quantity Ownership

The Item Count Manager intentionally does not maintain a duplicate item-count database.

Instead, quantity remains owned by the appropriate subsystem:

| Item Domain   | Quantity Owner      |
| ------------- | ------------------- |
| Currency      | `CurrencyManager`   |
| Ammunition    | `AmmoManager`       |
| Equipment     | `InventoryManager`  |
| General Items | `InventoryManager`  |
| Consumables   | `ConsumableManager` |
| Potions       | `PotionManager`     |
| Gems          | `GemManager`        |
| Metals        | `MetalManager`      |
| Minerals      | `MineralManager`    |
| Ingredients   | `IngredientManager` |
| Stones        | `StoneManager`      |

This preserves a single source of truth for each inventory domain.

`ItemCountManager` is therefore a **read aggregation layer**, not an inventory owner.

---

## Routing Architecture

The manager uses the `ItemResource.ItemType` as the primary routing mechanism.

```text
Item ID
   │
   ▼
ItemResource
   │
   ▼
ItemType
   │
   ├── CURRENCY ──────► CurrencyManager
   ├── AMMO ──────────► AmmoManager
   ├── EQUIPMENT ─────► InventoryManager
   ├── ITEM ──────────► InventoryManager
   ├── CONSUMABLE ────► ConsumableManager
   ├── POTION ────────► PotionManager
   ├── GEM ───────────► GemManager
   ├── METAL ─────────► MetalManager
   ├── MINERAL ───────► MineralManager
   ├── INGREDIENT ────► IngredientManager
   └── STONE ─────────► StoneManager
```

The router provides the architectural boundary between the unified item model and the specialized storage implementations.

---

## Identifier Normalization

Some specialized managers expose quantities using legacy, alternate, or generalized identifiers.

The Item Count Manager provides normalization at the aggregation boundary where necessary.

For example, multiple external identifiers can resolve to the same currency identity.

This allows callers to use item IDs from the broader item database without requiring every caller to understand historical or alternate identifiers used by specialized managers.

Normalization is therefore an **interface compatibility mechanism**, not a second item database.

---

## Change Notification Architecture

The Item Count Manager subscribes to inventory-change signals from the managers that can affect item quantities.

```text
CurrencyManager ─────┐
AmmoManager ─────────┤
InventoryManager ────┤
PotionManager ───────┤
GemManager ──────────┤
IngredientManager ───┤
MetalManager ────────┤
MineralManager ──────┤
StoneManager ────────┘
             │
             ▼
      ItemCountManager
             │
             ▼
 item_count_changed(item_id)
```

This allows consumers to observe changes through a unified signal rather than connecting individually to every inventory subsystem.

The Item Count Manager therefore serves as an **event aggregation point** as well as a quantity-query router.

---

## Initialization

The manager uses an explicit initialization lifecycle guarded by `is_initialized`.

Initialization establishes its subscriptions to the underlying inventory managers.

The initialization guard prevents duplicate signal connections if initialization is requested more than once.

```text
initialize()
    │
    ├── Already initialized?
    │       └── Yes → return
    │
    └── Connect inventory-change sources
            │
            ▼
        Initialized
```

---

## Event Propagation

Inventory systems remain responsible for reporting their own changes.

The Item Count Manager receives those changes and exposes a common notification channel to consumers.

This creates a simplified dependency model:

```text
Specialized Inventory Systems
          │
          │ change notification
          ▼
   ItemCountManager
          │
          │ unified notification
          ▼
       Consumers
```

Consumers therefore do not need direct knowledge of the underlying inventory manager responsible for a particular item.

---

## Consumer Architecture

The primary consumers of `ItemCountManager` are systems that need **item availability information**, rather than direct ownership or mutation of inventory data.

Examples include systems that need to answer questions such as:

```text
How many of this item does the player have?
```

or:

```text
Did the player's quantity of this item change?
```

These systems can remain independent of the underlying storage implementation.

---

## Architectural Boundaries

### ItemCountManager Owns

* Unified item quantity lookup.
* Item-type routing.
* Identifier normalization required for cross-system lookup.
* Aggregation of inventory-change notifications.
* The public `item_count_changed` notification interface.

### ItemCountManager Delegates To

**ItemDatabase**

* Item identity and item-type resolution.

**Specialized Managers**

* Authoritative quantity retrieval.
* Inventory state.
* Currency state.
* Material state.
* Other domain-specific item storage.

### ItemCountManager Does Not Own

* Item quantities.
* Inventory slots.
* Currency balances.
* Item persistence.
* Item definitions.
* Item creation or destruction.
* Inventory mutation operations.

---

## Architectural Principle

The Item Count Manager exists to prevent **item ownership knowledge from leaking into consumers**.

Without this layer, a consumer needing an item quantity would have to determine the appropriate manager itself:

```text
if currency:
    CurrencyManager

elif potion:
    PotionManager

elif gem:
    GemManager

...
```

With the Item Count Manager:

```text
consumer
    │
    ▼
ItemCountManager.get_item_count(item_id)
    │
    ▼
correct owning manager
```

This establishes a stable, centralized quantity-resolution contract while allowing the underlying inventory architecture to remain specialized.

---

## System Position

The Item Count Manager sits between the game's unified item-definition layer and its specialized inventory managers.

```text
                 ItemDatabase
                      │
                      ▼
              ┌───────────────┐
              │ ItemCount     │
              │ Manager       │
              └───────┬───────┘
                      │
          ┌───────────┼────────────┐
          ▼           ▼            ▼
       Currency    Inventory    Specialized
       Manager     Manager      Item Managers
          │           │            │
          └───────────┴────────────┘
                      │
                      ▼
               Authoritative
                Item Counts
```

Its architectural purpose is to provide **one stable item-count interface over multiple independent inventory domains** without consolidating ownership of those domains into a single manager.
