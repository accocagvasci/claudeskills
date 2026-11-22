---
name: webots-core
description: "Use this skill when working with the Webots robotics simulation platform for installation, project setup, understanding core concepts (world files, nodes, scene tree, Robot/MjData equivalent), or basic simulation workflows. Triggers on: webots, robotics simulation, .wbt files, scene tree, WorldInfo, Viewpoint, simulation step, wb_robot_step."
---

# Webots Core

## Purpose

Provide a reliable, implementation-first workflow for Webots fundamentals:
- Install Webots and validate GPU support.
- Set up a standard project structure.
- Understand the world model (`.wbt`, nodes, fields, scene tree).
- Run simulation with correct step timing semantics.

Use the official documentation as ground truth:
- https://cyberbotics.com/doc/guide/
- https://cyberbotics.com/doc/reference/

## Trigger Conditions

Activate this skill when requests include any of the following:
- "webots"
- "robotics simulation"
- ".wbt"
- "scene tree"
- "WorldInfo"
- "Viewpoint"
- "simulation step"
- "wb_robot_step"

Do not cover controller architecture details beyond timing/synchronization semantics.
Do not cover sensor or actuator specifics.

## Check System Requirements First

Validate minimum requirements before installation:
- CPU/RAM: recent dual-core 2 GHz minimum, quad-core recommended, 2 GB RAM minimum.
- GPU: OpenGL 3.3+ capable adapter; prefer NVIDIA or AMD.
- OS support:
  - Linux: Ubuntu 22.04/24.04 officially supported.
  - Windows: Windows 10/11 (64-bit).
  - macOS: macOS 12/13/14.

## Install Webots

### Linux

Install with APT repository (recommended for updates):

```bash
sudo mkdir -p /etc/apt/keyrings
cd /etc/apt/keyrings
sudo wget -q https://cyberbotics.com/Cyberbotics.asc
echo "deb [arch=amd64 signed-by=/etc/apt/keyrings/Cyberbotics.asc] https://cyberbotics.com/debian binary-amd64/" | sudo tee /etc/apt/sources.list.d/Cyberbotics.list
sudo apt update
sudo apt install webots
```

Alternative Linux options:
- Install `.deb` directly.
- Extract tarball and set `WEBOTS_HOME`.
- Install Snap package.

### Windows

Install from the official installer (`webots-<version>_setup.exe`) and complete the wizard.

Use silent installation when needed:

```bash
webots-<version>_setup.exe /SUPPRESSMSGBOXES /VERYSILENT /NOCANCEL /NORESTART /ALLUSERS
```

### macOS

Install via DMG from releases or via Homebrew.

DMG flow:

```bash
curl -L -O https://github.com/cyberbotics/webots/releases/download/<version>/webots-<version>.dmg
open webots-<version>.dmg
```

Homebrew flow:

```bash
brew install --cask webots
```

On Apple Silicon, keep "Open using Rosetta" disabled unless x86 execution is explicitly required.

### Build from Source

Build from source when binaries are unsuitable for target environment constraints.
- Source repo: `https://github.com/cyberbotics/webots`
- Build instructions: Webots GitHub wiki and repository contributing/build documentation.

## Verify GPU Driver and OpenGL

Run Linux OpenGL verification:

```bash
glxinfo | grep OpenGL
```

Interpretation:
- Good: vendor/renderer indicates NVIDIA, AMD, or Intel hardware acceleration.
- Bad: renderer shows Mesa software rasterizer or software fallback.

On all platforms, inspect runtime GPU diagnostics in Webots:
- `Help > OpenGL Information...`

## Build the Correct Mental Model

Apply this hierarchy for all world reasoning:
- **World**: one `.wbt` file represents one scene.
- **Nodes**: each world is a tree of nodes (VRML97 + Webots-specific nodes).
- **Fields**: each node stores properties as fields (scalars, vectors, child nodes, arrays).

Use this equivalence for simulation state reasoning:
- **Webots world + nodes + fields** ~= static scene/model description.
- **Controller process runtime state** ~= dynamic execution state.

Unlike single-process physics APIs, Webots runs simulator and controllers as separate processes synchronized through `wb_robot_step`.

## Understand World File Format (`.wbt`)

Treat `.wbt` as VRML97-based text with a required header.

Required first line:

```text
#VRML_SIM R2025a utf8
```

Every world must contain exactly one root instance each of:
- `WorldInfo`
- `Viewpoint`
- `Background`

Use SI units consistently:
- Length: meters
- Mass: kilograms
- Time: seconds (or milliseconds where explicitly specified)
- Angles: radians

## Configure Core Root Nodes

### `WorldInfo`

Use `WorldInfo` to set global physics and timing defaults:
- `basicTimeStep`: simulation step in milliseconds.
- `gravity`: gravity magnitude.
- `ERP` and `CFM`: contact solver stability behavior.
- `coordinateSystem`: default ENU.

### `Viewpoint`

Use `Viewpoint` to define camera defaults:
- `position`
- `orientation`
- `fieldOfView`
- optional follow behavior (`follow`, `followType`, `followSmoothness`)

### `Background`

Use `Background` to define visual environment:
- `skyColor`
- optional cube map URLs
- `luminosity` for PBR background lighting contribution

## Use ENU Coordinate System (R2022a+)

Use ENU as default world convention:
- X+: East
- Y+: North
- Z+: Up

Interpret motion, transforms, and camera framing in ENU unless a world explicitly changes `WorldInfo.coordinateSystem`.

## Create Standard Project Layout

Use this baseline structure:

```text
my_project/
  worlds/
    my_world.wbt
    .my_world.wbproj
    .my_world.jpg
  controllers/
  protos/
  plugins/
```

Meanings:
- `worlds/`: world files and per-world project metadata.
- `controllers/`: controller directories referenced by `Robot.controller`.
- `protos/`: reusable PROTO definitions.
- `plugins/`: project plugins (for example, physics plugin referenced by `WorldInfo.physics`).

## Create a New Project Directory in UI

Create project scaffold with built-in tooling:
- `File > New > New Project Directory...`

This flow initializes the expected hierarchy and default location behavior for world/controller/proto assets.

## Start Webots and Orient in the UI

Launch Webots and identify the four primary panels:
- **3D Window**: interactive rendering and object manipulation.
- **Scene Tree**: world node/field hierarchy editor.
- **Console**: simulator and controller logs.
- **Text Editor**: source/world/proto text editing.

## Use Simulation Modes Intentionally

Control simulation from `Simulation` menu/toolbar:
- **Pause**: stop execution.
- **Step**: execute one `WorldInfo.basicTimeStep` increment.
- **Real-time**: run near wall-clock speed.
- **Fast**: run as fast as possible.

## Apply `wb_robot_step` Synchronization Rule

Use this rule for every controller loop:
- `wb_robot_step(control_step_ms)` synchronizes controller with simulator.
- Sensor/actuator exchange happens at step boundaries.
- Commands issued before `wb_robot_step` are applied on the next step sync.

Minimal timing loop example (concept only):

```c
#include <webots/robot.h>

int main() {
  wb_robot_init();
  const int CONTROL_STEP_MS = 32;

  while (wb_robot_step(CONTROL_STEP_MS) != -1) {
    /* Read state, compute logic, queue commands for next sync. */
  }

  wb_robot_cleanup();
  return 0;
}
```

## Enforce Simulation Step vs Control Step Constraints

Apply strict timing compatibility:
- Simulation step = `WorldInfo.basicTimeStep`.
- Control step = argument to `wb_robot_step`.
- Control step must be an integer multiple of simulation step.

Example:
- If `basicTimeStep = 16`, valid control steps are `16`, `32`, `64`, `128`, etc.

## Understand Model Loading Boundaries

Treat model and runtime as separate layers:
- `.wbt` loads into simulator as world model.
- Controllers run in separate processes.
- Simulation state progresses only through synchronization calls.

This separation is the key reason why writing a value and reading a fresh simulated response without an intervening `wb_robot_step` is invalid.

## Hello World: Minimal Falling Box World

Use this world as a known-good baseline for installation and physics verification:

```text
#VRML_SIM R2025a utf8

WorldInfo {
  basicTimeStep 16
  gravity 9.81
  ERP 0.2
  CFM 0.00001
  coordinateSystem "ENU"
}

Viewpoint {
  orientation 0 0 1 0
  position -2.5 0 1.6
}

Background {
  skyColor [
    0.6 0.8 1
  ]
}

TexturedBackground {
}

TexturedBackgroundLight {
}

Floor {
  translation 0 0 0
  size 4 4
}

Solid {
  translation 0 0 0.35
  children [
    Shape {
      appearance PBRAppearance {
        baseColor 0.85 0.2 0.2
        roughness 0.5
        metalness 0
      }
      geometry Box {
        size 0.2 0.2 0.2
      }
    }
  ]
  boundingObject Box {
    size 0.2 0.2 0.2
  }
  physics Physics {
    density 1000
  }
  name "falling_box"
}
```

Expected result:
- Scene loads with a floor and one red box.
- Running simulation in `Real-time` or `Fast` causes the box to fall and settle on the floor.

## Verify Installation Checklist

Use this checklist before any deeper debugging:
1. Launch Webots without crash.
2. Open `Help > OpenGL Information...` and confirm hardware OpenGL driver.
3. Create project via `File > New > New Project Directory...`.
4. Save and reopen a `.wbt` in `worlds/` successfully.
5. Run the minimal falling-box world in `Step`, `Real-time`, and `Fast` modes.
6. Confirm `Step` advances one `basicTimeStep` increment per click.
7. Confirm console is active and no fatal runtime errors appear.

## Reference File

Load `references/core_reference.md` for concise field definitions, keyboard shortcuts, file hierarchy details, and migration notes.
