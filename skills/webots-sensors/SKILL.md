---
name: webots-sensors
description: "Use this skill when working with any Webots sensor - Camera, Lidar, DistanceSensor, GPS, Gyro, Accelerometer, InertialUnit, Compass, TouchSensor, RangeFinder, Radar, LightSensor, PositionSensor, Receiver, or Altimeter. Covers sensor configuration, enable/disable, reading values, and processing sensor data. Triggers on: webots sensor, camera, lidar, distance sensor, GPS, gyro, accelerometer, IMU, touch sensor, range finder, radar."
---

# Webots Sensors Skill

Use this skill to implement, configure, and debug sensor pipelines in Webots controllers.
Focus on device acquisition, sampling-period control, data extraction, and sensor-specific post-processing.
Exclude actuator control and basic controller bootstrap logic.

## When to use

Activate this skill when tasks involve any of the following:

- Camera
- Lidar
- DistanceSensor
- GPS
- Gyro
- Accelerometer
- InertialUnit
- Compass
- TouchSensor
- RangeFinder
- Radar
- LightSensor
- PositionSensor
- Receiver
- Altimeter

## Core execution pattern

Apply this sequence for all Webots sensors:

1. Acquire device by DEF name.
2. Enable sensor with a sampling period (typically controller timestep).
3. Advance simulation with `step`.
4. Read sensor value(s) with the appropriate getter.
5. Process, filter, or fuse data.

Python universal pattern:

```python
sensor = robot.getDevice("sensor_name")
sensor.enable(timestep)  # MUST enable before reading
while robot.step(timestep) != -1:
    value = sensor.getValue()  # or getValues() for vector sensors
```

C universal pattern:

```c
WbDeviceTag sensor = wb_robot_get_device("sensor_name");
wb_distance_sensor_enable(sensor, time_step); /* use sensor-specific enable */
while (wb_robot_step(time_step) != -1) {
  double value = wb_distance_sensor_get_value(sensor); /* or vector getter */
}
```

## Sampling and performance rules

- Match sensor sampling period to controller timestep unless slower sampling is acceptable.
- Increase sampling period for expensive devices (`Camera`, `Lidar`, `RangeFinder`) to reduce CPU load.
- Pull only required outputs (for example, avoid point cloud retrieval when only scalar ranges are needed).
- Keep image/point-cloud conversions out of tight loops when possible.

## Sensor playbooks (Python + C)

### Camera

Use for RGB/RGBA image acquisition and optional object recognition.

- Key operations: `enable`, `getImage`, `getWidth`, `getHeight`, `imageGetRed/Green/Blue/Gray` (language-dependent helpers).
- Recognition: enable with `recognitionEnable`, then call `getRecognitionObjects`.
- Effects and optics from node fields: noise, motion blur, lens distortion, depth of field, fish-eye, spherical projection.
- C raw image API (`wb_camera_get_image`) returns byte buffer; convert to array/matrix for processing.

Python example:

```python
camera = robot.getDevice("front_camera")
camera.enable(timestep)
camera.recognitionEnable(timestep)

width = camera.getWidth()
height = camera.getHeight()

while robot.step(timestep) != -1:
    image = camera.getImage()  # bytes in BGRA in Webots Python API
    # Simple obstacle cue: average center brightness drop
    center_x = width // 2
    center_y = height // 2
    gray = camera.imageGetGray(image, width, center_x, center_y)

    objects = camera.getRecognitionObjects()
    if len(objects) > 0 or gray < 40:
        obstacle_detected = True
    else:
        obstacle_detected = False
```

C example:

```c
WbDeviceTag cam = wb_robot_get_device("front_camera");
wb_camera_enable(cam, time_step);
wb_camera_recognition_enable(cam, time_step);

int width = wb_camera_get_width(cam);
int height = wb_camera_get_height(cam);

while (wb_robot_step(time_step) != -1) {
  const unsigned char *img = wb_camera_get_image(cam);
  int cx = width / 2;
  int cy = height / 2;
  int gray = wb_camera_image_get_gray(img, width, cx, cy);

  int count = wb_camera_recognition_get_number_of_objects(cam);
  if (count > 0 || gray < 40) {
    /* obstacle_detected = true */
  }
}
```

### Lidar

Use for depth scans and optional 3D point cloud.

- `getRangeImage()` provides distances.
- `getPointCloud()` provides `LidarPoint` entries with `x, y, z, layer_id, time`.
- Important fields: `horizontalResolution`, `numberOfLayers`, `fieldOfView`, `minRange`, `maxRange`.

Python example (360-degree scan processing):

```python
lidar = robot.getDevice("lidar")
lidar.enable(timestep)
lidar.enablePointCloud()

while robot.step(timestep) != -1:
    ranges = lidar.getRangeImage()
    min_distance = min(ranges)
    points = lidar.getPointCloud()  # list of LidarPoint
    near_points = [p for p in points if p.x * p.x + p.y * p.y < 0.25]
```

C example:

```c
WbDeviceTag lidar = wb_robot_get_device("lidar");
wb_lidar_enable(lidar, time_step);
wb_lidar_enable_point_cloud(lidar);

while (wb_robot_step(time_step) != -1) {
  const float *ranges = wb_lidar_get_range_image(lidar);
  int n = wb_lidar_get_horizontal_resolution(lidar) * wb_lidar_get_number_of_layers(lidar);
  float min_d = ranges[0];
  for (int i = 1; i < n; ++i)
    if (ranges[i] < min_d) min_d = ranges[i];

  const WbLidarPoint *cloud = wb_lidar_get_point_cloud(lidar);
  (void)cloud;
}
```

### DistanceSensor

Use for short-range proximity with infra-red, sonar, or laser models.

- `getValue()` returns raw sensor reading.
- Convert raw reading to distance via node `lookupTable`.
- Sensor `type` field controls infra-red/sonar/laser behavior.

Python example (IR wall following):

```python
left_ir = robot.getDevice("left_ir")
right_ir = robot.getDevice("right_ir")
left_ir.enable(timestep)
right_ir.enable(timestep)

while robot.step(timestep) != -1:
    l = left_ir.getValue()
    r = right_ir.getValue()
    error = l - r
    wall_on_left = l > 80.0
```

C example:

```c
WbDeviceTag left_ir = wb_robot_get_device("left_ir");
WbDeviceTag right_ir = wb_robot_get_device("right_ir");
wb_distance_sensor_enable(left_ir, time_step);
wb_distance_sensor_enable(right_ir, time_step);

while (wb_robot_step(time_step) != -1) {
  double l = wb_distance_sensor_get_value(left_ir);
  double r = wb_distance_sensor_get_value(right_ir);
  double error = l - r;
  (void)error;
}
```

### GPS

- `getValues()` returns `[x, y, z]`.
- `getSpeed()` returns scalar speed.
- `getSpeedVector()` returns `[vx, vy, vz]`.
- `coordinateSystem`: `"local"` or `"WGS84"` at node level.

Python:

```python
gps = robot.getDevice("gps")
gps.enable(timestep)

while robot.step(timestep) != -1:
    x, y, z = gps.getValues()
    speed = gps.getSpeed()
    vx, vy, vz = gps.getSpeedVector()
```

C:

```c
WbDeviceTag gps = wb_robot_get_device("gps");
wb_gps_enable(gps, time_step);

while (wb_robot_step(time_step) != -1) {
  const double *p = wb_gps_get_values(gps);
  double speed = wb_gps_get_speed(gps);
  const double *v = wb_gps_get_speed_vector(gps);
  (void)p; (void)speed; (void)v;
}
```

### Gyro

- `getValues()` returns angular velocity `[wx, wy, wz]` in rad/s.

Python:

```python
gyro = robot.getDevice("gyro")
gyro.enable(timestep)

while robot.step(timestep) != -1:
    wx, wy, wz = gyro.getValues()
```

C:

```c
WbDeviceTag gyro = wb_robot_get_device("gyro");
wb_gyro_enable(gyro, time_step);

while (wb_robot_step(time_step) != -1) {
  const double *w = wb_gyro_get_values(gyro);
  (void)w;
}
```

### Accelerometer

- `getValues()` returns `[ax, ay, az]` in m/s^2, including gravity.

Python:

```python
acc = robot.getDevice("accelerometer")
acc.enable(timestep)

while robot.step(timestep) != -1:
    ax, ay, az = acc.getValues()
```

C:

```c
WbDeviceTag acc = wb_robot_get_device("accelerometer");
wb_accelerometer_enable(acc, time_step);

while (wb_robot_step(time_step) != -1) {
  const double *a = wb_accelerometer_get_values(acc);
  (void)a;
}
```

### InertialUnit

- `getRollPitchYaw()` returns `[roll, pitch, yaw]` in radians.
- `getQuaternion()` returns `[w, x, y, z]`.

Python:

```python
imu = robot.getDevice("inertial unit")
imu.enable(timestep)

while robot.step(timestep) != -1:
    roll, pitch, yaw = imu.getRollPitchYaw()
    w, x, y, z = imu.getQuaternion()
```

C:

```c
WbDeviceTag imu = wb_robot_get_device("inertial unit");
wb_inertial_unit_enable(imu, time_step);

while (wb_robot_step(time_step) != -1) {
  const double *rpy = wb_inertial_unit_get_roll_pitch_yaw(imu);
  const double *q = wb_inertial_unit_get_quaternion(imu);
  (void)rpy; (void)q;
}
```

### Compass

- `getValues()` returns normalized 3D vector to magnetic north.

Python:

```python
compass = robot.getDevice("compass")
compass.enable(timestep)

while robot.step(timestep) != -1:
    nx, ny, nz = compass.getValues()
```

C:

```c
WbDeviceTag compass = wb_robot_get_device("compass");
wb_compass_enable(compass, time_step);

while (wb_robot_step(time_step) != -1) {
  const double *north = wb_compass_get_values(compass);
  (void)north;
}
```

### TouchSensor

- Types: `bumper` (boolean-like scalar), `force` (scalar), `force-3d` (vector).
- Use `getValue()` for scalar types, `getValues()` for `force-3d`.

Python:

```python
touch = robot.getDevice("touch")
touch.enable(timestep)

while robot.step(timestep) != -1:
    if touch.getType() == touch.FORCE3D:
        fx, fy, fz = touch.getValues()
    else:
        f = touch.getValue()
```

C:

```c
WbDeviceTag touch = wb_robot_get_device("touch");
wb_touch_sensor_enable(touch, time_step);

while (wb_robot_step(time_step) != -1) {
  WbTouchSensorType type = wb_touch_sensor_get_type(touch);
  if (type == WB_TOUCH_SENSOR_FORCE3D) {
    const double *f = wb_touch_sensor_get_values(touch);
    (void)f;
  } else {
    double f = wb_touch_sensor_get_value(touch);
    (void)f;
  }
}
```

### RangeFinder

- Depth-camera style sensor.
- `getRangeImage()` returns per-pixel distance values.

Python:

```python
rf = robot.getDevice("range_finder")
rf.enable(timestep)

width = rf.getWidth()
height = rf.getHeight()

while robot.step(timestep) != -1:
    depth = rf.getRangeImage()  # length = width * height
    center_depth = depth[(height // 2) * width + (width // 2)]
```

C:

```c
WbDeviceTag rf = wb_robot_get_device("range_finder");
wb_range_finder_enable(rf, time_step);

int width = wb_range_finder_get_width(rf);
int height = wb_range_finder_get_height(rf);

while (wb_robot_step(time_step) != -1) {
  const float *depth = wb_range_finder_get_range_image(rf);
  float center = depth[(height / 2) * width + (width / 2)];
  (void)center;
}
```

### Radar

- `getTargets()` returns detections.
- Target fields: `distance`, `receivedPower`, `speed`, `azimuth`.

Python:

```python
radar = robot.getDevice("radar")
radar.enable(timestep)

while robot.step(timestep) != -1:
    targets = radar.getTargets()
    for t in targets:
        d = t.distance
        p = t.receivedPower
        s = t.speed
        a = t.azimuth
```

C:

```c
WbDeviceTag radar = wb_robot_get_device("radar");
wb_radar_enable(radar, time_step);

while (wb_robot_step(time_step) != -1) {
  int n = wb_radar_get_number_of_targets(radar);
  const WbRadarTarget *targets = wb_radar_get_targets(radar);
  for (int i = 0; i < n; ++i) {
    double d = targets[i].distance;
    double p = targets[i].received_power;
    double s = targets[i].speed;
    double a = targets[i].azimuth;
    (void)d; (void)p; (void)s; (void)a;
  }
}
```

### LightSensor

- `getValue()` returns irradiance/light intensity.

Python:

```python
light = robot.getDevice("light")
light.enable(timestep)

while robot.step(timestep) != -1:
    irradiance = light.getValue()
```

C:

```c
WbDeviceTag light = wb_robot_get_device("light");
wb_light_sensor_enable(light, time_step);

while (wb_robot_step(time_step) != -1) {
  double irradiance = wb_light_sensor_get_value(light);
  (void)irradiance;
}
```

### PositionSensor

- Joint feedback sensor.
- Returns radians (rotational) or meters (linear).
- Attached to a `Joint` node.

Python:

```python
pos = robot.getDevice("joint_sensor")
pos.enable(timestep)

while robot.step(timestep) != -1:
    q = pos.getValue()
```

C:

```c
WbDeviceTag pos = wb_robot_get_device("joint_sensor");
wb_position_sensor_enable(pos, time_step);

while (wb_robot_step(time_step) != -1) {
  double q = wb_position_sensor_get_value(pos);
  (void)q;
}
```

### Receiver

- Communication sensor paired with `Emitter`.
- Read packet queue with `getQueueLength`, `getData`, `nextPacket`.

Python:

```python
rx = robot.getDevice("receiver")
rx.enable(timestep)

while robot.step(timestep) != -1:
    while rx.getQueueLength() > 0:
        data = rx.getData()
        # decode bytes as needed
        rx.nextPacket()
```

C:

```c
WbDeviceTag rx = wb_robot_get_device("receiver");
wb_receiver_enable(rx, time_step);

while (wb_robot_step(time_step) != -1) {
  while (wb_receiver_get_queue_length(rx) > 0) {
    const void *data = wb_receiver_get_data(rx);
    int size = wb_receiver_get_data_size(rx);
    (void)data; (void)size;
    wb_receiver_next_packet(rx);
  }
}
```

### Altimeter

- `getAltitude()` returns altitude above ground.

Python:

```python
alt = robot.getDevice("altimeter")
alt.enable(timestep)

while robot.step(timestep) != -1:
    altitude = alt.getAltitude()
```

C:

```c
WbDeviceTag alt = wb_robot_get_device("altimeter");
wb_altimeter_enable(alt, time_step);

while (wb_robot_step(time_step) != -1) {
  double altitude = wb_altimeter_get_value(alt);
  (void)altitude;
}
```

## Cross-sensor integration patterns

- Fuse IMU (`Accelerometer`, `Gyro`, `InertialUnit`) for stable attitude estimation.
- Use `Camera` recognition + `Lidar` depth for robust obstacle classification.
- Use `GPS` + `Compass` for global heading and waypoint tracking.
- Gate expensive sensor reads by task phase (navigation vs manipulation vs idle).

## Debug checklist

- Verify exact device name matches node `name` field.
- Verify `enable(...)` call executes before first getter call.
- Verify getter aligns with sensor type (`getValue` vs `getValues`).
- Validate sampling period and expected update rate.
- Confirm node fields (`lookupTable`, ranges, FOV, coordinate system) match design assumptions.

## Reference file

Use `references/sensors_reference.md` for complete multi-language API signatures, node fields, data formats, and recognition object schema.
