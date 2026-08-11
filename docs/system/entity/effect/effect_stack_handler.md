# 🧬 Effect Stack Handler

The Effect Stack Handler manages **stacking and duration behavior** for active `StatEffect` instances.

It is a lightweight runtime helper used by the Effect System to determine how a new effect interacts with an existing matching effect.

---

## 🎯 Responsibilities

The Effect Stack Handler is responsible for:

* Finding matching active effects
* Resolving stack behavior
* Enforcing maximum stack counts
* Resolving duration behavior
* Returning the resulting stack and duration state

It does **not** apply effects, modify stats, or manage effect lifecycles.

---

## 📚 Stack Behavior

Effects can define how repeated applications are handled.

| Behavior  | Result                            |
| --------- | --------------------------------- |
| `NONE`    | Resets to one stack               |
| `ADD`     | Adds one stack up to `max_stacks` |
| `REPLACE` | Replaces existing stacks with one |
| `IGNORE`  | Prevents the new application      |

---

## ⏱️ Duration Behavior

Effects can independently define how repeated applications affect duration.

| Behavior  | Result                                          |
| --------- | ----------------------------------------------- |
| `NONE`    | Keeps the current duration                      |
| `KEEP`    | Keeps the existing remaining duration           |
| `REFRESH` | Resets duration to the effect duration          |
| `ADD`     | Adds the new duration to the remaining duration |
| `REPLACE` | Replaces the remaining duration                 |

Stack and duration behavior are resolved independently, allowing combinations such as:

```text
ADD stacks + REFRESH duration
ADD stacks + KEEP duration
REPLACE stacks + REPLACE duration
IGNORE application
```

---

## 🔎 Effect Matching

Effects are matched using:

* Target
* `stack_group`

Effects without a `stack_group` do not participate in stack matching.

```text
Target
  +
Stack Group
  ↓
Matching Active Effect
  ↓
Stack / Duration Resolution
```

---

## 🔄 Runtime Flow

```text
New StatEffect
      │
      ▼
Find Matching Effect
      │
      ├── No Match → New Effect
      │
      └── Match
            │
            ▼
      Stack Resolution
            │
            ▼
      Duration Resolution
            │
            ▼
      Updated Effect State
```

The handler returns the resolved state to the calling Effect System rather than modifying the active effect directly.
