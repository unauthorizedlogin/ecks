# 📦 Item Removal Manager Architecture

## Overview

The Item Removal Manager is the centralized **item consumption and removal routing layer** for the game's item ecosystem.

It provides a unified interface for removing a requested quantity of an item without requiring calling systems to know which specialized manager owns that item's inventory state.

The manager resolves the item's definition through `ItemDatabase`, determines its `ItemType`, and delegates the removal operation to the appropriate authoritative manager.

It is therefore the mutation-side counterpart to the Item Count Manager's quantity-resolution role.

---

## Architectural Role

`ItemRemovalManager` acts as an **item removal gateway** between gameplay systems and the specialized managers responsible for item ownership.

```text
                 Gameplay System
                       │
                       │ item_id + amount
                       ▼
              ItemRemovalManager
                       │
                       ▼
                 ItemDatabase
                       │
                       ▼
                  ItemResource
                       │
                    ItemType
                       │
          ┌────────────┼────────────┐
          ▼            ▼            ▼
       Currency      Inventory    Specialized
       Manager       Manager      Managers
```

The manager does not own the item being removed. It determines **where the removal belongs** and delegates the operation.

---

## Primary Interface

The primary public entry point is:

```text
process_removal_by_id(item_id, amount)
```

This provides a simple item-ID-based interface for systems that know what item they need to remove but should not need knowledge of the item's storage implementation.

A second entry point accepts an already-resolved `ItemResource`:

```text
process_removal(item, amount)
```

This allows callers that already possess the authoritative item definition to bypass a redundant database lookup.

Both entry points converge on the same routing architecture.

---

## Item Resolution

When an item is supplied by ID, the manager resolves it through `ItemDatabase`.

```text
item_id
   │
   ▼
ItemDatabase
   │
   ▼
ItemResource
   │
   ▼
ItemType
   │
   ▼
Removal Router
```

`ItemDatabase` remains authoritative for item identity and classification.

The Item Removal Manager does not maintain its own item-definition registry.

Invalid IDs and missing `ItemResource` data are rejected before any removal operation is attempted.

---

## Removal Routing

The item's `ItemType` determines which subsystem receives the removal request.

```text
Item Type
    │
    ├── AMMO ──────────► AmmoManager
    ├── CONSUMABLE ────► ConsumableManager
    ├── CURRENCY ──────► CurrencyManager
    ├── EQUIPMENT ─────► InventoryManager
    ├── GEM ───────────► GemManager
    ├── INGREDIENT ────► IngredientManager
    ├── ITEM ───────────► InventoryManager
    ├── METAL ──────────► MetalManager
    ├── MINERAL ───────► MineralManager
    ├── POTION ────────► PotionManager
    └── STONE ──────────► StoneManager
```

Unsupported categories are explicitly rejected rather than silently routed.

Quest items are intentionally excluded from generic removal because their removal may be governed by quest-specific state and rules.

Miscellaneous items currently have no generic removal implementation.

---

## Ownership Model

The Item Removal Manager does not become the owner of inventory quantities.

The specialized managers remain authoritative:

| Item Domain   | Removal Owner       |
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

The Item Removal Manager simply provides a unified mutation interface across these domains.

---

## Identifier Normalization

Some item domains use multiple identifiers for the same logical item.

The removal layer handles these aliases at the routing boundary.

For example, currency and ammunition identifiers may have singular, plural, legacy, or descriptive variants that resolve to the same specialized manager operation.

This allows gameplay systems to work with the unified item IDs exposed by the item-definition layer without requiring them to understand the internal identifiers used by each specialized manager.

---

## Mutation Boundary

The Item Removal Manager establishes an important architectural boundary:

```text
Gameplay
   │
   ▼
ItemRemovalManager
   │
   ▼
Authoritative Inventory Manager
```

Gameplay systems request **what should be removed**, while the owning manager determines **how the underlying inventory state is mutated**.

This prevents gameplay systems from directly manipulating multiple independent inventory systems.

For example, a gameplay system does not need to know whether removing a potion requires:

* Decrementing a stack.
* Calling a specialized potion method.
* Performing multiple individual removals.
* Updating another subsystem.

Those details remain behind the manager boundary.

---

## Validation

The manager performs basic request validation before routing:

* Item ID must resolve through `ItemDatabase`.
* A valid `ItemResource` must exist.
* The item must not be null.
* Removal amount must be greater than zero.
* The item type must have a supported removal route.

Invalid requests are rejected without attempting mutation.

This keeps invalid item-removal requests from propagating into specialized inventory systems.

---

## Specialized Removal Behavior

Although the public interface is unified, the underlying managers are free to implement their own removal semantics.

This is particularly relevant for item domains whose internal representation differs from a normal inventory stack.

For example, potion removal may require repeated specialized removal operations rather than a single quantity-based inventory call.

The Item Removal Manager abstracts that difference from callers.

The architectural contract is therefore:

```text
remove X of item Y
```

rather than:

```text
perform the exact internal inventory operation required by item domain Y
```

---

## Initialization

The manager uses an explicit initialization lifecycle through `initialize()` and guards initialization with `is_initialized`.

Unlike the Item Count Manager, its current initialization phase does not require signal registration or manager discovery.

The initialization boundary exists so the manager conforms to the broader manager lifecycle architecture and can acquire additional startup responsibilities without changing its public lifecycle contract.

---

## Architectural Boundaries

### ItemRemovalManager Owns

* Unified item-removal entry points.
* Item definition resolution.
* Item-type-based routing.
* Identifier normalization required for routing.
* Validation of generic removal requests.
* Rejection of unsupported item categories.

### ItemRemovalManager Delegates To

**ItemDatabase**

* Item definition and type resolution.

**CurrencyManager**

* Currency mutation.

**AmmoManager**

* Ammunition mutation.

**InventoryManager**

* Equipment and general inventory item removal.

**Specialized Item Managers**

* Consumable, potion, gem, metal, mineral, ingredient, and stone removal.

### ItemRemovalManager Does Not Own

* Inventory quantities.
* Currency balances.
* Inventory slots.
* Item persistence.
* Item definitions.
* Quest-item lifecycle.
* Specialized inventory implementation details.

---

## Relationship to Item Count Manager

`ItemCountManager` and `ItemRemovalManager` form complementary aggregation layers around the same specialized inventory architecture.

```text
                    ItemDatabase
                         │
              ┌──────────┴──────────┐
              │                     │
              ▼                     ▼
      ItemCountManager       ItemRemovalManager
              │                     │
              │ read                │ mutate
              ▼                     ▼
        Specialized Inventory Managers
              │                     │
              └──────────┬──────────┘
                         ▼
                 Authoritative State
```

### Item Count Manager

Answers:

> **How many of this item do I have?**

### Item Removal Manager

Performs:

> **Remove this amount of this item.**

Neither manager replaces the specialized inventory systems. They provide centralized interfaces over them.

---

## Architectural Principle

The Item Removal Manager exists to prevent **item-storage implementation knowledge from leaking into gameplay systems**.

A gameplay system should not need to contain routing such as:

```text
if currency:
    CurrencyManager

elif potion:
    PotionManager

elif gem:
    GemManager

...
```

Instead, it submits a domain-independent request:

```text
ItemRemovalManager.process_removal_by_id(item_id, amount)
```

The removal manager resolves the item's identity, determines its owning subsystem, and delegates the mutation.

This establishes a single, extensible **item removal gateway** across the game's heterogeneous inventory architecture.
