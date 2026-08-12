# ✏️ Event Viewer Rename Overlay

## Overview

The Rename Overlay is the **inline rename controller for Event Viewer views**.

It provides a temporary `LineEdit` overlay that allows the player to rename the currently active Event Viewer view without leaving the Event Viewer UI.

---

## Architecture Role

```text
EventViewerUI
     │
     │ start(view_id, current_name, position)
     ▼
Rename Overlay
     │
     ├── Accept → rename_requested
     └── Cancel → rename_canceled
```

The overlay handles the **rename interaction**, but does not rename the view itself.

`EventViewerUI` receives the result and passes the new name to `EventManager`.

---

# Responsibilities

The overlay handles:

* Opening the rename field.
* Loading the current view name.
* Positioning the field.
* Selecting the existing name.
* Capturing submitted text.
* Handling focus-loss cancellation.
* Managing typing/movement UI state.
* Signaling open/close events.
* Returning the requested rename to the parent UI.

---

# UI Structure

```text
TabRenameOverlay
└── LineEdit
```

The overlay itself starts hidden.

When activated, the `LineEdit` becomes the temporary input field for the selected view.

---

# Rename Flow

`EventViewerUI` starts the overlay with:

```text
start(view_id, current_name, global_pos)
```

The overlay then:

1. Stores the view ID.
2. Stores the active state.
3. Enables typing mode.
4. Disables player movement.
5. Displays the overlay.
6. Positions it beside the rename control.
7. Loads the current name.
8. Focuses the `LineEdit`.
9. Selects the existing text.

```text
Rename Button
      │
      ▼
start()
      │
      ├── Set View ID
      ├── Enable typing mode
      ├── Disable movement
      ├── Show LineEdit
      ├── Set position
      └── Select existing name
```

---

# Submit

When the user submits the text:

```text
_on_submit(text)
```

the overlay:

* Deactivates itself.
* Hides.
* Restores player movement.
* Clears typing mode.
* Emits `rename_requested`.

```text
LineEdit Submit
      │
      ▼
_on_submit()
      │
      ▼
rename_requested(view_id, new_name)
      │
      ▼
EventViewerUI
      │
      ▼
EventManager.rename_view()
```

The overlay therefore **does not directly modify EventManager state**.

---

# Cancel

Focus loss triggers:

```text
_on_cancel()
```

The overlay closes without submitting a new name.

```text
Focus Lost
    │
    ▼
_on_cancel()
    │
    ├── Hide
    ├── Restore movement
    └── rename_canceled
```

This prevents an incomplete rename interaction from modifying the active view.

---

# UI State Integration

While the rename field is active:

```text
UIManager.is_typing_ui = true
UIManager.movement_disabled.emit()
```

When the interaction ends:

```text
UIManager.is_typing_ui = false
UIManager.movement_enabled.emit()
```

This makes the overlay part of the broader UI input-locking system rather than independently handling player input.

---

# Signals

### `rename_requested`

```text
rename_requested(view_id, new_name)
```

Sent when the user submits a new name.

### `rename_canceled`

Sent when the rename interaction closes without submission.

### `rename_opened`

Sent when the overlay becomes active.

### `rename_closed`

Sent whenever the rename interaction ends.

These signals allow `EventViewerUI` to respond without the overlay needing to know how the surrounding Event Viewer works.

---

# Architectural Boundary

### Rename Overlay owns

* Rename input UI.
* Temporary rename state.
* Focus handling.
* Input submission/cancellation.
* UI typing lock.
* Movement lock.
* Rename signals.

### `EventViewerUI` owns

* Determining which view is being renamed.
* Positioning the overlay.
* Receiving the requested name.
* Updating the EventManager.

### `EventManager` owns

* The actual view name.
* Persistent view state.
* Saving the renamed view.

---

# Core Design

The overlay is intentionally a **small interaction component**:

```text
EventViewerUI
      │
      │ "Rename this view"
      ▼
Rename Overlay
      │
      │ "User entered this name"
      ▼
EventViewerUI
      │
      ▼
EventManager
```

It provides the input interaction without taking ownership of Event Viewer view management.
