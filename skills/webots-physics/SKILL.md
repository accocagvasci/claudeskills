---
name: webots-physics
description: "Use this skill when configuring physics simulation in Webots - ODE engine parameters, joints (HingeJoint, SliderJoint, BallJoint), collision detection, contact properties, fluid dynamics, physics plugins, damping, or performance tuning. Triggers on: webots physics, ODE, joint, HingeJoint, SliderJoint, collision, contact, friction, bounce, damping, fluid, buoyancy, physics plugin, ERP, CFM."
---

# Webots Physics Skill

Use this skill to configure and debug rigid body physics behavior in Webots using ODE-backed simulation primitives and parameters.

Do not cover basic world building workflow in this skill; route that work to `webots-world-building`.
Do not cover motor control API usage in this skill; route that work to `webots-actuators`.

## Scope

- Configure global simulation behavior from `WorldInfo` physics fields.
- Configure mass, inertia, and center of mass with `Physics`.
- Configure and tune joint constraints and passive dynamics.
- Configure collision shapes and contact material interactions.
- Configure damping and fluid interaction fields.
- Implement advanced custom dynamics through physics plugins.
- Tune runtime performance while preserving required simulation fidelity.

## ODE Engine Fundamentals

Webots uses the Open Dynamics Engine (ODE) for rigid body dynamics.

Control global physics from `WorldInfo`:

- `basicTimeStep`: simulation step in milliseconds. Lower value increases numerical accuracy and stability, with higher CPU cost. Higher value improves runtime speed, with lower stability and precision.
- `ERP` (Error Reduction Parameter): `[0, 1]` coefficient controlling how aggressively constraint error is corrected each step.
- `CFM` (Constraint Force Mixing): softening term for constraints. Higher values generally make constraints softer and can improve stability in difficult contact scenes.
- `gravity`: global gravitational acceleration vector.

Use conservative baseline values first, then tune one parameter at a time.

## Physics Node

Use `Physics` on each dynamic `Solid` to define mass properties.

```vrml
Physics {
  density 1000          # kg/m^3 (use -1 if specifying mass directly)
  mass 0.5              # kg (use -1 if using density)
  centerOfMass [0 0 0]  # local coordinates
  inertiaMatrix [       # optional: Ixx Iyy Izz, Ixy Ixz Iyz
    0.001 0.001 0.001
    0 0 0
  ]
}
```

Apply these rules:

- Use either `density` or `mass`; set the other to `-1`.
- If both are omitted or left at default unresolved values, derive mass properties from `boundingObject` geometry.
- Set `centerOfMass` and `inertiaMatrix` explicitly when simulation fidelity matters (robot balance, high-speed motion, manipulation).

## Joint Types

Model articulated mechanisms with Webots joint nodes.

### HingeJoint (revolute, most common)

Use for single-axis rotational motion.

```vrml
HingeJoint {
  jointParameters HingeJointParameters {
    position 0          # initial angle (rad)
    axis 0 1 0          # rotation axis
    anchor 0 0 0        # anchor point
    minStop -1.57       # joint limits
    maxStop 1.57
    springConstant 0    # spring behavior
    dampingConstant 0   # damping
  }
  device [
    RotationalMotor { name "motor1" maxVelocity 10 maxTorque 5 }
    PositionSensor { name "sensor1" }
    Brake { name "brake1" }
  ]
  endPoint Solid { ... }
}
```

### SliderJoint (prismatic)

Use for single-axis linear motion.

- Follow the same structural pattern as `HingeJoint`.
- Interpret `JointParameters.position` in meters.
- Use `axis` as translation direction and `anchor` as reference point.

### Hinge2Joint (universal, 2 DOF)

Use for two rotational degrees of freedom around two axes.

- Configure first axis through `HingeJointParameters`.
- Configure second axis through `JointParameters`.
- Tune stops and damping on both axes to avoid unstable cross-coupling.

### BallJoint (spherical, 3 DOF)

Use for free rotation around one point.

- Use `BallJointParameters` to define `anchor`.
- Apply damping and stop constraints through node-specific fields when needed.

## Collision Detection

Use `boundingObject` to define collision geometry.

- Prefer simple primitives (`Box`, `Sphere`, `Cylinder`, `Capsule`) for best performance.
- Use `IndexedFaceSet`/`Mesh` collision only where geometric fidelity is required.
- Combine primitives in a `Group` to build efficient compound collision shapes.
- Treat nested child `Solid` nodes without intermediate `Joint` as a fused rigid body.

## ContactProperties

Define material pair interaction in `WorldInfo.contactProperties`.

```vrml
WorldInfo {
  contactProperties [
    ContactProperties {
      material1 "rubber"
      material2 "asphalt"
      coulombFriction [1.0]     # friction coefficient
      bounce 0.2                # bounciness (0-1)
      bounceVelocity 0.1        # min velocity for bounce
      softCFM 0.001             # contact softness
      softERP 0.2
    }
  ]
}
```

- Match `material1`/`material2` against each `Solid.contactMaterial` value.
- Fall back to default contact friction/response when no pair matches.
- Use `softCFM`/`softERP` to stabilize high-contact scenes and stacked bodies.

## Damping

Use `Damping` for passive energy dissipation.

```vrml
Damping {
  linear 0.5    # linear velocity damping
  angular 0.5   # angular velocity damping
}
```

- Increase linear damping to reduce translational drift/oscillation.
- Increase angular damping to suppress spin and joint ringing.

## Fluid Dynamics

Use `Fluid` and `ImmersionProperties` to model buoyancy and drag.

- Define fluid volume and physical coefficients in a `Fluid` node.
- Define per-body fluid interaction in `ImmersionProperties` on each `Solid`.
- Account for Archimedes' thrust (buoyancy), drag forces, fluid density, and viscosity.
- Validate center of mass and collision geometry when tuning submerged behavior.

## Physics Plugin (Advanced)

Use a physics plugin for custom force models and direct ODE access.

- Implement plugin callbacks in C: `webots_physics_init`, `webots_physics_step`, `webots_physics_cleanup`.
- Access ODE bodies and joints directly from plugin-side APIs.
- Use for custom effects such as wind fields, tethers, bespoke force laws, and prototype soft-body approximations.
- Build and load as platform shared library (`.so`, `.dll`, `.dylib`).

## Performance Optimization

- Increase `basicTimeStep` to improve speed, accepting reduced accuracy.
- Set `WorldInfo.optimalThreadCount` for multi-threaded physics execution.
- Configure `physicsDisableTime` to sleep idle objects and reduce solver work.
- Prefer primitive collision shapes and avoid unnecessary mesh-mesh contacts.
- Reduce contact workload through `ContactProperties.maxContactJoints` when scenes are contact-heavy.

## Workflow

1. Establish baseline world parameters (`basicTimeStep`, `ERP`, `CFM`, `gravity`).
2. Validate each dynamic `Solid` mass and inertia setup.
3. Validate joint type and axis/anchor alignment against mechanism kinematics.
4. Validate collision geometry simplicity and correctness.
5. Tune `ContactProperties` for friction, restitution, and softness.
6. Add damping and fluid parameters only as needed.
7. Use plugin-level customization only when built-in fields cannot express required dynamics.
8. Profile and tune runtime cost after behavior correctness is achieved.

## Reference File

Use `references/physics_reference.md` for field-level node reference, callback signatures, and ODE object mapping details.
