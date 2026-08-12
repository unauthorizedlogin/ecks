# 💬 Dialogue Runtime

## Overview

`DialogueRuntime` is the runtime controller for active dialogue sessions.

It manages dialogue state, NPC context, active dialogue blocks, line progression, dialogue modes, and runtime signals.

---

## Architecture

```text
Dialogue Definition
        │
        ▼
 DialogueRuntime
        │
 ┌──────┼────────┐
 ▼      ▼        ▼
State   Lines   Mode
 │       │       │
 ▼       ▼       ├── Flavor
Signals  Advance ├── Quest
                 └── Shop
```

---

## Responsibilities

`DialogueRuntime` handles:

* Starting dialogue sessions
* Resolving the requested dialogue state
* Tracking the active NPC
* Tracking the active dialogue block
* Advancing dialogue lines
* Emitting the current line
* Ending dialogue sessions
* Tracking dialogue mode
* Broadcasting dialogue lifecycle signals

---

## Dialogue Modes

Runtime state distinguishes between:

* **Flavor dialogue**
* **Quest dialogue**
* **Shop dialogue**

These flags allow consuming systems to determine what type of interaction is currently active.

---

## Runtime Flow

```text
Start
  │
  ▼
Resolve State Block
  │
  ▼
Emit Dialogue Started
  │
  ▼
Emit Current Line
  │
  ▼
Advance
  │
  ├── More Lines ──► Emit Next Line
  │
  └── End ─────────► Reset Runtime
```

## Signals

| Signal                  | Purpose                    |
| ----------------------- | -------------------------- |
| `active_state_changed`  | Dialogue activity state    |
| `dialogue_started`      | Dialogue session started   |
| `dialogue_ended`        | Dialogue session ended     |
| `line_changed`          | Current text changed       |
| `dialogue_line_changed` | Full dialogue line changed |

`DialogueRuntime` provides the **active-session state and progression layer**, while dialogue definitions provide the authored content.
