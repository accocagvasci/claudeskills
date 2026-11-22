# Webots Advanced Reference

This reference consolidates advanced operational material for Webots integration, scaling, troubleshooting, and extension.

Scope boundaries:

- Exclude beginner controller coding patterns (covered by `webots-controller-programming`).
- Exclude physics plugin internals (covered by `webots-physics`).

## 1) `webots_ros2` Package Structure and Configuration

`webots_ros2` is the official ROS 2 interface package family for Webots.

### Core package roles

- `webots_ros2_driver`
  - Main bridge between Webots devices and ROS 2 topics/services/actions.
  - Publishes sensor messages and consumes actuator commands.
- `webots_ros2_msgs`
  - Message/service definitions used by Webots-specific integrations.
- `webots_ros2_core` (distribution-dependent naming)
  - Shared launcher/utilities for process orchestration.
- `Ros2Supervisor`
  - ROS 2-accessible Supervisor API wrappers for simulation control.

### Typical launch composition

- Webots process via `WebotsLauncher`.
- One `driver` node per robot instance.
- Optional Supervisor control node for scenario resets and orchestration.
- Optional custom plugin libraries attached to the driver.

### Configuration checklist

- `robot_description`
  - Provide URDF or Xacro-expanded XML.
  - Keep link/joint naming and inertial models aligned with real robot stack.
- Namespacing
  - Use per-robot namespace for multi-robot worlds.
  - Keep TF frame names deterministic and collision-free.
- Sensor mapping
  - Camera -> image topics.
  - Lidar/RangeFinder -> scan or point cloud topics.
  - IMU -> imu topic.
  - Encoders/joints -> joint states.
- Time sync
  - Use simulated clock conventions consistently across downstream ROS 2 nodes.

### Source vs apt install

- Package install (example): `apt install ros-humble-webots-ros2`
- Source build preferred for:
  - Unreleased fixes.
  - Custom plugins.
  - Branch pinning for reproducibility.

## 2) Extern Controller Environment Variables

Extern controllers run outside Webots when Robot `controller` is set to `<extern>`.

### Primary connection variables

- `WEBOTS_CONTROLLER_URL`
  - Explicit endpoint for controller connection.
  - Common form: `ipc:///tmp/local_url/ROBOT_NAME`
  - Remote/distributed form uses `tcp://HOST:PORT/ROBOT_NAME` style routing patterns.
- `WEBOTS_HOME`
  - Webots installation root used by launcher utilities and runtime resources.
- `WEBOTS_TMPDIR`
  - Shared temp channel folder (notably important with snap packaging).

### Support variables frequently used in deployments

- `LD_LIBRARY_PATH`
  - Required in some Linux packaging contexts so controller runtime libraries are discoverable.
- `WEBOTS_SAFE_MODE`
  - Forces safer startup profile for recovery from world/OpenGL startup crashes.

### Snap-specific notes

Recommended exports when using snap package:

```bash
export WEBOTS_HOME=/snap/webots/current/usr/share/webots
export LD_LIBRARY_PATH=$WEBOTS_HOME/lib/controller
```

`WEBOTS_TMPDIR` is typically derived automatically from `SNAP_USER_COMMON`.

### Launcher options for extern controllers (`webots-controller`)

- `--help`
- `--protocol=<ipc|tcp>`
- `--ip-address=<ip-address>`
- `--port=<port>`
- `--robot-name=<robot-name>`
- `--interactive` (MATLAB interactive mode)
- `--matlab-path=<matlab-path>`
- `--stdout-redirect`
- `--stderr-redirect`

## 3) TCP/IP Interfacing API Patterns

Webots controllers can expose or consume TCP APIs for external software (MATLAB, LabVIEW, planners, digital twins).

### Deployment patterns

- Controller as TCP server
  - External application connects and sends commands.
  - Good for hardware-in-the-loop dashboards and operator stations.
- Controller as TCP client
  - Controller initiates connection to upstream orchestrator.
  - Good for cloud/distributed evaluation farms.

### Protocol design recommendations

- Frame all messages explicitly (length-prefix or newline-delimited schema).
- Include protocol version in handshake.
- Include command ack/nack and error code taxonomy.
- Add heartbeat and timeout behavior.
- Make commands idempotent when possible.
- Separate high-rate telemetry from low-rate command channel.

### Real-time safety in control loops

- Keep socket IO non-blocking or offloaded to dedicated thread/process.
- Copy inbound command into thread-safe buffer consumed at control step boundary.
- Apply stale-command timeout and safe-stop fallback.

### Minimal Python skeleton (controller-side TCP server)

```python
import socket

HOST, PORT = "0.0.0.0", 9000
server = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
server.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
server.bind((HOST, PORT))
server.listen(1)
conn, _ = server.accept()

while True:
    data = conn.recv(1024)
    if not data:
        break
    command = data.decode("utf-8").strip()
    # parse command and update actuator targets
```

## 4) Webots Executable Command-Line Options (Complete Set)

The following options are exposed by Webots startup help text:

### General

- `--help` : display help and exit.
- `--version` : display version and exit.
- `--sysinfo` : display system information and exit.

### Simulation / UI startup

- `--mode=<mode>` : startup mode override (`pause`, `realtime`, or `fast`).
- `--no-rendering` : disable rendering in main 3D view.
- `--fullscreen` : start fullscreen.
- `--minimize` : start minimized.
- `--batch` : disable blocking pop-up dialogs.
- `--clear-cache` : clear Webots cache on startup.

### Logging and IO routing

- `--stdout` : redirect controller stdout to terminal.
- `--stderr` : redirect controller stderr to terminal.
- `--heartbeat[=<time>]` : print periodic heartbeat dot to stdout.
- `--log-performance=<file>[,<steps>]` : performance logging for benchmarking.

### Networking / external integration

- `--port` : TCP port override (default 1234).
- `--stream[=<mode>]` : start streaming server (`w3d` default or `mjpeg`).
- `--extern-urls` : print extern controller URLs to stdout.

### Conversion subcommand

- `convert` : convert PROTO to URDF/WBO/WRL.
  - Supports `-p` to override PROTO parameters.
  - Supports output path via conversion options (`-o` in usage examples).

### Positional

- `[worldfile]` : optional `.wbt` world path to open.

## 5) Known Bugs and Workarounds (Comprehensive Practical Set)

### Cross-platform

- Saving world while simulation is running can accumulate pose drift.
  - Workaround: pause simulation and reset/revert before save.
- High-velocity body tunneling through thin obstacles.
  - Workaround: thicken collision geometry and/or reduce `basicTimeStep`.
- Determinism drift under heavy rendering load.
  - Workaround: benchmark in `--batch --mode=fast` and pin timestep/load.
- Multiple concurrent instances accidentally binding overlapping resources.
  - Workaround: set explicit `--port`, isolate output dirs, and namespace robots.

### Linux

- NVIDIA/mesa driver combinations may show visual artifacts or crashes.
  - Workaround: validate `glxinfo`, pin stable driver versions, test fallback settings.
- Wayland compositor issues (window focus/input/embedding glitches).
  - Workaround: run X11 session for affected workloads.
- AppImage sandbox constraints on some distros.
  - Workaround: run with `--no-sandbox` when required by environment policy.

### macOS

- Metal renderer transparency artifacts in some scenes.
  - Workaround: adjust material/transparency use, cross-check with alternative render settings.
- External tool launches from bundled app paths may fail under shell/env mismatch.
  - Workaround: normalize launch through terminal wrappers with explicit env exports.

### Windows

- Path quoting and shell differences can break extern launcher scripts.
  - Workaround: use absolute quoted paths and keep launcher invocation minimal.
- Mixed terminal encodings may mangle non-ASCII log output.
  - Workaround: enforce UTF-8 terminal/code page in automation scripts.

### Extern controller edge cases

- `robot.synchronization = TRUE` waits indefinitely when extern controller never connects.
  - Workaround: add startup watchdog and explicit controller health checks.
- Multi-robot extern sessions can bind to wrong robot when names collide.
  - Workaround: enforce unique Robot `name` fields and pass `--robot-name`.

## 6) Performance Benchmarking Tips

### Benchmark design

- Separate simulation cost from rendering cost.
- Use fixed world seed and deterministic initial conditions.
- Benchmark one change at a time (sensor set, timestep, collision mesh complexity).
- Record both throughput (sim steps/sec) and objective metric quality.

### High-impact levers

- `basicTimeStep`
  - Typical high-throughput range: 8-64 ms.
  - Smaller timestep increases fidelity but cost rises rapidly.
- Sensor pruning
  - Camera/Lidar/RangeFinder are usually the dominant CPU consumers.
- Collision simplification
  - Replace detailed meshes with simple convex/primitive `boundingObject` definitions.
- Threading
  - Tune `WorldInfo.optimalThreadCount` to host CPU topology.
- Headless runs
  - Use `webots --batch --mode=fast world.wbt` for large sweeps.

### Logging and profiling

- Use `--log-performance=<file>[,<steps>]` for repeatable trend capture.
- Pair logs with scenario metadata (world hash, robot config hash, machine profile).
- Capture `--sysinfo` output with benchmark artifacts for reproducibility.

## 7) Plugin API Reference (Controller Plugin + Robot Window Plugin)

### Controller plugin / remote-control bridge

Purpose:

- Bridge simulation control paths to external systems or real robot communication stacks.

Implementation characteristics:

- Typically C/C++ shared-library style plugin architecture.
- Deterministic serialization and message framing required for stable control.
- Explicit conversion between simulator units/frames and hardware protocol conventions.

Integration notes:

- Keep plugin protocol versioned.
- Include watchdog behavior and fail-safe command fallback.
- Test with packet loss/latency injection before hardware trials.

### Robot Window plugin

Purpose:

- Build custom HTML/JavaScript UI tied to a robot/controller for telemetry and operator controls.

Directory layout:

- Place assets in `plugins/robot_windows/`.
- Typical files: HTML, CSS, JS, static assets.

Communication model:

- JavaScript API exchanges messages with controller-side logic.
- Controller emits serialized data payloads to window.
- Window sends command events back to controller.

Best practices:

- Keep UI message schema versioned.
- Throttle high-frequency telemetry to avoid UI backlog.
- Validate and sanitize all inbound UI-originated commands.

## 8) Build Dependencies by Platform

Use source builds for custom patching, instrumentation, and integration testing.

Repository:

```bash
git clone https://github.com/cyberbotics/webots.git
```

Build commands:

- Linux: `make -j$(nproc)`
- macOS: `make -j$(sysctl -n hw.ncpu)`

### Linux dependency baseline

- Toolchain: `gcc` or `clang`, `make`, `cmake`, `git`, `pkg-config`
- Graphics/OpenGL stack and development headers
- Qt runtime/development packages used by current Webots release
- Audio/media support libraries used by official build scripts
- Python 3 toolchain for auxiliary scripts

### macOS dependency baseline

- Xcode command line tools (`clang`, `make`, SDK headers)
- `cmake`, `git` (Homebrew or equivalent)
- Qt installation compatible with target Webots revision
- Python 3 runtime for tooling scripts

### Windows dependency baseline

- MSYS2/MinGW64 environment aligned with Webots build scripts
- `git`, `cmake`, GNU/clang-compatible toolchain as required by release
- Qt and runtime dependencies matching pinned Webots version

### Build hygiene recommendations

- Pin toolchain versions per CI image.
- Keep submodules and dependencies synchronized to the same commit baseline.
- Run reproducible builds in clean environment containers/VMs.

## 9) Advanced Multi-Robot and Optimization Notes

- Run one controller process per robot for fault isolation.
- Use Emitter/Receiver for in-world low-overhead messaging.
- Use Supervisor for global reset, scoring, and episodic orchestration.
- Use extern + shared memory/message bus for high-throughput distributed coordination.
- Parallelize optimization across multiple worlds/instances using distinct `--port` assignments.

## 10) Quick Command Snippets

Start Webots headless-fast:

```bash
webots --batch --mode=fast my_world.wbt
```

Start extern controller on local IPC:

```bash
export WEBOTS_CONTROLLER_URL=ipc:///tmp/local_url/ROBOT_NAME
python3 my_controller.py
```

Launch controller with launcher against remote Webots:

```bash
$WEBOTS_HOME/webots-controller --protocol=tcp --ip-address=192.168.1.20 --port=1234 --robot-name=ROBOT_A /path/to/controller
```
