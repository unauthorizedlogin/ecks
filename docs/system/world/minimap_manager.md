# 🗺️ MiniMap Manager

## Manager Overview

`MiniMapManager` is the **central runtime manager for the minimap system**.

It coordinates the minimap's specialized controllers while providing a single manager-level interface for:

* Minimap attachment and lifecycle
* Camera and player synchronization
* Map geometry construction
* NPC registration and markers
* Quest objective and quest-offer markers
* Storage and treasure markers
* Minimap fog/reveal state
* Expanded/collapsed minimap state
* Quest-driven marker synchronization

The manager acts as the **orchestration layer**. Specialized minimap behavior is delegated to dedicated controllers rather than being implemented directly inside the manager.

---

## Architecture

`MiniMapManager` coordinates five specialized components:

| Component                 | Responsibility                                         |
| ------------------------- | ------------------------------------------------------ |
| `MiniMapLoader`           | Attaches the minimap and builds map geometry           |
| `MiniMapCameraController` | Owns minimap camera configuration and updates          |
| `MiniMapPlayerController` | Synchronizes the minimap with the player               |
| `MiniMapMarkerController` | Creates, updates, removes, and manages minimap markers |
| `MiniMapFogController`    | Handles minimap fog/reveal operations                  |

The manager maintains references to these controllers and routes public minimap operations to the appropriate component.

```text
MiniMapManager
│
├── MiniMapLoader
│   └── Map geometry
│
├── MiniMapCameraController
│   └── Camera profile / camera state
│
├── MiniMapPlayerController
│   └── Player synchronization
│
├── MiniMapMarkerController
│   ├── NPC markers
│   ├── Objective markers
│   ├── Storage markers
│   ├── Treasure markers
│   └── Quest marker overrides
│
└── MiniMapFogController
    └── Fog / reveal state
```

---

# 🧩 Manager Responsibilities

## Minimap Lifecycle

The manager owns the active minimap reference and coordinates attaching or detaching it from every minimap subsystem.

### `attach_minimap()`

Attaches a `Control` minimap to all five controllers, rebuilds map geometry, flushes pending markers, and requests quest marker synchronization.

### `detach_minimap()`

Detaches the minimap from every controller and clears the manager's minimap reference.

### `build_map_geometry()`

Delegates map geometry construction to `MiniMapLoader`.

---

# 🎥 Camera Coordination

Camera functionality is delegated to `MiniMapCameraController`.

The manager exposes:

* `set_camera_profile()`
* `get_camera_id()`
* `get_camera()`
* `has_camera()`

During `_process()`, the manager updates the minimap camera through the camera controller.

The minimap therefore uses the existing camera-profile architecture rather than maintaining camera logic directly inside `MiniMapManager`.

---

# 🧍 Player Synchronization

`MiniMapManager` listens for `PlayerManager.player_ready`.

When the player becomes available, `attach_player()` passes the player reference to:

* `MiniMapPlayerController`
* `MiniMapCameraController`

The manager also detects an already-existing player during initialization.

`clear_player()` removes the player from both controllers.

---

# 🗺️ NPC Registration

The manager maintains:

```gdscript
var npc_nodes: Dictionary = {}
```

NPCs are registered by their `npc_id`.

`register_npc()`:

1. Validates the NPC.
2. Extracts its `npc_id`.
3. Stores the NPC in `npc_nodes`.
4. Creates a normal NPC marker unless a quest marker currently overrides it.
5. Requests deferred quest-marker synchronization.

The manager also provides:

* `get_npc()`
* `add_npc_marker()`
* `remove_npc_marker()`
* `update_npc_marker()`

NPC marker creation itself is delegated to `MiniMapMarkerController`.

---

# 🎯 Marker Interface

`MiniMapManager` provides the public marker API while `MiniMapMarkerController` owns the actual marker implementation.

### NPC Markers

```text
add_npc_marker()
remove_npc_marker()
update_npc_marker()
```

### Objective Markers

```text
add_objective_marker()
remove_objective_marker()
update_objective_marker()
```

Objective markers support:

* Objective ID
* Icon
* Marker size
* Associated NPC ID

### Storage Markers

```text
add_storage_marker()
remove_storage_marker()
update_storage_marker()
```

### Treasure Markers

```text
add_treasure_marker()
remove_treasure_marker()
update_treasure_marker()
```

---

# 🎯 Quest Marker Synchronization

`MiniMapManager` integrates the minimap with `QuestManager`.

It listens for:

* `quest_started`
* `quest_completed`
* `quest_canceled`

Quest marker synchronization rebuilds the active quest marker state rather than incrementally trying to determine every individual marker change.

```text
QuestManager
     │
     ├── quest_started
     ├── quest_completed
     └── quest_canceled
             │
             ▼
     MiniMapManager
             │
             ▼
     sync_quest_markers()
             │
       ┌─────┴──────────────┐
       ▼                    ▼
Active Objectives       Quest Offers
       │                    │
       └─────────┬──────────┘
                 ▼
          NPC Marker Sync
```

A deferred synchronization guard prevents multiple requests from producing redundant synchronization passes:

```gdscript
_quest_marker_sync_pending
```

---

# 🧭 Active Quest Markers

`_sync_active_quest_markers()` scans all active quests and evaluates their objectives.

An objective is skipped when:

* It has no valid state.
* It is already completed.
* Its objective gate has not been unlocked.
* It has no target.
* Its target cannot be located.
* Its marker icon is invalid.

Unlocked objectives are converted into minimap markers using:

* Story marker icon/size for story quests
* Active marker icon/size for other quest categories

Marker IDs are generated from the quest and objective:

```text
quest_<quest_id>_<objective_id>
```

---

# 📍 Quest Target Resolution

`MiniMapManager` resolves objective targets based on objective type.

| Objective Type   | Target Resolution                     |
| ---------------- | ------------------------------------- |
| `TALK`           | NPC by `target_id`                    |
| `TURN_IN`        | NPC by `turn_in_npc_id`               |
| `PURCHASE`       | NPC containing matching `shop_id`     |
| `QUEST_START`    | NPC referenced by quest dialogue keys |
| `QUEST_COMPLETE` | NPC offering the referenced quest     |
| `INTERACT`       | World quest target                    |
| `TRAVEL`         | World quest target                    |
| `COLLECT`        | World quest target                    |
| `KILL`           | World quest target                    |

This keeps quest marker generation independent from the individual quest objective implementations.

---

# 🧑‍🤝‍🧑 NPC Quest Marker Overrides

Quest objective markers can override normal NPC markers.

`MiniMapMarkerController.quest_override_npcs` tracks NPCs whose standard marker should not be recreated.

During synchronization:

1. Existing objective markers are removed.
2. Quest NPC overrides are cleared.
3. Active quest markers are rebuilt.
4. Quest offer markers are rebuilt.
5. Normal NPC markers are restored only where no quest marker override exists.

This ensures a quest-specific marker replaces the generic NPC marker rather than rendering both simultaneously.

---

# 📜 Quest Offer Markers

`_sync_quest_offer_markers()` reads active quest offers from `QuestManager`.

For each active offer:

1. Resolve the offering NPC.
2. Retrieve the minimap quest-available icon.
3. Create an objective-style marker using:

```text
offer_<npc_id>
```

This allows available quests to appear on NPC locations before the quest is accepted.

---

# 🌫️ Fog / Reveal

Fog functionality is delegated entirely to `MiniMapFogController`.

The manager exposes:

```gdscript
reveal_minimap_area(world_position, radius)
```

The manager does not own fog rendering or reveal implementation.

---

# 🔍 World Target Discovery

For non-NPC objectives, `_find_world_target()` searches the `quest_targets` group.

Targets may identify themselves through either:

```gdscript
target_id
```

or:

```gdscript
id
```

This allows world objects to expose a quest-target identity without requiring the minimap manager to know their specific implementation.

---

# 🔄 Runtime Update

`_process()` performs only the runtime updates required by the active minimap:

```text
MiniMapManager
    │
    ├── CameraController.update()
    └── PlayerController.update()
```

The manager does not directly manipulate camera positioning or player tracking.

Marker management is event-driven and handled through explicit marker synchronization/update calls.

---

# 🔗 System Dependencies

`MiniMapManager` integrates with:

* `PlayerManager`
* `QuestManager`
* `MiniMapLoader`
* `MiniMapCameraController`
* `MiniMapPlayerController`
* `MiniMapMarkerController`
* `MiniMapFogController`
* `CameraProfile`
* `QuestDefinition`
* `QuestInstance`
* `ObjectiveDefinition`
* `ObjectiveState`
* `VendorComponent`
* Minimap scene configuration

---

# 🧠 Architecture Role

`MiniMapManager` is the **orchestration layer of the minimap architecture**.

It does not own the implementation of individual minimap subsystems. Instead, it:

* Maintains the active minimap and player references
* Coordinates specialized controllers
* Provides the public minimap API
* Bridges `QuestManager` with minimap markers
* Resolves quest objectives into world/NPC targets
* Coordinates marker synchronization
* Controls minimap expansion state

This keeps the manager focused on **system coordination and integration**, while camera, loading, player tracking, markers, and fog remain isolated in their respective controllers.
