# 🎵 Credits Audio

## Overview

The Credits Audio Controller manages the music playback used by the Credits scene.

It maintains a configurable list of `AudioStream` resources, starts the first track when the scene loads, and automatically advances through the playlist when a song finishes.

---

## Architecture Role

```text
Credits Scene
     │
     ├── AudioPlayer
     │
     └── Credits Audio Controller
              │
              └── songs[]
                    │
                    ▼
              AudioPlayer
```

The controller manages **playback state**, while the `AudioPlayer` node handles the actual audio playback.

---

## Responsibilities

* Store the Credits music playlist.
* Track the currently playing song.
* Start playback when the Credits scene initializes.
* Change the `AudioPlayer` stream when selecting a track.
* Start the selected track.
* Detect when a track finishes.
* Automatically advance to the next track.
* Loop back to the first track after the final song.

---

## Playlist Configuration

The playlist is exposed directly through:

```gdscript
@export var songs: Array[AudioStream] = []
```

This allows Credits music to be configured from the Godot Inspector without hardcoding individual tracks into the controller.

The current track is tracked using:

```gdscript
var current_song_index := 0
```

---

## Playback Flow

When the controller initializes:

```text
_ready()
   │
   ├── Songs available?
   │       │
   │       └── Yes
   │            ▼
   │       play_song(0)
   │
   └── Connect AudioPlayer.finished
```

`play_song()` assigns the selected `AudioStream` to the player and starts playback.

```text
songs[index]
     │
     ▼
AudioPlayer.stream
     │
     ▼
AudioPlayer.play()
```

---

## Automatic Playlist Advancement

The controller listens for the `AudioPlayer.finished` signal.

When a track finishes:

```text
AudioPlayer
    │
    │ finished
    ▼
_on_audio_finished()
    │
    ▼
Increment song index
    │
    ▼
Wrap index when necessary
    │
    ▼
play_song(next_index)
```

The modulo operation:

```gdscript
(current_song_index + 1) % songs.size()
```

creates a continuous playlist loop:

```text
Track 1 → Track 2 → Track 3 → Track 1 → ...
```

---

## Track Selection

`play_song(index)` validates the requested index before changing playback:

```gdscript
if index >= 0 and index < songs.size():
```

When valid:

1. Updates `current_song_index`.
2. Assigns the corresponding `AudioStream`.
3. Starts playback.

This keeps playlist state synchronized with the actual track being played.

---

## Architectural Boundary

| Component                | Responsibility                   |
| ------------------------ | -------------------------------- |
| Credits Audio Controller | Playlist and playback sequencing |
| `AudioPlayer`            | Actual audio playback            |
| Credits Scene            | Provides the AudioPlayer node    |
| `songs` export           | Defines available Credits music  |

### Key Principle

**The Credits Audio Controller is a lightweight playlist controller: it owns track sequencing and delegates actual playback to the scene's `AudioPlayer`.**
