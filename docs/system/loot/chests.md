## 📦 Chest Loot System

The Chest Loot System provides interactive world containers for both item rewards and persistent player storage.

The system currently supports two distinct chest types:

* Dynamic Loot Chests
* Storage Chests

These share the same world interaction foundation while serving different gameplay purposes.

---

# 🎁 Dynamic Loot Chests

Dynamic Loot Chests are world containers designed to provide one-time rewards.

Responsibilities:

* Detect nearby player interaction
* Play opening animations
* Spawn assigned loot objects
* Trigger quest interaction events
* Prevent duplicate reward spawning

When opened, the chest:

1. Validates player interaction
2. Marks itself as opened
3. Plays the chest animation
4. Spawns the assigned item scene
5. Sends interaction events to the quest system

---

## 🗝️ Quest Integration

Dynamic chests support quest objectives through interaction events.

When opened, they notify the Quest System:

```gdscript
ObjectiveType.INTERACT
```

with the configured:

```gdscript
interact_id
```

This allows chest interactions to be used for objectives such as:

* Open the chest
* Recover an item
* Search a location
* Discover hidden rewards

---

# 🏦 Storage Chests

Storage Chests provide persistent inventory containers for player storage.

Unlike dynamic loot chests, storage chests do not spawn rewards. They connect directly into the inventory system.

Responsibilities:

* Open shared storage UI
* Load stored inventory data
* Provide item add/remove access
* Persist contents by chest ID
* Manage player interaction state

Each storage chest is identified through:

```gdscript
chest_id
```

which allows individual containers to maintain their own inventory.

---

# 📦 Storage Inventory Integration

Storage chests communicate with:

* InventoryManager
* UIManager
* Player Inventory UI

Opening a storage chest:

1. Initializes the chest inventory
2. Disables player movement
3. Opens the inventory interface
4. Opens the storage container view

Closing the chest:

1. Closes storage UI
2. Closes player inventory
3. Restores player movement

---

# 🖱️ Interaction Support

Storage chests support both:

* Proximity interaction
* Mouse-based interaction

The system exposes:

```gdscript
mouse_interact()
```

allowing integration with cursor-based ARPG interactions.

---

# 🎨 Animation Support

Both chest types support animated states through:

```gdscript
AnimatedSprite2D
```

Current supported states:

* Closed
* Open

Animations are triggered automatically based on interaction state.

---

# 🧠 Runtime Design

The chest system intentionally separates responsibilities:

### Dynamic Loot Chest

Handles:

* Reward spawning
* One-time world loot
* Quest interactions

### Storage Chest

Handles:

* Persistent inventory storage
* Player item management
* Inventory UI integration

This separation allows future expansion for:

* Randomized loot tables
* Locked containers
* Keys and requirements
* Mimic encounters
* Shared stash systems
* Dungeon-specific rewards

---

# 📌 Summary

The Chest Loot System provides the foundation for world containers by separating temporary loot rewards from persistent player storage.

Dynamic chests support exploration rewards and quest objectives, while storage chests provide permanent inventory management through the game's inventory architecture.
