# 🎮 New Game Initialization

The Start Menu handles creation of a new game session.

Before starting, the player selects:

* Player name
* Character class
* Difficulty

Validation ensures required selections are completed before beginning the game.

Once confirmed, the selected values are passed into the game runtime:

```
Player Name
Class ID
Difficulty ID
```

These become the initial session parameters used by downstream systems.
