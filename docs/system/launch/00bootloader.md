# 🧩 Bootloader

The Bootloader is the first runtime entry point.

Responsibilities:

* Sets initial game state
* Loads required startup databases
* Initializes core definitions
* Applies user settings
* Handles initial scene routing
* Controls transition effects

The bootloader supports two launch modes:

### Title Screen

Loads the standard player-facing entry flow.

### Injector Mode

Skips menus and directly loads the game world for testing or development workflows.
