# 🎁 Dialogue Offer Controller

## Overview

`QuestOfferController` manages the runtime state of a quest offer during NPC dialogue.

It tracks which quest is being offered, which NPC is offering it, and whether the interaction is currently in conversation or has reached the offer state.

## Architecture

```text
NPC Dialogue
     │
     ▼
QuestOfferController
     │
     ├── Quest ID
     ├── NPC ID
     └── Offer State
            │
            ├── NONE
            ├── IN_CONVERSATION
            └── OFFERED
```

## Responsibilities

* Begin a quest offer
* Track the active quest/NPC pairing
* Transition the offer to `OFFERED`
* Clear the active offer
* Provide the current offer data

The controller stores **runtime offer state only**. Quest definitions and dialogue resources remain responsible for authored quest and dialogue data.
