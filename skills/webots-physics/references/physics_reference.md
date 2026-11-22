# Webots Physics Reference

This reference focuses on physics simulation configuration only.

- Do not use this file for basic scene/world authoring workflow (`webots-world-building`).
- Do not use this file for actuator control patterns (`webots-actuators`).

## 1) WorldInfo physics-related fields

`WorldInfo` controls global solver behavior and contact defaults.

| Field | Type | Purpose |
|---|---|---|
| `gravity` | `SFVec3f` | Global gravity vector in m/s^2. |
| `basicTimeStep` | `SFInt32` | Simulation step in ms. Lower improves fidelity and cost; higher improves throughput and can reduce stability. |
| `ERP` | `SFFloat` | Global Error Reduction Parameter for constraints. |
| `CFM` | `SFFloat` | Global Constraint Force Mixing (constraint softness). |
| `contactProperties` | `MFNode` (`ContactProperties`) | Material-pair contact/friction/restitution overrides. |
| `physicsDisableTime` | `SFFloat` | Idle duration before bodies are auto-disabled/slept. |
| `physicsDisableLinearThreshold` | `SFFloat` | Linear speed threshold for sleeping. |
| `physicsDisableAngularThreshold` | `SFFloat` | Angular speed threshold for sleeping. |
| `optimalThreadCount` | `SFInt32` | Number of physics worker threads for multithreaded solving. |

## 2) Physics node (per-Solid rigid body properties)

| Field | Type | Purpose |
|---|---|---|
| `density` | `SFFloat` | Mass density in kg/m^3. Use `-1` when `mass` is explicitly set. |
| `mass` | `SFFloat` | Total mass in kg. Use `-1` when `density` is used. |
| `centerOfMass` | `MFVec3f` | COM point(s) in local coordinates. Typical single point for one rigid body. |
| `inertiaMatrix` | `MFFloat` | Inertia tensor terms in local frame as `[Ixx Iyy Izz, Ixy Ixz Iyz]` blocks. |
| `damping` | `SFNode` (`Damping`) | Optional linear/angular passive damping. |

Mass-property rules:

- Use one of `density` or `mass`; set the other to `-1`.
- If explicit values are absent, mass properties are computed from `boundingObject` geometry.

## 3) Damping node fields

| Field | Type | Purpose |
|---|---|---|
| `linear` | `SFFloat` | Linear velocity damping coefficient. |
| `angular` | `SFFloat` | Angular velocity damping coefficient. |

## 4) Joint hierarchy and complete field reference

### 4.1 Abstract Joint node

All articulated joints derive from `Joint`.

| Field | Type | Purpose |
|---|---|---|
| `jointParameters` | `SFNode` | Primary parameter node for the joint type (`JointParameters`, `HingeJointParameters`, or `BallJointParameters` depending on concrete joint). |
| `device` | `MFNode` | Optional attached devices (motors, sensors, brakes). |
| `endPoint` | `SFNode` (`Solid`) | Child rigid body attached through the joint. |

### 4.2 JointParameters fields (generic axis/limit parameters)

`JointParameters` is used directly by `SliderJoint`, and as secondary/tertiary parameter blocks in multi-axis joints.

| Field | Type | Purpose |
|---|---|---|
| `position` | `SFFloat` | Initial joint coordinate (m for prismatic axis, rad for rotational axes that use this parameter node). |
| `axis` | `SFVec3f` | Joint axis direction in local coordinates. |
| `minStop` | `SFFloat` | Lower hard stop limit. |
| `maxStop` | `SFFloat` | Upper hard stop limit. |
| `springConstant` | `SFFloat` | Axis spring stiffness. |
| `dampingConstant` | `SFFloat` | Axis damping coefficient. |
| `staticFriction` | `SFFloat` | Static friction on this axis. |
| `stopERP` | `SFFloat` | Per-joint stop ERP override. |
| `stopCFM` | `SFFloat` | Per-joint stop CFM override. |

### 4.3 HingeJointParameters fields

Used by `HingeJoint` (primary axis) and `Hinge2Joint` (first rotational axis).

| Field | Type | Purpose |
|---|---|---|
| `position` | `SFFloat` | Initial revolute angle (rad). |
| `axis` | `SFVec3f` | Revolute axis (unit vector). |
| `anchor` | `SFVec3f` | Point through which axis passes. |
| `minStop` | `SFFloat` | Lower angular hard stop (rad). |
| `maxStop` | `SFFloat` | Upper angular hard stop (rad). |
| `springConstant` | `SFFloat` | Angular spring stiffness. |
| `dampingConstant` | `SFFloat` | Angular damping coefficient. |
| `staticFriction` | `SFFloat` | Static friction torque term. |
| `stopERP` | `SFFloat` | Hard-stop ERP override. |
| `stopCFM` | `SFFloat` | Hard-stop CFM override. |
| `suspensionAxis` | `SFVec3f` | Optional suspension axis (not the hinge axis). |
| `suspensionSpringConstant` | `SFFloat` | Suspension spring stiffness. |
| `suspensionDampingConstant` | `SFFloat` | Suspension damping coefficient. |

### 4.4 BallJointParameters fields

Used by `BallJoint` primary block.

| Field | Type | Purpose |
|---|---|---|
| `anchor` | `SFVec3f` | Ball joint anchor position. |
| `springConstant` | `SFFloat` | Uniform rotational spring component. |
| `dampingConstant` | `SFFloat` | Uniform rotational damping component. |
| `staticFriction` | `SFFloat` | Uniform rotational static friction component. |

### 4.5 HingeJoint fields

| Field | Type | Purpose |
|---|---|---|
| `jointParameters` | `SFNode` (`HingeJointParameters`) | Primary revolute axis definition. |
| `device` | `MFNode` | Optional devices attached to hinge joint. |
| `endPoint` | `SFNode` (`Solid`) | Child body linked by revolute constraint. |

### 4.6 SliderJoint fields

| Field | Type | Purpose |
|---|---|---|
| `jointParameters` | `SFNode` (`JointParameters`) | Prismatic axis definition. |
| `device` | `MFNode` | Optional devices attached to slider joint. |
| `endPoint` | `SFNode` (`Solid`) | Child body linked by prismatic constraint. |

### 4.7 Hinge2Joint fields

| Field | Type | Purpose |
|---|---|---|
| `jointParameters` | `SFNode` (`HingeJointParameters`) | First rotational axis block. |
| `jointParameters2` | `SFNode` (`JointParameters`) | Second rotational axis block. |
| `device` | `MFNode` | Optional devices for axis instrumentation/actuation. |
| `endPoint` | `SFNode` (`Solid`) | Child body linked by two-axis rotational joint. |

### 4.8 BallJoint fields

| Field | Type | Purpose |
|---|---|---|
| `jointParameters` | `SFNode` (`BallJointParameters`) | Anchor and uniform rotational passive terms. |
| `jointParameters2` | `SFNode` (`JointParameters`) | Optional second-axis limit/passive terms. |
| `jointParameters3` | `SFNode` (`JointParameters`) | Optional third-axis limit/passive terms. |
| `device` | `MFNode` | Optional attached devices. |
| `endPoint` | `SFNode` (`Solid`) | Child body linked by spherical constraint. |

## 5) ContactProperties complete field reference

Define pairwise material response in `WorldInfo.contactProperties`.

| Field | Type | Purpose |
|---|---|---|
| `material1` | `SFString` | First material tag; matched to `Solid.contactMaterial`. |
| `material2` | `SFString` | Second material tag; matched to `Solid.contactMaterial`. |
| `coulombFriction` | `MFFloat` | Friction coefficients (one or two tangent directions). |
| `frictionRotation` | `SFFloat` | Tangential basis rotation for anisotropic friction. |
| `bounce` | `SFFloat` | Restitution coefficient. |
| `bounceVelocity` | `SFFloat` | Minimum impact speed required to apply restitution. |
| `forceDependentSlip` | `MFFloat` | ODE force-dependent slip coefficients per friction direction. |
| `softERP` | `SFFloat` | Contact-level ERP override. |
| `softCFM` | `SFFloat` | Contact-level CFM override. |
| `maxContactJoints` | `SFInt32` | Maximum contact joints retained for a colliding pair (deepest points kept). |

## 6) Fluid and immersion fields

### 6.1 Fluid fields

`Fluid` defines a volume where immersed rigid bodies can receive buoyancy and drag.

| Field | Type | Purpose |
|---|---|---|
| `name` | `SFString` | Identifier referenced by `ImmersionProperties.fluidName`. |
| `model` | `SFString` | Optional model label metadata. |
| `description` | `SFString` | Optional description metadata. |
| `density` | `SFFloat` | Fluid density (kg/m^3); used by buoyancy and drag terms. |
| `viscosity` | `SFFloat` | Dynamic viscosity; used by viscous resistance terms. |
| `streamVelocity` | `SFVec3f` | Uniform flow velocity vector (laminar approximation). |
| `boundingObject` | `SFNode` | Immersion volume geometry used for fluid overlap tests. |
| `locked` | `SFBool` | UI interaction lock for scene editing. |

### 6.2 ImmersionProperties fields

`ImmersionProperties` is added to a `Solid` to define fluid interaction coefficients.

| Field | Type | Purpose |
|---|---|---|
| `fluidName` | `SFString` | Name of target `Fluid` node. |
| `referenceArea` | `SFString` | Drag reference area mode (`"immersed area"` or `"xyz-projected area"`). |
| `dragForceCoefficients` | `SFVec3f` | Directional drag force coefficients. |
| `dragTorqueCoefficients` | `SFVec3f` | Directional drag torque coefficients. |
| `viscousResistanceForceCoefficient` | `SFFloat` | Low-Reynolds viscous force coefficient. |
| `viscousResistanceTorqueCoefficient` | `SFFloat` | Low-Reynolds viscous torque coefficient. |

## 7) Physics plugin C API reference

Webots physics plugins are native shared libraries loaded by the simulator and linked against ODE and the Webots plugin interface.

### 7.1 Required callback signatures

```c
DLLEXPORT void webots_physics_init();
DLLEXPORT int  webots_physics_collide(dGeomID g1, dGeomID g2);
DLLEXPORT void webots_physics_step();
DLLEXPORT void webots_physics_step_end();
DLLEXPORT void webots_physics_cleanup();
DLLEXPORT void webots_physics_draw(int pass, const char *view);
```

Callback intent:

- `webots_physics_init`: initialize plugin state and cache body/joint handles.
- `webots_physics_collide`: custom collision handling hook; return value controls custom/default processing path.
- `webots_physics_step`: apply custom forces/constraints before or during integration phase.
- `webots_physics_step_end`: post-step processing and state publishing.
- `webots_physics_cleanup`: release memory and plugin resources.
- `webots_physics_draw`: optional custom debug rendering pass.

### 7.2 Utility functions available in plugin code

```c
dGeomID      dWebotsGetGeomFromDEF(const char *defName);
dBodyID      dWebotsGetBodyFromDEF(const char *defName);
dJointGroupID dWebotsGetContactJointGroup();
void         dWebotsSend(int channel, const void *buffer, int size);
const void  *dWebotsReceive(int *size);
void         dWebotsConsolePrintf(const char *format, ...);
double       dWebotsGetTime();
```

Utility intent:

- `dWebotsGetGeomFromDEF`: map a DEF name to the ODE geometry (`dGeomID`) tied to `boundingObject`.
- `dWebotsGetBodyFromDEF`: map a DEF name to ODE body (`dBodyID`).
- `dWebotsGetContactJointGroup`: access contact joint group for custom contact generation.
- `dWebotsSend` / `dWebotsReceive`: exchange plugin/controller messages.
- `dWebotsConsolePrintf`: write formatted logs to Webots console.
- `dWebotsGetTime`: read simulation time.

## 8) ODE object structure cheat sheet

Core opaque handles used by Webots plugin callbacks:

| ODE type | Meaning | Typical usage |
|---|---|---|
| `dBodyID` | Rigid body object | Read/write pose, velocity, mass, forces, torques. |
| `dGeomID` | Collision geometry object | Collision queries, association with body, category/mask data. |
| `dJointID` | Constraint/joint object | Configure joint anchors, axes, limits, motor/stop params. |
| `dWorldID` | Dynamics world | Global solver settings, stepping, gravity (managed by Webots). |
| `dSpaceID` | Collision space | Broad-phase collision structure (managed by Webots). |
| `dJointGroupID` | Contact group | Temporary contact joints per step. |

Practical mapping in Webots:

- `Solid` with `Physics` maps to an ODE `dBodyID` plus one or more `dGeomID` collision shapes.
- Webots joint nodes map to ODE `dJointID` constraints.
- Contact points generate temporary contact joints in the active `dJointGroupID`.

## 9) Stability and performance notes

- Raise `basicTimeStep` for throughput, lower for accuracy and solver stability.
- Use simple primitive `boundingObject` shapes whenever possible.
- Use `softCFM`/`softERP` and per-joint `stopCFM`/`stopERP` to stabilize stiff contact/limit conditions.
- Limit contact workload with `maxContactJoints` and material-pair filtering.
- Use sleep parameters (`physicsDisable*`) for large scenes with many idle bodies.
