# Webots Core Reference

## WorldInfo Fields (Core)

```text
WorldInfo {
  SFString title                          ""
  MFString info                           [ ]
  SFString window                         "<none>"
  SFFloat  gravity                        9.81
  SFFloat  CFM                            0.00001
  SFFloat  ERP                            0.2
  SFString physics                        "<none>"
  SFFloat  basicTimeStep                  32
  SFFloat  FPS                            60
  SFInt32  optimalThreadCount             1
  SFFloat  physicsDisableTime             1
  SFFloat  physicsDisableLinearThreshold  0.01
  SFFloat  physicsDisableAngularThreshold 0.01
  SFNode   defaultDamping                 NULL
  SFFloat  inkEvaporation                 0
  SFString coordinateSystem               "ENU"
  SFString gpsCoordinateSystem            "local"
  SFVec3f  gpsReference                   0 0 0
  SFFloat  lineScale                      0.1
  SFFloat  dragForceScale                 30.0
  SFFloat  dragTorqueScale                5.0
  SFInt32  randomSeed                     0
  MFNode   contactProperties              []
}
```

Key operational fields:
- `basicTimeStep`: atomic simulator integration step in ms.
- `FPS`: maximum main-view refresh rate in real-time mode.
- `gravity`: global gravitational acceleration magnitude.
- `ERP`/`CFM`: ODE contact solver behavior tuning.
- `coordinateSystem`: axis convention (`ENU`, `NUE`, `EUN`), ENU default.
- `optimalThreadCount`: physics threading count; can impact determinism.
- `dragForceScale`/`dragTorqueScale`: user-drag interaction force scaling.

## Viewpoint Fields (Core)

```text
Viewpoint {
  SFFloat    fieldOfView             0.785398
  SFRotation orientation             0 0 1 0
  SFVec3f    position                -10 0 0
  SFString   description             ""
  SFFloat    near                    0.05
  SFFloat    far                     0.0
  SFFloat    exposure                1.0
  SFString   follow                  ""
  SFString   followType              "Tracking Shot"
  SFFloat    followSmoothness        0.5
  SFNode     lensFlare               NULL
  SFFloat    ambientOcclusionRadius  2
  SFFloat    bloomThreshold          21
}
```

Key operational fields:
- `position`, `orientation`: absolute camera transform.
- `fieldOfView`: perspective angle in radians.
- `follow`: target solid path/name string to follow.
- `followType`: follow behavior (`None`, `Tracking Shot`, `Mounted Shot`, `Pan and Tilt Shot`).

## Background Fields (Core)

```text
Background {
  MFColor  skyColor            [ 0 0 0 ]
  MFString backUrl             []
  MFString bottomUrl           []
  MFString frontUrl            []
  MFString leftUrl             []
  MFString rightUrl            []
  MFString topUrl              []
  MFString backIrradianceUrl   []
  MFString bottomIrradianceUrl []
  MFString frontIrradianceUrl  []
  MFString leftIrradianceUrl   []
  MFString rightIrradianceUrl  []
  MFString topIrradianceUrl    []
  SFFloat  luminosity          1
}
```

Key operational fields:
- `skyColor`: fallback sky color when no cubemap is active.
- `*Url`: six-face visual cubemap.
- `*IrradianceUrl`: six-face HDR lighting cubemap for PBR reflections.
- `luminosity`: scale factor for background light contribution.

## Coordinate System and Units

- Default axis convention: `ENU` (X East, Y North, Z Up).
- Units: meters, kilograms, seconds, radians.
- `WorldInfo.coordinateSystem` can switch to `NUE` or `EUN` for compatibility workflows.

## World File Essentials (`.wbt`)

- Required extension: `.wbt`.
- Required header line:

```text
#VRML_SIM R2025a utf8
```

- Required root node presence: exactly one `WorldInfo`, `Viewpoint`, and `Background`.
- Syntax model: VRML97 subset plus Webots-specific nodes.

## Standard Project Hierarchy

```text
project_root/
  worlds/
    my_world.wbt
    .my_world.wbproj
    .my_world.jpg
  controllers/
  protos/
  plugins/
```

Directory semantics:
- `worlds/`: world files and per-world GUI/project metadata.
- `controllers/`: executable/controller source folders.
- `protos/`: reusable PROTO definitions.
- `plugins/`: project plugins (including physics plugin usage).

## UI Keyboard Shortcuts (Documented Core)

### Scene Tree / Field Editor

- `Enter`: start editing selected field item; apply text edits.
- `Tab`: move focus through field editor widgets and back to scene tree.
- `Up` / `Down`: increment/decrement numeric field values.
- `Space`: toggle checkbox fields.

### 3D Selection and Manipulation

- `Alt + Click`: select solid subpart (Linux may require `Ctrl + Alt + Click`).
- `Shift + Left Mouse Drag`: translate selected solid parallel to ground.
- `Shift + Right Mouse Drag`: rotate selected solid around world vertical axis.
- `Shift + Left+Right Mouse Drag` (or `Shift + Middle Mouse Drag`): lift/lower selected solid.
- `Shift + Mouse Wheel`: lift/lower selected solid.

### Physics Interaction in 3D View

- `Alt + Left Mouse Drag`: apply force to selected physical solid.
- `Alt + Right Mouse Drag`: apply torque to selected physical solid.
- Linux note: some window managers require `Ctrl + Alt` modifiers.

### macOS Single-Button Mouse Variants

- `Option + Mouse Drag`: camera translation.
- `Ctrl + Mouse Drag`: camera zoom/rotation.
- `Shift + Ctrl + Mouse Drag`: rotate selected object.
- `Shift + Command + Mouse Drag`: lift selected object.

## Simulation Timing Reference

- Simulation step: `WorldInfo.basicTimeStep`.
- Control step: argument passed to `wb_robot_step` / `Robot.step`.
- Constraint: control step must be a multiple of simulation step.
- Synchronization: simulator/controller data exchange occurs at step boundaries.

## Upgrade Notes: R2023a to R2025

## R2023a -> R2023b

Major compatibility shift:
- `Pose` introduced and used as base for `Solid`/`Fluid` workflows.
- Legacy `Transform` usage with non-default `scale` may require manual world/PROTO fixes.
- Auto-conversion exists for many cases; complex scaling/bounding object cases can require manual edits.

## R2023b -> R2025a/R2025b (Core-Relevant)

Core migration highlights:
- Web scene format naming changed from `X3D` to `W3D`.
- Platform support changes:
  - Ubuntu 20.04 removed; Ubuntu 24.04 added.
  - macOS 11 removed; macOS 14 added.
- Supervisor API rename (breaking for older code paths):
  - `wb_supervisor_node_get_proto_*` -> `wb_supervisor_node_get_base_node_*`.
  - ROS service field `proto` -> `queryBaseNode`.
- Lua support for PROTO scripting removed.
- Windows library naming cleanup in R2025b:
  - `libController.a`/`libCppController.a` removed.
  - `Controller.lib`/`CppController.lib` used instead.

Migration practice:
- Re-open and re-save worlds in target Webots version.
- Re-check plugins, external build scripts, and platform-specific dependencies.
- Re-validate startup and simulation modes (`pause`, `step`, `realtime`, `fast`) after upgrade.
