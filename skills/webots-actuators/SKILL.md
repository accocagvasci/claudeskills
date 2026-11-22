---
name: webots-actuators
description: "Use this skill when working with any Webots actuator - RotationalMotor, LinearMotor, Brake, LED, Display, Emitter, Speaker, Pen, Propeller, Connector, Muscle, or Track. Covers motor control modes (position, velocity, torque), actuator configuration, and command patterns. Triggers on: webots motor, setPosition, setVelocity, actuator, LED, display, emitter, propeller, brake, speaker, track."
---

# Webots Actuators Skill

Use this skill to implement and troubleshoot actuator control in Webots controllers, with emphasis on RotationalMotor and LinearMotor behavior, actuator node configuration, and per-step command semantics.

## Scope

- Cover only actuator devices and actuator-related node fields.
- Exclude sensor usage patterns (handled by `webots-sensors`).
- Exclude basic controller bootstrapping and generic simulation setup.

## Core Execution Model

- Treat motor and actuator setter calls as buffered commands applied on the next `wb_robot_step`.
- Issue all actuator commands for a cycle before stepping.
- Read-back methods (for example, target position or velocity) reflect controller-side state and/or the latest simulation update depending on API semantics.

## Motor-First Workflow (RotationalMotor + LinearMotor)

Start from motor control mode, because most actuator behaviors in mobile robots, manipulators, and mechanism-driven assets reduce to motor commands.

### 1) Position control (default mode)

Python:

```python
motor = robot.getDevice("my_motor")
motor.setPosition(1.57)  # target in radians (rotational) or meters (linear)
```

C:

```c
WbDeviceTag motor = wb_robot_get_device("my_motor");
wb_motor_set_position(motor, 1.57);  // rad (rotational) or m (linear)
```

Use for joint angle/extension targets with internal PID position tracking (`controlPID` and related motor fields on the node).

### 2) Velocity control (continuous rotation/translation)

Python:

```python
motor = robot.getDevice("my_motor")
motor.setPosition(float('inf'))  # MUST set position to infinity first
motor.setVelocity(6.28)  # rad/s or m/s
```

C:

```c
#include <math.h>

WbDeviceTag motor = wb_robot_get_device("my_motor");
wb_motor_set_position(motor, INFINITY);  // MUST set infinity first
wb_motor_set_velocity(motor, 6.28);      // rad/s or m/s
```

Apply this mode to wheel drives, conveyor rollers, and continuous actuators.

### 3) Torque/force control

Python:

```python
motor = robot.getDevice("my_motor")
motor.setPosition(float('inf'))
motor.setVelocity(float('inf'))  # remove velocity limit
motor.setTorque(0.5)  # N·m (rotational) or N (linear)
```

C:

```c
#include <math.h>

WbDeviceTag motor = wb_robot_get_device("my_motor");
wb_motor_set_position(motor, INFINITY);
wb_motor_set_velocity(motor, INFINITY);  // remove velocity cap
wb_motor_set_torque(motor, 0.5);         // N·m (rotational) or N (linear)
```

Use for dynamic interaction, compliant contact behavior, or direct effort-limited control.

### 4) Motor parameters and limits

- Respect node-defined limits (`maxVelocity`, `maxTorque`/`maxForce`, acceleration/jerk constraints where configured).
- Use runtime caps for safer behavior:
  - Python: `setAvailableTorque()`, `setAvailableForce()`
  - C: `wb_motor_set_available_torque()`, `wb_motor_set_available_force()`
- Use feedback/introspection methods during control loops:
  - `getTargetPosition()`, `getVelocity()`, `getMaxVelocity()`, `getMaxTorque()/getMaxForce()`
- Pair motors with `PositionSensor` for closed-loop application logic and convergence checks.

## Differential Drive Pattern (Canonical)

Python:

```python
left_motor = robot.getDevice("left wheel motor")
right_motor = robot.getDevice("right wheel motor")
left_motor.setPosition(float('inf'))
right_motor.setPosition(float('inf'))
left_motor.setVelocity(5.0)
right_motor.setVelocity(5.0)
```

C:

```c
#include <math.h>

WbDeviceTag left_motor = wb_robot_get_device("left wheel motor");
WbDeviceTag right_motor = wb_robot_get_device("right wheel motor");
wb_motor_set_position(left_motor, INFINITY);
wb_motor_set_position(right_motor, INFINITY);
wb_motor_set_velocity(left_motor, 5.0);
wb_motor_set_velocity(right_motor, 5.0);
```

## Actuator-Specific Patterns

### Brake

- Apply damping on the same joint as a motor.
- Use `setDampingConstant(value)` / `wb_brake_set_damping_constant(tag, value)`.
- Treat as dissipative braking, not commanded position holding.

Python:

```python
brake = robot.getDevice("joint_brake")
brake.setDampingConstant(0.8)
```

C:

```c
WbDeviceTag brake = wb_robot_get_device("joint_brake");
wb_brake_set_damping_constant(brake, 0.8);
```

### LED

- Use boolean/integer for mono LEDs (`0` off, `1` on).
- Use packed RGB integer for color LEDs (for example `0xFF0000` for red).

Python:

```python
led = robot.getDevice("status_led")
led.set(0xFF0000)
```

C:

```c
WbDeviceTag led = wb_robot_get_device("status_led");
wb_led_set(led, 0xFF0000);
```

### Display

- Use immediate drawing API for HUD/in-robot visualization.
- Common primitives: `drawPixel`, `drawLine`, `drawRectangle`, `drawOval`, `drawText`.
- Color/transparency controls: `setColor`, `setAlpha`, `setOpacity`.
- Image workflow: `imageNew`, `imageCopy`, `imagePaste`, `imageSave`.

Python:

```python
display = robot.getDevice("screen")
display.setColor(0x00FF00)
display.drawText("READY", 4, 14)
display.drawLine(0, 0, 63, 31)
```

C:

```c
WbDeviceTag display = wb_robot_get_device("screen");
wb_display_set_color(display, 0x00FF00);
wb_display_draw_text(display, "READY", 4, 14);
wb_display_draw_line(display, 0, 0, 63, 31);
```

### Emitter

- Send byte payloads to compatible Receiver devices.
- Use `setChannel(channel)` for multiplexed links.
- Use `setRange(distance)` to constrain transmission radius when applicable.
- Account for emitter type (`radio`, `serial`, `infrared`) at node configuration time.

Python:

```python
emitter = robot.getDevice("tx")
emitter.setChannel(2)
emitter.setRange(10.0)
emitter.send(b"go")
```

C:

```c
const char payload[] = "go";
WbDeviceTag emitter = wb_robot_get_device("tx");
wb_emitter_set_channel(emitter, 2);
wb_emitter_set_range(emitter, 10.0);
wb_emitter_send(emitter, payload, sizeof(payload) - 1);
```

### Speaker

- Use `playSound` for file/sample playback control (volume, pitch, balance, looping).
- Use `speak` for text-to-speech.
- Use `stop` to terminate an active sound.

Python:

```python
speaker = robot.getDevice("speaker")
speaker.playSound(speaker, speaker, "sounds/beep.wav", 1.0, 1.0, 0.0, False)
speaker.speak("dock complete", 1.0)
```

C:

```c
WbDeviceTag speaker = wb_robot_get_device("speaker");
wb_speaker_play_sound(speaker, speaker, "sounds/beep.wav", 1.0, 1.0, 0.0, false);
wb_speaker_speak(speaker, "dock complete", 1.0);
```

### Pen

- Use `write(on)` to enable/disable drawing traces.
- Use `setInkColor(color, density)` for color and opacity-like density control.
- Drawing occurs when pen geometry contacts surfaces.

Python:

```python
pen = robot.getDevice("trail_pen")
pen.setInkColor(0x0000FF, 0.8)
pen.write(True)
```

C:

```c
WbDeviceTag pen = wb_robot_get_device("trail_pen");
wb_pen_set_ink_color(pen, 0x0000FF, 0.8);
wb_pen_write(pen, true);
```

### Propeller

- Use the joint motor for control; Propeller has no dedicated device API.
- Configure `thrustConstants` and `torqueConstants` in the `Propeller` node.
- Command rotor speed with motor velocity/torque methods.

Python:

```python
rotor = robot.getDevice("propeller motor")
rotor.setPosition(float('inf'))
rotor.setVelocity(120.0)
```

C:

```c
#include <math.h>

WbDeviceTag rotor = wb_robot_get_device("propeller motor");
wb_motor_set_position(rotor, INFINITY);
wb_motor_set_velocity(rotor, 120.0);
```

### Connector

- Use `lock()` / `unlock()` to command docking latches.
- Use `isLocked()` to check local state.
- Rely on compatible connector alignment/proximity for successful auto-attachment.

Python:

```python
connector = robot.getDevice("dock")
connector.lock()
locked = connector.isLocked()
```

C:

```c
WbDeviceTag connector = wb_robot_get_device("dock");
wb_connector_lock(connector);
bool locked = wb_connector_is_locked(connector);
```

### Muscle

- Treat as visual-only actuator representation linked to articulated motion.
- Configure attachment points and appearance in node fields.
- Drive visible contraction indirectly via associated joints/motors.

### Track

- Use motorized joints to command track speed.
- Configure visual motion with `animationStep` on `Track` node.
- Apply to tank treads and conveyor-style systems.

Python:

```python
track_motor = robot.getDevice("left track motor")
track_motor.setPosition(float('inf'))
track_motor.setVelocity(3.0)
```

C:

```c
#include <math.h>

WbDeviceTag track_motor = wb_robot_get_device("left track motor");
wb_motor_set_position(track_motor, INFINITY);
wb_motor_set_velocity(track_motor, 3.0);
```

## Troubleshooting Checklist

- Verify the commanded control mode: position vs velocity vs torque/force.
- For velocity mode, verify `setPosition(INFINITY)` is applied before velocity commands.
- For torque/force mode, verify both position and velocity are uncapped (`INFINITY`) before effort commands.
- Check node-level limits (`maxVelocity`, `maxForce`/`maxTorque`, `acceleration`) when observed behavior saturates.
- Confirm that command effects are inspected only after stepping the simulation.

## Cross-Language Mapping Rule

- Python method names map directly to C API with `wb_<device>_<action>` style.
- Keep units identical across languages:
  - rotational position: radians
  - linear position: meters
  - rotational velocity: rad/s
  - linear velocity: m/s
  - rotational torque: N·m
  - linear force: N

## Reference Material

- Use `references/actuators_reference.md` for complete API tables across C/C++/Python/Java/MATLAB, full node field definitions, and PID parameter details.
