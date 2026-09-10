# 🌫️ World Fog of War

The **World Fog of War** system provides persistent exploration and dynamic player vision across the world map.

It separates **persistent exploration state** from **current player visibility**, allowing explored areas to remain revealed after the player moves while the active vision radius continues to update around the player.

---

# Architecture Role

`WorldFogOfWar` is a **world-level rendering and visibility system**.

### Responsibilities

* Define the world-space fog area.
* Maintain exploration and visibility state.
* Convert player Vision into a world-space reveal radius.
* Track current player visibility.
* Persist explored regions between sessions.
* Render fog using chunked textures.
* Apply softened exploration boundaries.
* Update only affected fog regions when vision changes.
* Support permanent area reveals independent of player vision.

### Does Not Own

* Player statistics.
* Player movement.
* Vision stat calculation.
* Save-file management.
* Map generation.
* Minimap UI.

**Data flow:**

`Player / Stat System → WorldFogOfWar → Fog State → Chunk Textures → World Rendering`

---

# World Configuration

The system defines a configurable world-space fog region using:

* `world_origin`
* `world_size`
* `cell_size`

The configured world area is converted into a grid of fog cells.

Each cell maintains independent state for exploration and visibility.

---

# Fog State

The system maintains three separate masks:

| State              | Purpose                                                          |
| ------------------ | ---------------------------------------------------------------- |
| `_explored`        | Persistent record of areas discovered by the player              |
| `_explored_visual` | Persistent softened visual representation of explored boundaries |
| `_visible`         | Temporary current-player vision                                  |

### Persistent Exploration

`_explored` records whether a cell has been discovered.

Once revealed, exploration is retained and serialized.

### Persistent Visual Exploration

`_explored_visual` stores the softened reveal value produced when an area is explored.

Unlike current visibility, this mask is **not cleared when the player moves away**.

This preserves smooth exploration boundaries rather than reverting previously explored terrain to hard cell edges.

### Current Visibility

`_visible` represents the player's active vision area.

When the player moves, the previous vision region is cleared and the new vision region is calculated.

---

# Vision System

`update_player_vision()` receives:

* World position
* Vision value

The Vision value is converted into a world-space radius.

Vision updates are distance-gated through `vision_update_distance` to avoid recalculating the fog for insignificant player movement.

The system updates the combined dirty region covering both:

* Previous vision
* New vision

This ensures stale visibility is cleared while minimizing texture work.

---

# Vision Softness

`vision_softness` controls the radial falloff between fully revealed and fully obscured areas.

The reveal calculation provides:

* Full visibility inside the inner radius.
* Gradual falloff through the softness region.
* Full fog outside the vision radius.

The same radial falloff is retained in `_explored_visual`, allowing explored boundaries to remain visually softened after the player's current vision moves elsewhere.

---

# Permanent Area Revelation

`reveal_area()` provides an independent reveal operation.

This is intended for systems that need to permanently expose an area without representing active player vision.

Examples include:

* Map discoveries.
* World landmarks.
* Scripted exploration events.
* Revealed regions.
* Future map/world systems.

Permanent reveals update exploration state but do not create current player visibility.

---

# Chunked Rendering

The fog is rendered using fixed-size chunks rather than one large world texture.

### Constants

* `FOG_CHUNK_SIZE = 64`
* `FOG_RENDER_SCALE = 4`

Each chunk maintains:

* `Image`
* `ImageTexture`
* `Sprite2D`

Chunks are positioned directly in world space.

Only chunks intersecting an updated fog region are regenerated.

This architecture prevents player vision changes from requiring the entire world fog texture to be rebuilt.

---

# Incremental Initialization

Fog chunks are initialized progressively.

The first chunk is prepared immediately, while remaining chunks are initialized through deferred processing across frames.

This prevents large world fog maps from forcing their entire texture workload into a single frame during initialization.

---

# Fog Rendering

Each fog cell resolves its final visual state from the highest-priority applicable condition:

1. Persistent explored visual state.
2. Current player visibility.
3. Completely unexplored fog.

The resulting alpha is derived from the stored reveal value.

Optional `FastNoiseLite` modulation can be applied to the fog to introduce subtle visual variation.

---

# Texture Updates

`_update_texture_region()` determines which fog chunks intersect a changed region.

Only those chunks are rebuilt and uploaded to their associated `ImageTexture`.

This allows vision updates to remain localized instead of rebuilding the complete fog map.

---

# Save / Load

Fog exploration is integrated with the save system through:

```text
serialize_fog()
deserialize_fog()
```

Persisted data includes:

* `_explored`
* `_explored_visual`

Current visibility is intentionally **not persisted**.

When loading:

* Persistent exploration is restored.
* Persistent visual exploration is restored when available.
* Current visibility is cleared.
* Vision tracking is reset.
* Fog textures are rebuilt.

This ensures the player's active vision is recalculated from their current runtime position rather than restored as stale session state.

---

# Architecture Notes

* Fog state is maintained independently from UI/minimap presentation.
* World-space coordinates are the authoritative position system.
* Exploration is persistent; current visibility is transient.
* Explored visual softness is persistent rather than purely a rendering-time effect.
* Vision updates are distance-gated for performance.
* Rendering is chunk-based and region-updated.
* Fog resolution is controlled independently through `cell_size`.
* The system supports both dynamic player vision and scripted permanent reveals.
* Save serialization stores exploration state without coupling the fog system to `SaveManager` implementation details.

### File

`WorldFogOfWar`
`system/world/.../world_fog_of_war.gd`
