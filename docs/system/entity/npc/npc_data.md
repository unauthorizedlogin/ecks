# NPCData Resource

`NPCData` is the centralized data definition for non-player characters within the RPG framework.

It provides the identity and behavioral configuration used to define an NPC independently from its runtime scene instance.

The resource is intentionally lightweight at its current stage and is designed to expand as additional NPC systems are introduced.

---

## Architecture Role

`NPCData` acts as the **definition layer for NPCs**.

```text
NPCData Resource
       │
       ├── Identity
       └── Behavior
              │
              ▼
        NPC Runtime
              │
       ┌──────┼──────┐
       ▼      ▼      ▼
   Dialogue  Shop   Future NPC Systems
```

The resource defines **what an NPC is and what systems it connects to**.

It does not contain the runtime NPC instance, dialogue execution, shop logic, or other gameplay behavior.

---

# Identity

The Identity section defines the basic presentation and identification of an NPC.

```gdscript
@export var npc_id: String
@export var display_name: String
@export var title: String
@export var portrait: Resource
```

### `npc_id`

The unique identifier for the NPC.

This provides the stable reference used by databases, generators, dialogue systems, quests, and other systems that need to identify a specific NPC.

### `display_name`

The NPC's displayed name.

### `title`

Optional secondary identity information such as:

* Blacksmith
* Merchant
* Guard
* Trainer
* Farmer

### `portrait`

Resource reference used for NPC presentation in UI and dialogue systems.

---

# Behavior

The Behavior section defines which gameplay systems are associated with the NPC.

```gdscript
@export var dialogue_id: String
@export var type: String
@export var shop_id: String
```

### `dialogue_id`

References the dialogue definition associated with the NPC.

The `NPCData` resource does not execute dialogue. The Dialogue System resolves and manages the referenced dialogue.

### `type`

Defines the NPC's broad classification.

This provides a lightweight way to distinguish NPC roles and can be expanded as NPC behavior becomes more specialized.

### `shop_id`

References an associated shop definition.

The NPC resource identifies the shop relationship; shop inventory, pricing, purchasing, and transaction logic remain owned by the Shop System.

---

# Runtime Separation

`NPCData` is a **definition resource**, not an NPC runtime controller.

```text
NPCData
   │
   │ definition
   ▼
NPC Scene / Runtime Instance
   │
   ├── DialogueManager
   ├── Shop System
   └── Future NPC Systems
```

This separation allows multiple runtime instances to reference the same NPC definition without duplicating configuration data.

Runtime systems remain responsible for state and behavior such as:

* Current dialogue state
* Interaction state
* Shop transactions
* Quest participation
* NPC movement
* Runtime schedules
* Other mutable NPC state

---

# Future Expansion

`NPCData` is intended to become the central configuration point for additional NPC capabilities as those systems are implemented.

Potential future integrations include:

```text
NPCData
 ├── Dialogue
 ├── Shop
 ├── Quest
 ├── Crafting
 ├── Jobs
 ├── Training
 ├── Services
 ├── Reputation
 └── Other NPC Interactions
```

These should remain references or configuration data rather than embedding the implementation of those systems inside `NPCData`.

For example, future fields could associate an NPC with:

```text
Crafting Definition
Job Definition
Training Definition
Service Definition
Quest Configuration
```

The corresponding managers and runtime systems would remain responsible for executing those capabilities.

---

# Data-Driven NPC Architecture

The resource allows NPC content to be authored independently from gameplay code.

A generated NPC definition can therefore provide the system with:

```text
NPC ID
   ↓
Identity
   ↓
NPC Type
   ↓
Dialogue Reference
   ↓
Shop Reference
   ↓
Future System References
```

This keeps NPC configuration centralized while allowing individual systems to own their respective gameplay logic.

---

# Design Boundaries

### NPCData owns

* NPC identity
* Display information
* NPC classification
* References to NPC-related systems
* Future NPC capability configuration

### NPCData does not own

* NPC runtime behavior
* Dialogue execution
* Shop transactions
* Crafting execution
* Job execution
* Training execution
* NPC AI
* UI
* Persistent runtime state

Those responsibilities belong to their respective runtime systems.

---

# Summary

`NPCData` is the **central NPC definition resource** used to describe an NPC and connect it to the systems that provide its functionality.

Its current implementation provides:

* NPC identity
* Display name and title
* Portrait
* NPC type
* Dialogue reference
* Shop reference

The resource is intentionally structured for expansion so that future NPC capabilities such as **crafting, jobs, training, services, quests, and other interactions** can be attached to the NPC definition without turning `NPCData` into the implementation layer for those systems.
