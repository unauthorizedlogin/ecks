# 📋 ItemRequirement Architecture

## Overview

`ItemRequirement` is a supporting resource used by `ItemResource` to describe item restrictions.

It provides a generic, reusable representation of a requirement without embedding requirement evaluation logic into the item resource.

The requirement defines **what must be satisfied**; the system evaluating the requirement determines **whether it is satisfied**.

---

## Requirement Model

Each requirement consists of three primary components:

```text
ItemRequirement
    │
    ├── RequirementType
    ├── ID
    └── Amount
```

`RequirementType` determines how the requirement should be interpreted.

`id` identifies the class, stat, quest, or item involved.

`amount` provides the numeric threshold where the requirement type requires one.

This gives the requirement system a common data structure across different restriction categories.

---

## Requirement Types

### Class

Identifies a required class or class-related definition.

### Level

Defines a minimum or relevant level threshold through `amount`.

### Stat

Identifies a required statistic through `id` and its required value through `amount`.

### Quest

Identifies a quest-related prerequisite through `id`.

### Equipped Item

Identifies another item that must be equipped.

The architecture can be extended with additional requirement types without changing the fundamental `ItemResource` requirement relationship.

---

## Separation of Definition and Evaluation

`ItemRequirement` intentionally contains no logic for determining whether the requirement has been met.

This is an important architectural boundary.

```text
ItemResource
     │
     ▼
ItemRequirement
     │
     │ requirement definition
     ▼
Requirement / Equipment / Character Systems
     │
     │ evaluate
     ▼
Allowed / Denied
```

This keeps item resources declarative and prevents requirement-specific gameplay logic from being embedded into individual item definitions.
