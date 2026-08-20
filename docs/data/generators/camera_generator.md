# 🏷️ Camera Profile Resource Generator System

The Camera Profile Resource Generator System provides a data-driven pipeline for creating and managing camera configurations.

Camera profiles are authored through CSV data and automatically converted into `CameraProfile` `.tres` resources, allowing camera behavior and presentation configurations to be defined independently from runtime camera logic.

The system is responsible for:

* camera identity and metadata
* camera dimension and mode configuration
* projection configuration
* runtime camera controls
* zoom and rotation settings
* mouse-look configuration
* 2D camera configuration
* grid configuration
* camera transition behavior
* 3D camera configuration
* position and rotation offsets
* follow behavior
* camera limits
* drag behavior
* viewport configuration
* processing mode
* camera database organization
* generated camera indexing

---

## 🧱 Camera Profile Architecture

Each generated camera is represented by a `CameraProfile` resource.

Camera profiles centralize camera configuration rather than embedding camera settings directly into individual camera controllers.

Profiles can define:

* Camera identity
* Camera dimension
* Camera mode
* Projection type
* Zoom behavior
* Rotation behavior
* Mouse-look behavior
* 2D settings
* Grid settings
* Transition settings
* 3D projection settings
* Position offsets
* Rotation offsets
* Follow behavior
* Camera boundaries
* Drag behavior
* Viewport settings
* Processing mode

The generator creates the configuration resource.

The camera runtime system interprets the profile and applies the configuration to the active camera.

---

## 🎥 Camera Classification

Camera profiles support multiple dimensions and camera modes.

### Camera Dimensions

Profiles can target:

* 2D cameras
* 3D cameras

### Camera Modes

The profile architecture supports modes such as:

* Top Down
* Side Scroll
* Isometric
* First Person
* Third Person

### Projection Types

Supported projection configuration includes:

* Orthographic
* Perspective

This allows the same profile architecture to support multiple camera styles across the engine.

---

## 🎛️ Runtime Camera Controls

Camera profiles can define player-controlled camera behavior.

Supported controls include:

* Zoom enablement
* Minimum zoom
* Maximum zoom
* Zoom step
* Rotation enablement
* Rotation step
* Mouse-look enablement
* Minimum pitch
* Maximum pitch

These settings allow different camera profiles to expose different levels of runtime control without requiring separate camera implementations.

Example:

```text
Top Down Camera

Zoom:
Enabled

Minimum:
1.0

Maximum:
5.0

Rotation:
Disabled
```

```text
Third Person Camera

Zoom:
Enabled

Rotation:
Enabled

Mouse Look:
Enabled

Pitch:
-89° → 89°
```

---

## 🗺️ 2D Camera Configuration

2D camera profiles support configuration for:

* Zoom
* Rotation
* Grid visibility
* Grid dimensions

Example:

```text
Grid Camera

Zoom:
1, 1

Grid:
Enabled

Grid Size:
320, 176
```

The grid configuration is stored as part of the camera profile rather than being hardcoded into the camera controller.

---

## 🎞️ Camera Transition Configuration

Camera profiles define transition behavior used when camera settings change.

Supported configuration includes:

* Transition duration
* Tween transition type
* Tween easing type

Transition types include:

* Linear
* Sine
* Quadratic
* Cubic
* Quartic
* Quintic
* Exponential
* Elastic
* Circular
* Bounce
* Back

Easing modes include:

* Ease In
* Ease Out
* Ease In Out
* Ease Out In

This allows camera profiles to define their own movement characteristics while leaving transition execution to the runtime camera system.

---

## 🎥 3D Camera Configuration

3D profiles support:

* Field of view
* Orthographic size
* Near clipping distance
* Far clipping distance

This allows perspective and orthographic 3D cameras to share the same profile architecture.

Example:

```text
Third Person

Projection:
Perspective

FOV:
75

Near Clip:
0.05

Far Clip:
4000
```

---

## 📍 Position & Rotation Configuration

Camera profiles support independent position and rotation offsets.

Position offsets use:

```text
Vector3
X, Y, Z
```

Rotation offsets use:

```text
Vector3
X, Y, Z
```

These values allow camera positioning and orientation to be configured by profile rather than embedded into camera controller logic.

---

## 👤 Follow Behavior

Camera profiles define how the camera follows its target.

Supported settings include:

* Follow smoothing
* Follow speed

Example:

```text
Follow Smoothing:
Enabled

Follow Speed:
5
```

The profile defines the desired follow behavior while the camera controller manages the actual runtime movement.

---

## 🧱 Camera Limits

Profiles can optionally define world-space camera boundaries.

Supported limits:

* Left
* Top
* Right
* Bottom

Limits can be enabled or disabled per profile.

Example:

```text
Limits:
Enabled

Left:
-10000

Top:
-10000

Right:
10000

Bottom:
10000
```

This allows different maps or camera configurations to use different playable boundaries without changing camera code.

---

## 🖱️ Camera Drag Configuration

Profiles support independent horizontal and vertical camera dragging.

Configuration includes:

* Horizontal drag enablement
* Vertical drag enablement
* Horizontal drag margin
* Vertical drag margin

This allows camera profiles to define whether and how camera movement responds to drag behavior.

---

## 🖥️ Viewport Configuration

Camera profiles can optionally define an explicit viewport configuration.

Supported settings include:

* Viewport enablement
* Viewport width
* Viewport height

Example:

```text
Viewport:
Enabled

Size:
1920 × 1080
```

---

## ⚙️ Processing Configuration

Camera profiles define their preferred processing mode.

The generator resolves the configured process mode against the `CameraProfile` process mode enumeration.

This allows camera updates to operate under the appropriate engine processing lifecycle without hardcoding the mode into the runtime controller.

---

## 🗂️ Camera Database Routing

Generated camera profiles are organized using folder and subfolder metadata from the CSV.

Example:

```text
data/databases/cameras/

├── default/
│   ├── general/
│   │   ├── default_2d.tres
│
├── gameplay/
│   ├── top_down/
│   ├── side_scroll/
│
├── 3d/
│   ├── first_person/
│   ├── third_person/
```

The generator creates missing directories automatically.

This allows camera databases to expand without requiring changes to the generator itself.

---

## 🔄 Data Parsing & Validation

The generator converts CSV values into the appropriate resource types.

Supported conversions include:

* Boolean values
* Integers
* Floating-point values
* `Vector2`
* `Vector2i`
* `Vector3`
* Enum values
* Color values
* Strings

Boolean fields support:

```text
true
false
1
0
yes
no
```

Invalid enum, boolean, or vector values generate warnings and fall back to configured defaults where applicable.

This provides a consistent boundary between human-authored CSV configuration and strongly typed camera resources.

---

## ⚙️ Generation Pipeline

The generation process:

1. Reads camera profile CSV definitions
2. Parses CSV headers and values
3. Creates `CameraProfile` resources
4. Applies camera classification
5. Applies runtime controls
6. Applies mouse-look configuration
7. Applies 2D settings
8. Applies grid configuration
9. Applies transition settings
10. Applies 3D settings
11. Applies position and rotation offsets
12. Applies follow behavior
13. Applies camera limits
14. Applies drag behavior
15. Applies viewport configuration
16. Applies processing configuration
17. Routes the profile into its database folder
18. Saves the `.tres` resource
19. Rebuilds the camera index

---

## 🔎 Generated Camera Index

After resource generation completes, the generator invokes the Camera Index Generator.

The index provides the runtime system with a deterministic collection of generated camera resources.

The pipeline therefore becomes:

```text
Camera CSV
 |
 ↓
Camera Resource Generator
 |
 ↓
CameraProfile.tres
 |
 ↓
Camera Index Generator
 |
 ↓
Camera Index
 |
 ↓
Runtime Camera System
```

The index removes the need for runtime systems to independently discover camera resources through filesystem scanning.

---

## 🔗 Camera System Integration

The Camera Profile Generator defines camera configuration.

It does not operate the camera itself.

Responsibilities are separated:

```text
CSV
 |
 ↓
Camera Resource Generator
 |
 ↓
CameraProfile
 |
 ↓
Camera Index
 |
 ↓
Camera System
 |
 ↓
Active Camera
```

The generator provides configuration for:

* camera type
* projection
* controls
* movement
* targeting
* boundaries
* presentation

The runtime camera system is responsible for interpreting those settings and controlling the active camera.

Camera profiles can therefore be reused across different camera controllers and gameplay contexts without duplicating configuration logic.

---

## ⚠️ Generator Scope

The current generator contains a metadata application function for:

* minimap enablement
* debug color
* profile version

However, `_apply_metadata()` is **not currently invoked by `_generate_camera_profile()`**.

Therefore, those metadata fields are not presently populated through this generation path.

The architecture should treat that functionality as reserved/incomplete rather than documenting it as an active part of the current pipeline.

---

## ✅ System Responsibilities

The Camera Profile Resource Generator System:

✅ Creates camera profile resources
✅ Converts CSV data into `CameraProfile` resources
✅ Configures 2D and 3D camera behavior
✅ Configures projection settings
✅ Configures runtime camera controls
✅ Configures follow and movement behavior
✅ Configures camera limits and drag behavior
✅ Configures viewport and processing settings
✅ Routes profiles into database categories
✅ Converts structured CSV values into typed properties
✅ Rebuilds the camera resource index

The Camera Profile Resource Generator System does **not**:

❌ Control the active camera
❌ Execute camera movement
❌ Handle player input
❌ Manage camera state
❌ Perform runtime camera transitions
❌ Determine which camera should be active
❌ Manage minimap camera behavior

Those responsibilities belong to:

* Camera System
* Camera Controller
* World System
* Player/Input Systems
* Minimap Camera System
