# Webots Actuators Reference

This document provides actuator-focused API and node-field reference for Webots controllers.

## Conventions

- Command setters are applied by simulation after the next `wb_robot_step`.
- Device names are retrieved from robot definitions and resolved through controller language bindings.
- Units:
  - angle: radians
  - distance: meters
  - angular velocity: rad/s
  - linear velocity: m/s
  - torque: N·m
  - force: N

## 1) Motor API (RotationalMotor and LinearMotor)

Motor devices are exposed with unified APIs. Method availability can differ by motor type (torque-oriented vs force-oriented helpers).

### 1.1 Core Methods by Language

| Operation | C | C++ | Python | Java | MATLAB |
|---|---|---|---|---|---|
| Get motor device | `wb_robot_get_device` | `Robot::getMotor` | `robot.getDevice` | `robot.getMotor` | `wb_robot_get_device` |
| Set position target | `wb_motor_set_position(tag, value)` | `Motor::setPosition(value)` | `motor.setPosition(value)` | `motor.setPosition(value)` | `wb_motor_set_position(tag, value)` |
| Get position target | `wb_motor_get_target_position(tag)` | `Motor::getTargetPosition()` | `motor.getTargetPosition()` | `motor.getTargetPosition()` | `wb_motor_get_target_position(tag)` |
| Set velocity target | `wb_motor_set_velocity(tag, value)` | `Motor::setVelocity(value)` | `motor.setVelocity(value)` | `motor.setVelocity(value)` | `wb_motor_set_velocity(tag, value)` |
| Get commanded velocity | `wb_motor_get_velocity(tag)` | `Motor::getVelocity()` | `motor.getVelocity()` | `motor.getVelocity()` | `wb_motor_get_velocity(tag)` |
| Get max velocity | `wb_motor_get_max_velocity(tag)` | `Motor::getMaxVelocity()` | `motor.getMaxVelocity()` | `motor.getMaxVelocity()` | `wb_motor_get_max_velocity(tag)` |
| Set acceleration limit | `wb_motor_set_acceleration(tag, value)` | `Motor::setAcceleration(value)` | `motor.setAcceleration(value)` | `motor.setAcceleration(value)` | `wb_motor_set_acceleration(tag, value)` |
| Get acceleration limit | `wb_motor_get_acceleration(tag)` | `Motor::getAcceleration()` | `motor.getAcceleration()` | `motor.getAcceleration()` | `wb_motor_get_acceleration(tag)` |
| Enable force feedback | `wb_motor_enable_force_feedback(tag, ms)` | `Motor::enableForceFeedback(ms)` | `motor.enableForceFeedback(ms)` | `motor.enableForceFeedback(ms)` | `wb_motor_enable_force_feedback(tag, ms)` |
| Disable force feedback | `wb_motor_disable_force_feedback(tag)` | `Motor::disableForceFeedback()` | `motor.disableForceFeedback()` | `motor.disableForceFeedback()` | `wb_motor_disable_force_feedback(tag)` |
| Read force feedback | `wb_motor_get_force_feedback(tag)` | `Motor::getForceFeedback()` | `motor.getForceFeedback()` | `motor.getForceFeedback()` | `wb_motor_get_force_feedback(tag)` |
| Force feedback period | `wb_motor_get_force_feedback_sampling_period(tag)` | `Motor::getForceFeedbackSamplingPeriod()` | `motor.getForceFeedbackSamplingPeriod()` | `motor.getForceFeedbackSamplingPeriod()` | `wb_motor_get_force_feedback_sampling_period(tag)` |
| Enable torque feedback | `wb_motor_enable_torque_feedback(tag, ms)` | `Motor::enableTorqueFeedback(ms)` | `motor.enableTorqueFeedback(ms)` | `motor.enableTorqueFeedback(ms)` | `wb_motor_enable_torque_feedback(tag, ms)` |
| Disable torque feedback | `wb_motor_disable_torque_feedback(tag)` | `Motor::disableTorqueFeedback()` | `motor.disableTorqueFeedback()` | `motor.disableTorqueFeedback()` | `wb_motor_disable_torque_feedback(tag)` |
| Read torque feedback | `wb_motor_get_torque_feedback(tag)` | `Motor::getTorqueFeedback()` | `motor.getTorqueFeedback()` | `motor.getTorqueFeedback()` | `wb_motor_get_torque_feedback(tag)` |
| Torque feedback period | `wb_motor_get_torque_feedback_sampling_period(tag)` | `Motor::getTorqueFeedbackSamplingPeriod()` | `motor.getTorqueFeedbackSamplingPeriod()` | `motor.getTorqueFeedbackSamplingPeriod()` | `wb_motor_get_torque_feedback_sampling_period(tag)` |
| Set direct torque | `wb_motor_set_torque(tag, value)` | `Motor::setTorque(value)` | `motor.setTorque(value)` | `motor.setTorque(value)` | `wb_motor_set_torque(tag, value)` |
| Get max torque | `wb_motor_get_max_torque(tag)` | `Motor::getMaxTorque()` | `motor.getMaxTorque()` | `motor.getMaxTorque()` | `wb_motor_get_max_torque(tag)` |
| Set available torque | `wb_motor_set_available_torque(tag, value)` | `Motor::setAvailableTorque(value)` | `motor.setAvailableTorque(value)` | `motor.setAvailableTorque(value)` | `wb_motor_set_available_torque(tag, value)` |
| Get available torque | `wb_motor_get_available_torque(tag)` | `Motor::getAvailableTorque()` | `motor.getAvailableTorque()` | `motor.getAvailableTorque()` | `wb_motor_get_available_torque(tag)` |
| Set direct force | `wb_motor_set_force(tag, value)` | `Motor::setForce(value)` | `motor.setForce(value)` | `motor.setForce(value)` | `wb_motor_set_force(tag, value)` |
| Get max force | `wb_motor_get_max_force(tag)` | `Motor::getMaxForce()` | `motor.getMaxForce()` | `motor.getMaxForce()` | `wb_motor_get_max_force(tag)` |
| Set available force | `wb_motor_set_available_force(tag, value)` | `Motor::setAvailableForce(value)` | `motor.setAvailableForce(value)` | `motor.setAvailableForce(value)` | `wb_motor_set_available_force(tag, value)` |
| Get available force | `wb_motor_get_available_force(tag)` | `Motor::getAvailableForce()` | `motor.getAvailableForce()` | `motor.getAvailableForce()` | `wb_motor_get_available_force(tag)` |
| Get type | `wb_motor_get_type(tag)` | `Motor::getType()` | `motor.getType()` | `motor.getType()` | `wb_motor_get_type(tag)` |
| Get associated brake | `wb_motor_get_brake(tag)` | `Motor::getBrake()` | `motor.getBrake()` | `motor.getBrake()` | `wb_motor_get_brake(tag)` |
| Get associated sensor | `wb_motor_get_position_sensor(tag)` | `Motor::getPositionSensor()` | `motor.getPositionSensor()` | `motor.getPositionSensor()` | `wb_motor_get_position_sensor(tag)` |

### 1.2 Motor Control Modes

| Mode | Required Calls | Behavior |
|---|---|---|
| Position | `setPosition(target)` | Internal PID tracks target position; velocity/effort limits apply |
| Velocity | `setPosition(INFINITY)` then `setVelocity(v)` | Motor runs continuously at commanded speed subject to limits |
| Torque/Force | `setPosition(INFINITY)`, `setVelocity(INFINITY)`, then `setTorque(t)`/`setForce(f)` | Direct effort command with optional available effort cap |

### 1.3 Motor Node Fields (Actuation and Dynamics)

Common fields used by `RotationalMotor` and `LinearMotor` nodes:

| Field | Type | Meaning |
|---|---|---|
| `name` | `SFString` | Device name used in controller lookup |
| `maxVelocity` | `SFFloat` | Upper velocity bound for command clamping |
| `maxTorque` / `maxForce` | `SFFloat` | Physical upper effort capability |
| `minPosition` | `SFFloat` | Lower position bound when finite |
| `maxPosition` | `SFFloat` | Upper position bound when finite |
| `multiplier` | `SFFloat` | Conversion factor between motor axis and joint transform |
| `controlPID` | `SFVec3f` | Position controller gains `[P I D]` |
| `acceleration` | `SFFloat` | Velocity ramp limit |
| `sound` | `SFString` | Optional motor sound profile |
| `consumptionFactor` | `SFFloat` | Power consumption modeling factor |
| `type` | enum-like string | Motor behavior category (implementation dependent) |

Note: Exact field set may vary by Webots version and node subtype; use world/node documentation for version-specific defaults and optional fields.

### 1.4 PID Control Parameters (`controlPID`)

- `P` (proportional gain): increases correction strength based on position error.
- `I` (integral gain): accumulates residual error; reduces steady-state offset.
- `D` (derivative gain): damps overshoot by reacting to error change rate.

Practical tuning sequence:

1. Increase `P` until response is fast but not unstable.
2. Add small `D` to damp oscillation.
3. Add minimal `I` only when steady-state bias remains.
4. Re-check limits (`maxVelocity`, `maxTorque`/`maxForce`, `acceleration`) after tuning.

## 2) Brake

### 2.1 API by Language

| Operation | C | C++ | Python | Java | MATLAB |
|---|---|---|---|---|---|
| Set damping constant | `wb_brake_set_damping_constant(tag, value)` | `Brake::setDampingConstant(value)` | `brake.setDampingConstant(value)` | `brake.setDampingConstant(value)` | `wb_brake_set_damping_constant(tag, value)` |
| Get associated motor | `wb_brake_get_motor(tag)` | `Brake::getMotor()` | `brake.getMotor()` | `brake.getMotor()` | `wb_brake_get_motor(tag)` |
| Get associated position sensor | `wb_brake_get_position_sensor(tag)` | `Brake::getPositionSensor()` | `brake.getPositionSensor()` | `brake.getPositionSensor()` | `wb_brake_get_position_sensor(tag)` |

### 2.2 Brake Node Fields

| Field | Type | Meaning |
|---|---|---|
| `name` | `SFString` | Device name |
| `dampingConstant` | `SFFloat` | Damping magnitude applied at joint |

## 3) LED

### 3.1 API by Language

| Operation | C | C++ | Python | Java | MATLAB |
|---|---|---|---|---|---|
| Set LED value/color | `wb_led_set(tag, value)` | `LED::set(value)` | `led.set(value)` | `led.set(value)` | `wb_led_set(tag, value)` |

### 3.2 LED Node Fields

| Field | Type | Meaning |
|---|---|---|
| `name` | `SFString` | Device name |
| `color` | `MFColor` | Palette or base color(s) |
| `gradual` | `SFBool` | Enable smooth transitions |

## 4) Display

### 4.1 API by Language (Representative Full Set)

| Operation | C | C++ | Python | Java | MATLAB |
|---|---|---|---|---|---|
| Width / Height | `wb_display_get_width`, `wb_display_get_height` | `Display::getWidth`, `Display::getHeight` | `display.getWidth`, `display.getHeight` | `display.getWidth`, `display.getHeight` | `wb_display_get_width`, `wb_display_get_height` |
| Set color | `wb_display_set_color` | `Display::setColor` | `display.setColor` | `display.setColor` | `wb_display_set_color` |
| Set alpha | `wb_display_set_alpha` | `Display::setAlpha` | `display.setAlpha` | `display.setAlpha` | `wb_display_set_alpha` |
| Set opacity | `wb_display_set_opacity` | `Display::setOpacity` | `display.setOpacity` | `display.setOpacity` | `wb_display_set_opacity` |
| Draw pixel | `wb_display_draw_pixel` | `Display::drawPixel` | `display.drawPixel` | `display.drawPixel` | `wb_display_draw_pixel` |
| Draw line | `wb_display_draw_line` | `Display::drawLine` | `display.drawLine` | `display.drawLine` | `wb_display_draw_line` |
| Draw rectangle | `wb_display_draw_rectangle` | `Display::drawRectangle` | `display.drawRectangle` | `display.drawRectangle` | `wb_display_draw_rectangle` |
| Fill rectangle | `wb_display_fill_rectangle` | `Display::fillRectangle` | `display.fillRectangle` | `display.fillRectangle` | `wb_display_fill_rectangle` |
| Draw oval | `wb_display_draw_oval` | `Display::drawOval` | `display.drawOval` | `display.drawOval` | `wb_display_draw_oval` |
| Fill oval | `wb_display_fill_oval` | `Display::fillOval` | `display.fillOval` | `display.fillOval` | `wb_display_fill_oval` |
| Draw polygon | `wb_display_draw_polygon` | `Display::drawPolygon` | `display.drawPolygon` | `display.drawPolygon` | `wb_display_draw_polygon` |
| Fill polygon | `wb_display_fill_polygon` | `Display::fillPolygon` | `display.fillPolygon` | `display.fillPolygon` | `wb_display_fill_polygon` |
| Draw text | `wb_display_draw_text` | `Display::drawText` | `display.drawText` | `display.drawText` | `wb_display_draw_text` |
| Set font | `wb_display_set_font` | `Display::setFont` | `display.setFont` | `display.setFont` | `wb_display_set_font` |
| Image load/new | `wb_display_image_load` / `wb_display_image_new` | `Display::imageLoad` / `Display::imageNew` | `display.imageLoad` / `display.imageNew` | `display.imageLoad` / `display.imageNew` | `wb_display_image_load` / `wb_display_image_new` |
| Image paste | `wb_display_image_paste` | `Display::imagePaste` | `display.imagePaste` | `display.imagePaste` | `wb_display_image_paste` |
| Image copy | `wb_display_image_copy` | `Display::imageCopy` | `display.imageCopy` | `display.imageCopy` | `wb_display_image_copy` |
| Image save | `wb_display_image_save` | `Display::imageSave` | `display.imageSave` | `display.imageSave` | `wb_display_image_save` |
| Image delete | `wb_display_image_delete` | `Display::imageDelete` | `display.imageDelete` | `display.imageDelete` | `wb_display_image_delete` |
| Attach camera image | `wb_display_image_paste` (camera buffer usage) | `Display::imagePaste` | `display.imagePaste` | `display.imagePaste` | `wb_display_image_paste` |

### 4.2 Display Node Fields

| Field | Type | Meaning |
|---|---|---|
| `name` | `SFString` | Device name |
| `width` | `SFInt32` | Pixel width |
| `height` | `SFInt32` | Pixel height |
| `channel` | `SFInt32` | Optional channel when linked with communication workflows |

## 5) Emitter

### 5.1 API by Language

| Operation | C | C++ | Python | Java | MATLAB |
|---|---|---|---|---|---|
| Send packet | `wb_emitter_send(tag, data, size)` | `Emitter::send(data, size)` | `emitter.send(data)` | `emitter.send(data)` | `wb_emitter_send(tag, data, size)` |
| Set channel | `wb_emitter_set_channel(tag, c)` | `Emitter::setChannel(c)` | `emitter.setChannel(c)` | `emitter.setChannel(c)` | `wb_emitter_set_channel(tag, c)` |
| Get channel | `wb_emitter_get_channel(tag)` | `Emitter::getChannel()` | `emitter.getChannel()` | `emitter.getChannel()` | `wb_emitter_get_channel(tag)` |
| Set range | `wb_emitter_set_range(tag, r)` | `Emitter::setRange(r)` | `emitter.setRange(r)` | `emitter.setRange(r)` | `wb_emitter_set_range(tag, r)` |
| Get range | `wb_emitter_get_range(tag)` | `Emitter::getRange()` | `emitter.getRange()` | `emitter.getRange()` | `wb_emitter_get_range(tag)` |
| Buffer size | `wb_emitter_get_buffer_size(tag)` | `Emitter::getBufferSize()` | `emitter.getBufferSize()` | `emitter.getBufferSize()` | `wb_emitter_get_buffer_size(tag)` |

### 5.2 Emitter Node Fields

| Field | Type | Meaning |
|---|---|---|
| `name` | `SFString` | Device name |
| `channel` | `SFInt32` | Communication channel |
| `baudRate` | `SFInt32` | Serial mode transmission rate |
| `byteSize` | `SFInt32` | Serial frame byte size |
| `bufferSize` | `SFInt32` | Outgoing buffer capacity |
| `range` | `SFFloat` | Emission distance limit |
| `aperture` | `SFFloat` | Emission cone angle |
| `type` | `SFString` | `radio`, `serial`, or `infrared` |

## 6) Speaker

### 6.1 API by Language

| Operation | C | C++ | Python | Java | MATLAB |
|---|---|---|---|---|---|
| Play sound | `wb_speaker_play_sound` | `Speaker::playSound` | `speaker.playSound` | `speaker.playSound` | `wb_speaker_play_sound` |
| Stop sound | `wb_speaker_stop(tag, sound)` | `Speaker::stop(sound)` | `speaker.stop(sound)` | `speaker.stop(sound)` | `wb_speaker_stop(tag, sound)` |
| Is sound playing | `wb_speaker_is_sound_playing` | `Speaker::isSoundPlaying` | `speaker.isSoundPlaying` | `speaker.isSoundPlaying` | `wb_speaker_is_sound_playing` |
| Set engine | `wb_speaker_set_engine` | `Speaker::setEngine` | `speaker.setEngine` | `speaker.setEngine` | `wb_speaker_set_engine` |
| Set language | `wb_speaker_set_language` | `Speaker::setLanguage` | `speaker.setLanguage` | `speaker.setLanguage` | `wb_speaker_set_language` |
| Speak text | `wb_speaker_speak(tag, text, volume)` | `Speaker::speak(text, volume)` | `speaker.speak(text, volume)` | `speaker.speak(text, volume)` | `wb_speaker_speak(tag, text, volume)` |
| Is speaking | `wb_speaker_is_speaking(tag)` | `Speaker::isSpeaking()` | `speaker.isSpeaking()` | `speaker.isSpeaking()` | `wb_speaker_is_speaking(tag)` |

### 6.2 Speaker Node Fields

| Field | Type | Meaning |
|---|---|---|
| `name` | `SFString` | Device name |
| `engine` | `SFString` | Speech synthesis backend |
| `language` | `SFString` | Default speech language |

## 7) Pen

### 7.1 API by Language

| Operation | C | C++ | Python | Java | MATLAB |
|---|---|---|---|---|---|
| Enable/disable writing | `wb_pen_write(tag, bool)` | `Pen::write(bool)` | `pen.write(bool)` | `pen.write(bool)` | `wb_pen_write(tag, bool)` |
| Set ink color + density | `wb_pen_set_ink_color(tag, rgb, density)` | `Pen::setInkColor(rgb, density)` | `pen.setInkColor(rgb, density)` | `pen.setInkColor(rgb, density)` | `wb_pen_set_ink_color(tag, rgb, density)` |

### 7.2 Pen Node Fields

| Field | Type | Meaning |
|---|---|---|
| `name` | `SFString` | Device name |
| `inkColor` | `SFColor` | Default drawing color |
| `leadSize` | `SFFloat` | Stroke thickness |
| `write` | `SFBool` | Initial write state |

## 8) Propeller

Propeller is an actuator node driven indirectly by motorized rotation; no dedicated controller device API exists.

### 8.1 Control Path

- Attach a `RotationalMotor` to the propeller joint.
- Command angular speed/torque via motor API.
- Let physics apply thrust/drag using node constants.

### 8.2 Propeller Node Fields

| Field | Type | Meaning |
|---|---|---|
| `thrustConstants` | `SFVec3f`/`SFVec4f` (version dependent) | Polynomial/coefficients mapping rotor speed to thrust |
| `torqueConstants` | `SFVec3f`/`SFVec4f` (version dependent) | Counter-torque model coefficients |
| `fastHelixThreshold` | `SFFloat` | Threshold where simplified aerodynamic model may apply |
| `device` | `SFNode` | Linked motor/joint device chain |

## 9) Connector

### 9.1 API by Language

| Operation | C | C++ | Python | Java | MATLAB |
|---|---|---|---|---|---|
| Lock | `wb_connector_lock(tag)` | `Connector::lock()` | `connector.lock()` | `connector.lock()` | `wb_connector_lock(tag)` |
| Unlock | `wb_connector_unlock(tag)` | `Connector::unlock()` | `connector.unlock()` | `connector.unlock()` | `wb_connector_unlock(tag)` |
| Is locked | `wb_connector_is_locked(tag)` | `Connector::isLocked()` | `connector.isLocked()` | `connector.isLocked()` | `wb_connector_is_locked(tag)` |
| Presence check | `wb_connector_get_presence(tag)` | `Connector::getPresence()` | `connector.getPresence()` | `connector.getPresence()` | `wb_connector_get_presence(tag)` |
| Enable presence sensing | `wb_connector_enable_presence(tag, ms)` | `Connector::enablePresence(ms)` | `connector.enablePresence(ms)` | `connector.enablePresence(ms)` | `wb_connector_enable_presence(tag, ms)` |
| Disable presence sensing | `wb_connector_disable_presence(tag)` | `Connector::disablePresence()` | `connector.disablePresence()` | `connector.disablePresence()` | `wb_connector_disable_presence(tag)` |
| Presence period | `wb_connector_get_presence_sampling_period(tag)` | `Connector::getPresenceSamplingPeriod()` | `connector.getPresenceSamplingPeriod()` | `connector.getPresenceSamplingPeriod()` | `wb_connector_get_presence_sampling_period(tag)` |

### 9.2 Connector Node Fields

| Field | Type | Meaning |
|---|---|---|
| `name` | `SFString` | Device name |
| `type` | `SFString` | Connector behavior/type |
| `distanceTolerance` | `SFFloat` | Max distance for auto-attachment |
| `axisTolerance` | `SFFloat` | Angular alignment tolerance |
| `rotationTolerance` | `SFFloat` | Twist/alignment tolerance |
| `numberOfRotations` | `SFInt32` | Rotation symmetry count |
| `isLocked` | `SFBool` | Initial lock state |

## 10) Muscle

Muscle is a visual deformation/annotation node showing contraction between attachment points; direct actuator device commands are not exposed.

### 10.1 Muscle Node Fields

| Field | Type | Meaning |
|---|---|---|
| `startPoint` | `SFNode`/`SFVec3f` | First attachment |
| `endPoint` | `SFNode`/`SFVec3f` | Second attachment |
| `radius` | `SFFloat` | Muscle visual thickness |
| `color` | `SFColor` | Muscle color |
| `castShadows` | `SFBool` | Shadow rendering option |

## 11) Track

Track simulates moving belts/treads and is generally driven by motors attached to sprocket or wheel joints.

### 11.1 Control Path

- Set track propulsion speed through associated motors (velocity mode typical).
- Configure visual scrolling via `animationStep` on the `Track` node.

### 11.2 Track Node Fields

| Field | Type | Meaning |
|---|---|---|
| `name` | `SFString` | Track identifier |
| `textureAnimation` / `animationStep` | `SFFloat` | Visual belt movement increment |
| `geometriesCount` | `SFInt32` | Number of repeated segments |
| `device` | `SFNode` | Linked kinematic/drive chain |

## 12) Common Command Patterns

### 12.1 Differential Drive (Velocity Mode)

- Set both wheel motors to infinite position.
- Command per-wheel velocities.
- Apply at each control step.

### 12.2 Manipulator Joint Targeting (Position Mode)

- Set finite target on each joint motor.
- Bound speed using `setVelocity` and/or acceleration limits.
- Use joint sensors for convergence checks.

### 12.3 Effort-Limited Contact Interaction

- Set infinite position and velocity.
- Apply torque/force command.
- Clamp with `setAvailableTorque`/`setAvailableForce` for safe contact.

## 13) API-Node Mapping Summary

| Actuator | Primary Device API | Main Node Fields |
|---|---|---|
| RotationalMotor | `setPosition`, `setVelocity`, `setTorque`, feedback methods | `maxVelocity`, `maxTorque`, `controlPID`, `acceleration` |
| LinearMotor | `setPosition`, `setVelocity`, `setForce`, feedback methods | `maxVelocity`, `maxForce`, `controlPID`, `acceleration` |
| Brake | `setDampingConstant` | `dampingConstant` |
| LED | `set` | `color`, `gradual` |
| Display | draw/image/font/color API | `width`, `height` |
| Emitter | `send`, `setChannel`, `setRange` | `channel`, `type`, `range`, `bufferSize` |
| Speaker | `playSound`, `speak`, `stop` | `engine`, `language` |
| Pen | `write`, `setInkColor` | `inkColor`, `leadSize`, `write` |
| Propeller | indirect via Motor API | `thrustConstants`, `torqueConstants` |
| Connector | `lock`, `unlock`, `isLocked`, presence API | tolerance and lock fields |
| Muscle | no direct controller API | attachment and rendering fields |
| Track | indirect via Motor API | `animationStep`/texture animation fields |

## 14) Notes on Cross-Language Equivalence

- C API is canonical low-level naming (`wb_<device>_<action>`).
- C++/Python/Java expose object-style wrappers with near 1:1 method mapping.
- MATLAB typically mirrors C API names and signatures.
- Preserve control sequence ordering across all bindings (for example, set infinity position before velocity mode).
