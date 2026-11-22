---
name: webots-advanced
description: "Use this skill for advanced Webots topics: ROS/ROS2 integration, transfer to real robots, numerical optimization, extern robot controllers, TCP/IP interfacing, performance optimization, known bugs/workarounds, plugin development (controller/robot window), building Webots from source, and multi-robot coordination. Triggers on: webots ROS, ROS2, webots_ros2, transfer to robot, extern controller, TCP/IP, optimization, known bugs, plugin, multi-robot, numerical optimization."
---

# Webots Advanced Topics

Use this skill to implement and troubleshoot advanced Webots workflows involving ROS 2 integration, simulator-to-hardware transfer, external process orchestration, optimization loops, runtime scaling, plugin-based extensibility, and source builds.

Do not use this skill for basic controller coding patterns; refer to `webots-controller-programming`.
Do not use this skill for physics plugin internals; refer to `webots-physics`.

## Apply This Skill When

- Integrating Webots with ROS 2 (`webots_ros2`) including launch, URDF/Xacro, and topic wiring.
- Running controllers outside Webots (`<extern>`) for IDE debugging, distributed systems, or external middleware.
- Bridging Webots with third-party applications through TCP/IP.
- Performing optimization-in-the-loop and simulation-driven parameter tuning.
- Coordinating multi-robot systems and Supervisor-managed orchestration.
- Building custom Robot Window or remote-control plugin workflows.
- Diagnosing platform-specific bugs and applying known workarounds.
- Building Webots from source for patching, profiling, or advanced customization.

## ROS 2 Integration (`webots_ros2`)

Treat `webots_ros2` as the official ROS 2 interface.

- Install from packages when available:
  - `apt install ros-humble-webots-ros2`
- Build from source when custom patches or unreleased features are required.

### Core Components

- `webots_ros2_driver`: main interface node between Webots devices and ROS 2 graph.
- `Ros2Supervisor`: exposes Supervisor functionality through ROS 2 for runtime world and robot control.
- Plugin system: add custom translation logic for specific Webots devices or domain-specific message flows.

### Launch Pattern

Use the canonical launcher + driver composition:

```python
from launch import LaunchDescription
from launch.substitutions import LaunchConfiguration
from webots_ros2_driver.webots_launcher import WebotsLauncher

def generate_launch_description():
    webots = WebotsLauncher(world='my_world.wbt')
    robot_driver = Node(
        package='webots_ros2_driver',
        executable='driver',
        parameters=[{'robot_description': robot_description}]
    )
    return LaunchDescription([webots, robot_driver])
```

### Robot Description and Topics

- Generate `robot_description` from URDF/Xacro for frame consistency and reuse across simulation and real robots.
- Map Webots sensors to standard ROS 2 topics and message types (for example image, laser scan, IMU, joint state).
- Keep namespace and TF conventions aligned with downstream navigation, perception, and control stacks.

## Transfer to Real Robots

Use one of three transfer approaches:

1. Remote Control Plugin: keep controller on PC and send commands to the real robot.
2. Cross-Compilation: compile C/C++ controller for the robot CPU and deploy binary.
3. Interpreted Transfer: deploy Python controller script directly when runtime supports it.

### Remote Control Plugin Notes

- Implement plugin bridge in C/C++.
- Preserve command timing and actuator update cadence between simulation and hardware loop.
- Synchronize units, saturation limits, encoder scaling, and coordinate frames.

### Sim-to-Real Calibration

- Match motor limits, friction, inertial values, latency, sensor noise, and control rate.
- Validate against recorded real telemetry and iterate on model parameters.
- Prefer domain randomization and tolerance bands over single-point fitting.

## Extern Robot Controllers

Run controller process outside Webots when debugging with full IDE tooling, integrating ROS 2 stacks, or splitting workloads across hosts.

- Set Robot `controller` field to `<extern>`.
- Define `WEBOTS_CONTROLLER_URL` for transport endpoint.

```bash
export WEBOTS_CONTROLLER_URL=ipc:///tmp/local_url/ROBOT_NAME
python3 my_controller.py
```

Common endpoint styles:

- `ipc://` for local low-latency process communication.
- `tcp://` for remote/distributed execution.

## TCP/IP Interfacing

Use controller-side sockets to connect Webots with MATLAB, LabVIEW, custom C++/Python tools, and external supervisory software.

- Implement robot controller as TCP server or client.
- Define compact command protocol (binary or line-based) with explicit versioning.
- Include timeout, reconnect, heartbeat, and command acknowledgment semantics.
- Decouple network IO thread from control loop to protect simulation step determinism.

## Numerical Optimization

Use Webots as an evaluation backend for optimization algorithms.

- Supervisor applies candidate parameters.
- Reset simulation per trial.
- Run until termination criterion.
- Compute fitness and feed optimizer.

Supported patterns include genetic algorithms, gradient-based tuning, Bayesian optimization, and multi-objective workflows.

```python
# Optimization loop pattern
supervisor = Supervisor()
for generation in range(N_GENERATIONS):
    for individual in population:
        # Apply parameters to robot
        supervisor.simulationReset()
        # Run simulation
        while supervisor.step(timestep) != -1:
            if simulation_complete():
                break
        fitness = evaluate()
```

For multi-objective workloads, distribute candidates across parallel worlds/processes and aggregate Pareto metrics offline.

## Performance Optimization

Tune simulation cost before scaling experiments.

- Choose `basicTimeStep` in practical range (typically 8-64 ms).
- Set `WorldInfo.optimalThreadCount` to exploit multi-core physics execution.
- Disable unused high-cost sensors (Camera, Lidar, RangeFinder).
- Simplify `boundingObject` geometry.
- Reduce rendering overhead via batch/fast mode.

```bash
webots --batch --mode=fast my_world.wbt  # headless fast mode
```

## Known Bugs and Workarounds

General:

- Saving while simulation is running may accumulate pose error; pause and reset before saving.
- High-speed bodies may tunnel through thin geometry; increase collision thickness or reduce `basicTimeStep`.
- macOS Metal renderer may show transparency artifacts; validate with alternate visual settings or fallback testing workflows.

Linux-specific:

- Some NVIDIA driver versions cause rendering artifacts; verify OpenGL path with `glxinfo`.
- Wayland compositor may cause window/input instability; use X11 session when issues appear.
- AppImage execution may require `--no-sandbox` in constrained environments.

## Multi-Robot Coordination

- Run one controller process per robot for clear fault isolation.
- Use Emitter/Receiver channels for in-sim communication.
- Use Supervisor for global coordination, reset sequencing, and scoring.
- Use extern controllers plus shared-memory/message-bus patterns for high-throughput coordination.

## Plugin System

### Controller Plugin (Remote Control)

- Use to connect simulated control interfaces to real robot communication stacks.
- Keep serialization and transport framing deterministic.

### Robot Window Plugin

- Place HTML/JS assets in `plugins/robot_windows/`.
- Use JavaScript bridge API for controller-window communication.
- Build dashboards for telemetry, debugging tools, and command panels.

## Build Webots from Source

Use source builds for deep debugging, patching, profiling, or unreleased functionality testing.

- Requirements: `git`, `cmake`, `gcc`/`clang`, and platform dependencies.
- Clone:

```bash
git clone https://github.com/cyberbotics/webots.git
```

- Build on Linux:

```bash
make -j$(nproc)
```

- Build on macOS:

```bash
make -j$(sysctl -n hw.ncpu)
```

## Execution Checklist

- Confirm requested scope is advanced-topic only (no basic controller primer, no physics plugin deep-dive).
- Select integration path (ROS 2, extern, TCP/IP, plugin, optimization, or multi-robot).
- Apply platform-appropriate bug workarounds early.
- Benchmark and tune performance before large experiment runs.
- Capture reproducible configs in launch files, world files, and reference notes.

For detailed APIs, environment variables, build dependencies, CLI flags, and expanded troubleshooting matrices, use `references/advanced_reference.md`.
