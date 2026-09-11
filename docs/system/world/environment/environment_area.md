# 🌦️ Environment Area

### Purpose

`EnvironmentArea` defines a trigger region for environment zones. It detects the player entering an `EnvironmentShape` and delegates the active environment transition to the Environment Manager.

### Architecture Role

```text
EnvironmentArea
    └── EnvironmentShape[]
            ↓
      EnvironmentManager
            ↓
     Active Environment
```

`EnvironmentArea` is the spatial trigger layer. It does not resolve environments or manage environment state.

### Responsibilities

* Detect player entry into environment shapes.
* Restrict physics detection to the configured player collision mask.
* Identify which `EnvironmentShape` was entered.
* Notify `EnvironmentManager` of the active shape.
* Register and unregister itself with `EnvironmentManager`.

### Initialization

During `_ready()`:

* Connects `body_shape_entered` to the environment trigger handler.
* Disables collision-layer participation.
* Disables default collision interaction.
* Sets the area as non-monitorable.
* Enables detection for `PLAYER_MASK`.
* Registers the area with `EnvironmentManager`.

The area therefore functions strictly as a detection/trigger volume.

### Environment Shape Detection

When a physics body enters the area, `on_player_enter_environment_shape()` resolves the entered child using `local_shape_index`.

If the child is an `EnvironmentShape`, it is passed directly to:

```text
EnvironmentManager.set_active_environment_shape()
```

The Environment Manager then owns the subsequent environment resolution and application.

### Lifecycle

```text
_ready()
  ↓
Register EnvironmentArea
  ↓
Player enters EnvironmentShape
  ↓
Set active EnvironmentShape
  ↓
EnvironmentManager resolves environment
  ↓
_exit_tree()
  ↓
Unregister EnvironmentArea
```

### File(s) Affected

* `environment_area.gd`
* `EnvironmentShape`
* Environment Manager

### Notes

`EnvironmentArea` provides the **spatial detection layer** for the environment system.

It does not select a `ResourceEnvironment`, process refresh modes, evaluate time state, or apply weather/effects. Those responsibilities remain with `EnvironmentShape` and `EnvironmentManager`.
