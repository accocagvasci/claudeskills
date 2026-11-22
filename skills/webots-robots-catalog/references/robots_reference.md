# Webots Robots Reference

This reference provides device-name maps for high-frequency Webots robots used in research, education, and prototyping.

## Browse Full PROTO Catalog

- Webots Cloud (live PROTO and world browsing): `https://webots.cloud`

## Top 10 Most-Used Robots: Device Name Listings

## 1) E-puck (`E-puck`)

- Motors: `left wheel motor`, `right wheel motor`
- Distance sensors: `ps0`, `ps1`, `ps2`, `ps3`, `ps4`, `ps5`, `ps6`, `ps7`
- LEDs: `led0`, `led1`, `led2`, `led3`, `led4`, `led5`, `led6`, `led7`, `led8`, `led9`
- Camera: `camera`
- Inertial: `accelerometer`
- Optional communication modules by world/PROTO params: `receiver`, `emitter`

## 2) E-puck2 (`E-puck2`)

- Motors: `left wheel motor`, `right wheel motor`
- Distance sensors: `ps0`, `ps1`, `ps2`, `ps3`, `ps4`, `ps5`, `ps6`, `ps7`
- ToF distance: `tof`
- Ground sensors: `gs0`, `gs1`, `gs2`
- Microphones: `microphone`, `microphone right`, `microphone left`, `microphone rear`
- Camera: `camera`
- LEDs: `led0`, `led1`, `led2`, `led3`, `led4`, `led5`, `led6`, `led7`, `led8`, `led9`
- Inertial: `accelerometer`, `gyro`

## 3) TurtleBot3 Burger (`TurtleBot3Burger`)

- Wheel motors: `left wheel motor`, `right wheel motor`
- Wheel sensors: `left wheel sensor`, `right wheel sensor`
- LiDAR: `LDS-01`
- IMU: `accelerometer`, `gyro`, `inertial unit`
- Optional camera (world dependent): `camera`

## 4) TurtleBot3 Waffle Pi (`TurtleBot3WafflePI`)

- Wheel motors: `left wheel motor`, `right wheel motor`
- Wheel sensors: `left wheel sensor`, `right wheel sensor`
- LiDAR: `LDS-01`
- Camera: `camera`, `camera depth`
- IMU: `accelerometer`, `gyro`, `inertial unit`

## 5) Pioneer 3-DX (`Pioneer3dx`)

- Wheel motors: `left wheel`, `right wheel`
- Wheel encoders: `left wheel sensor`, `right wheel sensor`
- Sonars: `so0`, `so1`, `so2`, `so3`, `so4`, `so5`, `so6`, `so7`, `so8`, `so9`, `so10`, `so11`, `so12`, `so13`, `so14`, `so15`
- LiDAR front: `sick lms 291`
- LiDAR rear/top (variant dependent): `sick lms 291 rear`
- Optional cameras (world dependent): `camera`, `camera2`

## 6) UR5e (`UR5e`)

- Joint motors: `shoulder_pan_joint`, `shoulder_lift_joint`, `elbow_joint`, `wrist_1_joint`, `wrist_2_joint`, `wrist_3_joint`
- Joint sensors: `shoulder_pan_joint_sensor`, `shoulder_lift_joint_sensor`, `elbow_joint_sensor`, `wrist_1_joint_sensor`, `wrist_2_joint_sensor`, `wrist_3_joint_sensor`
- Tool connector (if enabled in world): `tool_connector`

## 7) Franka Panda (`PandaArm`)

- Arm joints: `panda_joint1`, `panda_joint2`, `panda_joint3`, `panda_joint4`, `panda_joint5`, `panda_joint6`, `panda_joint7`
- Joint sensors: `panda_joint1_sensor`, `panda_joint2_sensor`, `panda_joint3_sensor`, `panda_joint4_sensor`, `panda_joint5_sensor`, `panda_joint6_sensor`, `panda_joint7_sensor`
- Gripper joints: `panda_finger_joint1`, `panda_finger_joint2`
- Gripper sensors: `panda_finger_joint1_sensor`, `panda_finger_joint2_sensor`

## 8) NAO (`Nao`)

- Core joints/motors: `HeadYaw`, `HeadPitch`, `LShoulderPitch`, `LShoulderRoll`, `LElbowYaw`, `LElbowRoll`, `LWristYaw`, `LHipYawPitch`, `LHipRoll`, `LHipPitch`, `LKneePitch`, `LAnklePitch`, `LAnkleRoll`, `RShoulderPitch`, `RShoulderRoll`, `RElbowYaw`, `RElbowRoll`, `RWristYaw`, `RHipYawPitch`, `RHipRoll`, `RHipPitch`, `RKneePitch`, `RAnklePitch`, `RAnkleRoll`, `LHand`, `RHand`
- Cameras: `CameraTop`, `CameraBottom`
- Sonars: `Sonar/Left`, `Sonar/Right`
- IMU: `accelerometer`, `gyro`, `inertial unit`
- Foot force sensors: `LFsrFL`, `LFsrFR`, `LFsrRL`, `LFsrRR`, `RFsrFL`, `RFsrFR`, `RFsrRL`, `RFsrRR`
- Foot bumpers: `LFoot/Bumper/Left`, `LFoot/Bumper/Right`, `RFoot/Bumper/Left`, `RFoot/Bumper/Right`
- Hands phalanx motors: `LPhalanx1` through `LPhalanx8`, `RPhalanx1` through `RPhalanx8`
- Ear LEDs: `Ears/Led/Left`, `Ears/Led/Right`
- Eye LEDs: `Face/Led/Left`, `Face/Led/Right`
- Chest/foot LEDs: `ChestBoard/Led`, `LFoot/Led`, `RFoot/Led`
- Audio devices (if enabled): `Microphone`, `Speaker`

## 9) Crazyflie (`Crazyflie`)

- Propeller motors: `m1_motor`, `m2_motor`, `m3_motor`, `m4_motor`
- IMU: `accelerometer`, `gyro`, `inertial unit`
- Altitude/range: `range_front`, `range_back`, `range_left`, `range_right`, `range_down`
- Optional deck camera (world dependent): `camera`

## 10) DJI Mavic 2 Pro (`Mavic2Pro`)

- Propeller motors: `front left propeller`, `front right propeller`, `rear left propeller`, `rear right propeller`
- IMU/GNSS: `accelerometer`, `gyro`, `inertial unit`, `gps`, `compass`
- Camera: `camera`
- Gimbal motors (variant dependent): `camera roll`, `camera pitch`, `camera yaw`

## E-puck Complete Device Map

- Drive:
  - `left wheel motor`
  - `right wheel motor`
- Proximity (`DistanceSensor`):
  - `ps0`, `ps1`, `ps2`, `ps3`, `ps4`, `ps5`, `ps6`, `ps7`
- LEDs:
  - `led0`, `led1`, `led2`, `led3`, `led4`, `led5`, `led6`, `led7`, `led8`, `led9`
- Vision:
  - `camera`
- Inertial:
  - `accelerometer`
- Optional communication:
  - `receiver`
  - `emitter`

## NAO Complete Device Map

- Joint motors:
  - `HeadYaw`, `HeadPitch`
  - `LShoulderPitch`, `LShoulderRoll`, `LElbowYaw`, `LElbowRoll`, `LWristYaw`, `LHand`
  - `RShoulderPitch`, `RShoulderRoll`, `RElbowYaw`, `RElbowRoll`, `RWristYaw`, `RHand`
  - `LHipYawPitch`, `LHipRoll`, `LHipPitch`, `LKneePitch`, `LAnklePitch`, `LAnkleRoll`
  - `RHipYawPitch`, `RHipRoll`, `RHipPitch`, `RKneePitch`, `RAnklePitch`, `RAnkleRoll`
- Hand phalanx motors:
  - `LPhalanx1`, `LPhalanx2`, `LPhalanx3`, `LPhalanx4`, `LPhalanx5`, `LPhalanx6`, `LPhalanx7`, `LPhalanx8`
  - `RPhalanx1`, `RPhalanx2`, `RPhalanx3`, `RPhalanx4`, `RPhalanx5`, `RPhalanx6`, `RPhalanx7`, `RPhalanx8`
- Cameras:
  - `CameraTop`
  - `CameraBottom`
- Sonars:
  - `Sonar/Left`
  - `Sonar/Right`
- Inertial sensors:
  - `accelerometer`
  - `gyro`
  - `inertial unit`
- Foot force sensors:
  - `LFsrFL`, `LFsrFR`, `LFsrRL`, `LFsrRR`
  - `RFsrFL`, `RFsrFR`, `RFsrRL`, `RFsrRR`
- Foot bumpers:
  - `LFoot/Bumper/Left`, `LFoot/Bumper/Right`
  - `RFoot/Bumper/Left`, `RFoot/Bumper/Right`
- LEDs:
  - `Ears/Led/Left`, `Ears/Led/Right`
  - `Face/Led/Left`, `Face/Led/Right`
  - `ChestBoard/Led`, `LFoot/Led`, `RFoot/Led`

## UR5e Joint Names and Limits

Reference convention: joint name, lower limit, upper limit (radians and degrees).

| Joint | Lower (rad) | Upper (rad) | Lower (deg) | Upper (deg) |
|-------|-------------|-------------|-------------|-------------|
| shoulder_pan_joint | -6.283 | 6.283 | -360 | 360 |
| shoulder_lift_joint | -6.283 | 6.283 | -360 | 360 |
| elbow_joint | -3.142 | 3.142 | -180 | 180 |
| wrist_1_joint | -6.283 | 6.283 | -360 | 360 |
| wrist_2_joint | -6.283 | 6.283 | -360 | 360 |
| wrist_3_joint | -6.283 | 6.283 | -360 | 360 |

## TurtleBot3 Device Names

## TurtleBot3 Burger

- `left wheel motor`
- `right wheel motor`
- `left wheel sensor`
- `right wheel sensor`
- `LDS-01`
- `accelerometer`
- `gyro`
- `inertial unit`
- `camera` (if included by world/variant)

## TurtleBot3 Waffle Pi

- `left wheel motor`
- `right wheel motor`
- `left wheel sensor`
- `right wheel sensor`
- `LDS-01`
- `camera`
- `camera depth`
- `accelerometer`
- `gyro`
- `inertial unit`

## Pioneer 3-DX Device Names

- Drive:
  - `left wheel`
  - `right wheel`
  - `left wheel sensor`
  - `right wheel sensor`
- Sonar ring:
  - `so0`, `so1`, `so2`, `so3`, `so4`, `so5`, `so6`, `so7`
  - `so8`, `so9`, `so10`, `so11`, `so12`, `so13`, `so14`, `so15`
- LiDARs:
  - `sick lms 291`
  - `sick lms 291 rear`
- Optional cameras:
  - `camera`
  - `camera2`

## Notes

- Device naming can vary with PROTO parameters and world variants.
- When a runtime mismatch occurs, inspect active devices from controller startup and adapt lookup names to the loaded instance.
