---
name: webots-robots-catalog
description: "Use this skill to find and use pre-built robot models in Webots - industrial arms (UR, KUKA, ABB, Franka), mobile robots (e-puck, TurtleBot, Pioneer), humanoids (NAO, Darwin-OP, Atlas), drones (Mavic, Crazyflie), and vehicles (Tesla, BMW). Provides PROTO names, device lists, and usage examples. Triggers on: webots robot, e-puck, NAO, UR5, TurtleBot, KUKA, Pioneer, drone, humanoid, industrial robot, robot model."
---

# Webots Robots Catalog

Use this skill to quickly select pre-built robot and vehicle models from the Webots PROTO library.

## Scope

- Focus on cataloging available robot models, PROTO names, and practical starting points.
- Use `references/robots_reference.md` for detailed device-name maps and high-frequency robot details.
- Exclude custom robot authoring workflows.
- Exclude low-level sensor and actuator API tutorials.

## How to Add a Robot

`Add button > PROTO nodes (Webots Projects) > robots > [manufacturer] > [model]`

## Mobile Robots (Research/Education)

| Robot | Manufacturer | PROTO Name | Type | Key Devices |
|-------|-------------|------------|------|-------------|
| e-puck | GCtronic | E-puck | Differential | 8 DistanceSensors, Camera, 10 LEDs, Accelerometer |
| e-puck2 | GCtronic | E-puck2 | Differential | Same + ToF sensor, microphones |
| Thymio II | Mobsya | Thymio2 | Differential | 7 DistanceSensors, ground sensors, accelerometer |
| Khepera IV | K-Team | Khepera4 | Differential | 12 IR sensors, ultrasound, camera, gyro |
| Khepera III | K-Team | Khepera3 | Differential | 11 IR sensors, ultrasound |
| Khepera I | K-Team | Khepera1 | Differential | 8 IR sensors |
| Create (Roomba) | iRobot | Create | Differential | Bumper, cliff sensors, IR |
| JetBot | NVIDIA | JetBot | Differential | Camera |

## Mobile Robots (Professional/Research)

| Robot | PROTO Name | Type | Key Devices |
|-------|------------|------|-------------|
| Pioneer 3-DX | Pioneer3dx | Differential | 16 sonars, 2 LiDARs |
| Pioneer 3-AT | Pioneer3at | Skid-steer | 16 sonars |
| TurtleBot3 Burger | TurtleBot3Burger | Differential | LiDAR, IMU, Camera |
| TurtleBot3 Waffle | TurtleBot3WafflePI | Differential | LiDAR, Camera, IMU |
| Robotino 3 | Robotino3 | Omnidirectional | 9 IR, camera, bumper |
| Summit-XL | SummitXlSteel | Skid-steer | GPS, IMU |
| Rosbot | Rosbot | Differential | LiDAR, camera, IMU |
| MiR100 | MiR100 | Differential | 2 LiDARs, IMU |

## Industrial Arms

| Robot | PROTO Name | DOF | Key Features |
|-------|------------|-----|--------------|
| UR3e | UR3e | 6 | Collaborative, 3kg payload |
| UR5e | UR5e | 6 | Collaborative, 5kg payload |
| UR10e | UR10e | 6 | Collaborative, 10kg payload |
| KUKA YouBot | YouBot | 5+mobile | Mobile manipulator |
| Franka Emika Panda | PandaArm | 7 | 7-DOF collaborative |
| ABB IRB 4600 | Irb4600-40 | 6 | Industrial, 40kg payload |
| Niryo Ned | NiryoNed | 6 | Educational |
| EPSON T6 | EpsonT6 | 4 (SCARA) | High speed |

## Humanoids

| Robot | PROTO Name | DOF | Key Features |
|-------|------------|-----|--------------|
| NAO | Nao | 25 | Speech, cameras, force sensors |
| Darwin-OP | DarwinOp2 | 20 | Open platform, walking |
| Robotis OP2 | RobotisOp2 | 20 | Competition robot |
| Robotis OP3 | RobotisOp3 | 20 | Upgraded OP2 |
| Atlas | Atlas | 28 | Boston Dynamics humanoid |
| iCub | ICub | 53 | Research humanoid |

## Drones

| Robot | PROTO Name | Type | Key Features |
|-------|------------|------|--------------|
| DJI Mavic 2 Pro | Mavic2Pro | Quadrotor | Camera, GPS |
| Crazyflie | Crazyflie | Quadrotor | Lightweight, swarm capable |

## Vehicles

| Vehicle | PROTO Name | Key Features |
|---------|------------|--------------|
| BMW X5 | BmwX5 | SUV with sensors |
| Tesla Model 3 | TeslaModel3 | Electric car |
| Toyota Prius | ToyotaPrius | Hybrid |
| Lincoln MKZ | LincolnMkz | Autonomous driving platform |
| Citroen C-Zero | CitroenCZero | Electric city car |

## Other Notable Robots

- Aibo ERS7 (Sony dog robot)
- Spot (Boston Dynamics quadruped)
- Salamander (amphibious)
- Blimp (lighter-than-air)
- Sojourner (Mars rover)
- PR2 (Willow Garage)
- Shrimp (climbing rover)

## Example: Using an E-puck

```python
# The E-puck has these device names:
# Motors: "left wheel motor", "right wheel motor"
# Distance sensors: "ps0" through "ps7"
# LEDs: "led0" through "led9"
# Camera: "camera"
# Accelerometer: "accelerometer"

from controller import Robot
robot = Robot()
timestep = int(robot.getBasicTimeStep())

# Get motors
left = robot.getDevice("left wheel motor")
right = robot.getDevice("right wheel motor")
left.setPosition(float('inf'))
right.setPosition(float('inf'))

# Get distance sensors
sensors = []
for i in range(8):
    s = robot.getDevice(f"ps{i}")
    s.enable(timestep)
    sensors.append(s)

while robot.step(timestep) != -1:
    values = [s.getValue() for s in sensors]
    # Simple obstacle avoidance
    if values[0] > 80 or values[7] > 80:
        left.setVelocity(-2.0)
        right.setVelocity(2.0)
    else:
        left.setVelocity(6.28)
        right.setVelocity(6.28)
```

## Usage Pattern

- Identify robot class first: mobile, manipulator, humanoid, drone, or vehicle.
- Select PROTO by model requirements (DOF, locomotion, payload, sensor suite).
- Add robot via PROTO tree and confirm device names from `references/robots_reference.md`.
- Implement controller logic using exact device identifiers.
