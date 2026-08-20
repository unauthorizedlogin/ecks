# ✨ Camera Database Architecture

## 🧠 Overview

The **Camera Database** is the central runtime repository for all `CameraProfile` resources used by the framework.

Camera profiles define reusable camera configurations for different gameplay perspectives and presentation modes. The Camera Database provides centralized access to these profiles while keeping camera configuration data separate from camera runtime behavior.

The database acts as a lightweight public-facing access layer over the underlying `CameraResourceDatabase`. It handles initialization and exposes camera definitions to systems that require them.

The database does not control active cameras, camera transitions, player tracking, or camera behavior.

---

# Responsibilities

The Camera Database is responsible for:

* Initializing the camera resource database.
* Loading all registered camera profiles.
* Providing camera lookup by Camera ID.
* Checking whether a camera profile exists.
* Exposing reusable `CameraProfile` resources to runtime systems.
* Providing a centralized access point for camera configuration data.

---

# Does Not

The Camera Database does **not**:

* Control the active camera.
* Move cameras.
* Track the player.
* Handle camera transitions.
* Process camera input.
* Apply camera zoom at runtime.
* Manage camera limits.
* Render camera views.
* Manage minimap camera behavior.
* Maintain active camera state.

Those responsibilities belong to camera runtime systems and controllers that consume the registered `CameraProfile` definitions.

---

# Resource Structure

Each registered camera is represented by a `CameraProfile`.

Camera profiles contain the configuration required to describe a camera mode, including properties such as:

* Camera identifier
* Camera dimension
* Camera mode
* Projection
* Zoom configuration
* Rotation
* Grid configuration
* Transition settings
* Position offset
* Camera limits

The database stores these definitions for retrieval by Camera ID rather than owning the runtime camera itself.

---

# Initialization Flow

```text
Camera System Access
        │
        ▼
CameraDatabase.initialize()
        │
        ▼
CameraResourceDatabase.load_all()
        │
        ▼
Load registered CameraProfile resources
        │
        ▼
Camera Database Ready
```

Initialization is protected by an internal initialization state so the resource database is loaded only once.

Initialization also records the number of registered cameras and loading duration for runtime diagnostics.

---

# Runtime Access

Primary runtime lookups include:

* `has_camera(id)`
* `get_camera_data(id)`

Example:

```gdscript
CameraDatabase.get_camera_data(camera_id)
```

Runtime systems retrieve the `CameraProfile` associated with a Camera ID and use that definition to configure their own camera behavior.

---

# Camera Profile Organization

Camera profiles are organized as reusable definitions representing different camera configurations.

Example profiles include:

```text
Cameras
│
├── default_2d
├── grid_2d
├── side_scroll_2d
├── isometric_2d
├── fps
├── third_person
└── third_person_tight
```

The profiles can support multiple camera dimensions and presentation modes without requiring separate database implementations.

---

# Resource Database Dependency

The public `CameraDatabase` delegates resource loading and lookup operations to:

```text
camera_resource_database.gd
```

The architecture separates the database's public runtime access layer from the underlying resource-loading implementation.

```text
CameraDatabase
      │
      ▼
CameraResourceDatabase
      │
      ▼
CameraProfile Resources
```

`CameraDatabase` therefore provides the centralized framework-facing interface while `CameraResourceDatabase` owns the actual camera resource collection.

---

# Relationship to Other Systems

The Camera Database serves as a shared configuration source for camera-related runtime systems.

Typical consumers include:

* Camera Manager
* Camera Controllers
* World Systems
* Player Systems
* Minimap Camera Systems
* Scene-specific camera controllers

Consumers request camera profiles by ID rather than maintaining their own copies of camera configuration.

---

# Design Goals

The Camera Database follows several architectural principles:

* Centralized camera configuration.
* Reusable camera profiles.
* Separation of camera data and runtime behavior.
* Support for multiple camera dimensions and modes.
* Single source of truth for camera configuration.
* Lightweight runtime access.
* Separation between public database access and resource loading.

---

# Benefits

This architecture provides:

* Centralized camera profile management.
* Reusable configurations across multiple systems.
* Consistent camera settings throughout the framework.
* Reduced coupling between camera definitions and runtime controllers.
* Support for multiple camera perspectives through shared profile data.
* Clear separation between camera configuration and camera execution.

The Camera Database functions as the framework's canonical repository for `CameraProfile` definitions, providing runtime systems with reusable camera configurations while leaving camera control and behavior to dedicated runtime systems.
