# Webots Sensors Reference (All Supported Sensor APIs)

This reference covers sensor-only APIs and node fields for:

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

Actuators and basic controller setup are intentionally excluded.

## 1) Universal sensor lifecycle

All sensors follow: enable -> step -> read.

### Python

```python
sensor = robot.getDevice("sensor_name")
sensor.enable(timestep)
while robot.step(timestep) != -1:
    value = sensor.getValue()  # or getValues()/sensor-specific getter
```

### C

```c
WbDeviceTag tag = wb_robot_get_device("sensor_name");
/* use sensor-specific enable function */
while (wb_robot_step(time_step) != -1) {
  /* use sensor-specific getter */
}
```

## 2) Language naming map

- C: `wb_<sensor>_<method>(...)`
- C++: `<SensorClass>::<method>(...)`
- Python: `<sensor>.<method>(...)`
- Java: `<SensorClass>.<method>(...)`
- MATLAB: `<wb_sensor_method>(...)` with C-style naming convention

Unless otherwise noted, methods exposed in C are available in C++, Python, Java, and MATLAB bindings with idiomatic naming.

## 3) Sensor API by node type

## Camera

### Primary methods

- Enable/disable/sampling: `enable`, `disable`, `getSamplingPeriod`
- Image access: `getImage`, `getImageArray`
- Image geometry: `getWidth`, `getHeight`, `getFov`, `setFov`, `getMinFov`, `getMaxFov`
- Optics: `getExposure`, `setExposure`, `getFocalLength`
- Pixel helpers: `imageGetRed`, `imageGetGreen`, `imageGetBlue`, `imageGetGray`, `imageGetAlpha`
- Save image: `saveImage` (where supported)
- Recognition pipeline: `recognitionEnable`, `recognitionDisable`, `isRecognitionSegmentationEnabled`, `enableRecognitionSegmentation`, `disableRecognitionSegmentation`, `getRecognitionObjects`, `getRecognitionNumberOfObjects`, `getRecognitionSamplingPeriod`

### C API signatures

```c
void wb_camera_enable(WbDeviceTag tag, int sampling_period);
void wb_camera_disable(WbDeviceTag tag);
int wb_camera_get_sampling_period(WbDeviceTag tag);

int wb_camera_get_width(WbDeviceTag tag);
int wb_camera_get_height(WbDeviceTag tag);
double wb_camera_get_fov(WbDeviceTag tag);
void wb_camera_set_fov(WbDeviceTag tag, double fov);
double wb_camera_get_min_fov(WbDeviceTag tag);
double wb_camera_get_max_fov(WbDeviceTag tag);
double wb_camera_get_exposure(WbDeviceTag tag);
void wb_camera_set_exposure(WbDeviceTag tag, double exposure);
double wb_camera_get_focal_length(WbDeviceTag tag);

const unsigned char *wb_camera_get_image(WbDeviceTag tag);
int wb_camera_image_get_red(const unsigned char *image, int width, int x, int y);
int wb_camera_image_get_green(const unsigned char *image, int width, int x, int y);
int wb_camera_image_get_blue(const unsigned char *image, int width, int x, int y);
int wb_camera_image_get_gray(const unsigned char *image, int width, int x, int y);
int wb_camera_image_get_alpha(const unsigned char *image, int width, int x, int y);
int wb_camera_save_image(WbDeviceTag tag, const char *filename, int quality);

void wb_camera_recognition_enable(WbDeviceTag tag, int sampling_period);
void wb_camera_recognition_disable(WbDeviceTag tag);
int wb_camera_recognition_get_sampling_period(WbDeviceTag tag);
int wb_camera_recognition_get_number_of_objects(WbDeviceTag tag);
const WbCameraRecognitionObject *wb_camera_recognition_get_objects(WbDeviceTag tag);
void wb_camera_recognition_enable_segmentation(WbDeviceTag tag);
void wb_camera_recognition_disable_segmentation(WbDeviceTag tag);
bool wb_camera_recognition_is_segmentation_enabled(WbDeviceTag tag);
const unsigned char *wb_camera_recognition_get_segmentation_image(WbDeviceTag tag);
```

### C++ API

```cpp
Camera::enable(int samplingPeriod)
Camera::disable()
Camera::getSamplingPeriod() const
Camera::getWidth() const
Camera::getHeight() const
Camera::getFov() const
Camera::setFov(double)
Camera::getMinFov() const
Camera::getMaxFov() const
Camera::getExposure() const
Camera::setExposure(double)
Camera::getFocalLength() const
Camera::getImage() const
Camera::getImageArray() const
Camera::imageGetRed(...)
Camera::imageGetGreen(...)
Camera::imageGetBlue(...)
Camera::imageGetGray(...)
Camera::imageGetAlpha(...)
Camera::saveImage(const std::string &, int)
Camera::recognitionEnable(int)
Camera::recognitionDisable()
Camera::recognitionGetSamplingPeriod() const
Camera::getRecognitionNumberOfObjects() const
Camera::getRecognitionObjects() const
Camera::enableRecognitionSegmentation()
Camera::disableRecognitionSegmentation()
Camera::isRecognitionSegmentationEnabled() const
Camera::getRecognitionSegmentationImage() const
```

### Python API

```python
Camera.enable(sampling_period)
Camera.disable()
Camera.getSamplingPeriod()
Camera.getWidth()
Camera.getHeight()
Camera.getFov()
Camera.setFov(fov)
Camera.getMinFov()
Camera.getMaxFov()
Camera.getExposure()
Camera.setExposure(exposure)
Camera.getFocalLength()
Camera.getImage()
Camera.getImageArray()
Camera.imageGetRed(image, width, x, y)
Camera.imageGetGreen(image, width, x, y)
Camera.imageGetBlue(image, width, x, y)
Camera.imageGetGray(image, width, x, y)
Camera.imageGetAlpha(image, width, x, y)
Camera.saveImage(filename, quality)
Camera.recognitionEnable(sampling_period)
Camera.recognitionDisable()
Camera.getRecognitionSamplingPeriod()
Camera.getRecognitionNumberOfObjects()
Camera.getRecognitionObjects()
Camera.enableRecognitionSegmentation()
Camera.disableRecognitionSegmentation()
Camera.isRecognitionSegmentationEnabled()
Camera.getRecognitionSegmentationImage()
```

### Java API

```java
Camera.enable(int)
Camera.disable()
Camera.getSamplingPeriod()
Camera.getWidth()
Camera.getHeight()
Camera.getFov()
Camera.setFov(double)
Camera.getMinFov()
Camera.getMaxFov()
Camera.getExposure()
Camera.setExposure(double)
Camera.getFocalLength()
Camera.getImage()
Camera.getImageArray()
Camera.imageGetRed(...)
Camera.imageGetGreen(...)
Camera.imageGetBlue(...)
Camera.imageGetGray(...)
Camera.imageGetAlpha(...)
Camera.saveImage(String, int)
Camera.recognitionEnable(int)
Camera.recognitionDisable()
Camera.getRecognitionSamplingPeriod()
Camera.getRecognitionNumberOfObjects()
Camera.getRecognitionObjects()
Camera.enableRecognitionSegmentation()
Camera.disableRecognitionSegmentation()
Camera.isRecognitionSegmentationEnabled()
Camera.getRecognitionSegmentationImage()
```

### MATLAB API (C-style wrappers)

```matlab
wb_camera_enable(tag, sampling_period)
wb_camera_disable(tag)
wb_camera_get_sampling_period(tag)
wb_camera_get_width(tag)
wb_camera_get_height(tag)
wb_camera_get_fov(tag)
wb_camera_set_fov(tag, fov)
wb_camera_get_min_fov(tag)
wb_camera_get_max_fov(tag)
wb_camera_get_exposure(tag)
wb_camera_set_exposure(tag, exposure)
wb_camera_get_focal_length(tag)
wb_camera_get_image(tag)
wb_camera_image_get_red(image, width, x, y)
wb_camera_image_get_green(image, width, x, y)
wb_camera_image_get_blue(image, width, x, y)
wb_camera_image_get_gray(image, width, x, y)
wb_camera_image_get_alpha(image, width, x, y)
wb_camera_save_image(tag, filename, quality)
wb_camera_recognition_enable(tag, sampling_period)
wb_camera_recognition_disable(tag)
wb_camera_recognition_get_sampling_period(tag)
wb_camera_recognition_get_number_of_objects(tag)
wb_camera_recognition_get_objects(tag)
wb_camera_recognition_enable_segmentation(tag)
wb_camera_recognition_disable_segmentation(tag)
wb_camera_recognition_is_segmentation_enabled(tag)
wb_camera_recognition_get_segmentation_image(tag)
```

### Camera image data format

- `wb_camera_get_image` returns raw byte buffer.
- Memory layout is row-major, packed 4 bytes per pixel (BGRA in most controller bindings).
- Pixel offset formula (C): `index = 4 * (x + y * width)`.
- Channel extraction:
  - Blue: `image[index + 0]`
  - Green: `image[index + 1]`
  - Red: `image[index + 2]`
  - Alpha: `image[index + 3]`
- Python conversion example:

```python
import numpy as np

img = camera.getImage()
arr = np.frombuffer(img, dtype=np.uint8).reshape((camera.getHeight(), camera.getWidth(), 4))
bgr = arr[:, :, :3]
```

### Recognition object schema (complete fields)

Each recognition object includes:

- `id` (int): stable object id for tracking
- `model` (string): recognized model label
- `position` (vec3): object position in camera coordinates
- `orientation` (quat): object orientation in camera coordinates
- `size` (vec2): object physical dimensions
- `position_on_image` (vec2 int): pixel center in image
- `size_on_image` (vec2 int): pixel bounding box size
- `number_of_colors` (int): dominant color count
- `colors` (float array): RGB triplets in `[0, 1]`

In C this is represented by `WbCameraRecognitionObject`.

## Lidar

### Primary methods

- `enable`, `disable`, `getSamplingPeriod`
- `getHorizontalResolution`, `getNumberOfLayers`
- `getFov`, `getVerticalFov`
- `getMinRange`, `getMaxRange`
- `getFrequency`, `setFrequency`, `getMinFrequency`, `getMaxFrequency`
- `getRangeImage`, `getLayerRangeImage`
- `enablePointCloud`, `disablePointCloud`, `isPointCloudEnabled`, `getPointCloud`

### C signatures

```c
void wb_lidar_enable(WbDeviceTag tag, int sampling_period);
void wb_lidar_disable(WbDeviceTag tag);
int wb_lidar_get_sampling_period(WbDeviceTag tag);

int wb_lidar_get_horizontal_resolution(WbDeviceTag tag);
int wb_lidar_get_number_of_layers(WbDeviceTag tag);
double wb_lidar_get_fov(WbDeviceTag tag);
double wb_lidar_get_vertical_fov(WbDeviceTag tag);
double wb_lidar_get_min_range(WbDeviceTag tag);
double wb_lidar_get_max_range(WbDeviceTag tag);
double wb_lidar_get_frequency(WbDeviceTag tag);
void wb_lidar_set_frequency(WbDeviceTag tag, double frequency);
double wb_lidar_get_min_frequency(WbDeviceTag tag);
double wb_lidar_get_max_frequency(WbDeviceTag tag);

const float *wb_lidar_get_range_image(WbDeviceTag tag);
const float *wb_lidar_get_layer_range_image(WbDeviceTag tag, int layer);
void wb_lidar_enable_point_cloud(WbDeviceTag tag);
void wb_lidar_disable_point_cloud(WbDeviceTag tag);
bool wb_lidar_is_point_cloud_enabled(WbDeviceTag tag);
const WbLidarPoint *wb_lidar_get_point_cloud(WbDeviceTag tag);
```

### C++ / Python / Java / MATLAB methods

Use the same method set as above with idiomatic names:

- C++: `Lidar::getRangeImage()`, `Lidar::getPointCloud()`, etc.
- Python: `Lidar.getRangeImage()`, `Lidar.getPointCloud()`, etc.
- Java: `Lidar.getRangeImage()`, `Lidar.getPointCloud()`, etc.
- MATLAB: `wb_lidar_get_range_image`, `wb_lidar_get_point_cloud`, etc.

### Lidar point cloud data structure

`WbLidarPoint` fields:

- `x` (float)
- `y` (float)
- `z` (float)
- `layer_id` (int)
- `time` (float)

Range image indexing is row-major by layer:

- linear index: `i = layer * horizontalResolution + beam`

## DistanceSensor

### Methods

- `enable`, `disable`, `getSamplingPeriod`
- `getValue`
- `getType` (infra-red / sonar / laser)
- `getMinValue`, `getMaxValue`
- `getAperture`
- `getLookupTable`, `getLookupTableSize`

### C

```c
void wb_distance_sensor_enable(WbDeviceTag tag, int sampling_period);
void wb_distance_sensor_disable(WbDeviceTag tag);
int wb_distance_sensor_get_sampling_period(WbDeviceTag tag);
double wb_distance_sensor_get_value(WbDeviceTag tag);
WbDistanceSensorType wb_distance_sensor_get_type(WbDeviceTag tag);
double wb_distance_sensor_get_min_value(WbDeviceTag tag);
double wb_distance_sensor_get_max_value(WbDeviceTag tag);
double wb_distance_sensor_get_aperture(WbDeviceTag tag);
const double *wb_distance_sensor_get_lookup_table(WbDeviceTag tag);
int wb_distance_sensor_get_lookup_table_size(WbDeviceTag tag);
```

### C++ / Python / Java / MATLAB

- C++: `DistanceSensor::getValue()`, `getType()`, `getLookupTable()`, ...
- Python: `DistanceSensor.getValue()`, `getType()`, `getLookupTable()`, ...
- Java: `DistanceSensor.getValue()`, `getType()`, `getLookupTable()`, ...
- MATLAB: `wb_distance_sensor_get_value`, `wb_distance_sensor_get_type`, ...

### Lookup table format (DistanceSensor)

`lookupTable` is a flat array of triplets:

`[x0, y0, sigma0, x1, y1, sigma1, ...]`

- `x`: physical distance sample (meters)
- `y`: ideal sensor response at that distance
- `sigma`: gaussian noise standard deviation at that sample

Interpolation is piecewise linear between samples.

## GPS

### Methods

- `enable`, `disable`, `getSamplingPeriod`
- `getValues` -> `[x, y, z]`
- `getSpeed` -> scalar
- `getSpeedVector` -> `[vx, vy, vz]`
- `getCoordinateSystem`

### C

```c
void wb_gps_enable(WbDeviceTag tag, int sampling_period);
void wb_gps_disable(WbDeviceTag tag);
int wb_gps_get_sampling_period(WbDeviceTag tag);
const double *wb_gps_get_values(WbDeviceTag tag);
double wb_gps_get_speed(WbDeviceTag tag);
const double *wb_gps_get_speed_vector(WbDeviceTag tag);
WbGpsCoordinateSystem wb_gps_get_coordinate_system(WbDeviceTag tag);
```

### C++ / Python / Java / MATLAB

- C++: `GPS::getValues()`, `getSpeed()`, `getSpeedVector()`, `getCoordinateSystem()`
- Python: `GPS.getValues()`, `getSpeed()`, `getSpeedVector()`, `getCoordinateSystem()`
- Java: same set
- MATLAB: `wb_gps_get_values`, `wb_gps_get_speed`, `wb_gps_get_speed_vector`, `wb_gps_get_coordinate_system`

## Gyro

### Methods

- `enable`, `disable`, `getSamplingPeriod`
- `getValues` -> angular velocity vector rad/s
- `getLookupTable`, `getLookupTableSize`

### C

```c
void wb_gyro_enable(WbDeviceTag tag, int sampling_period);
void wb_gyro_disable(WbDeviceTag tag);
int wb_gyro_get_sampling_period(WbDeviceTag tag);
const double *wb_gyro_get_values(WbDeviceTag tag);
const double *wb_gyro_get_lookup_table(WbDeviceTag tag);
int wb_gyro_get_lookup_table_size(WbDeviceTag tag);
```

## Accelerometer

### Methods

- `enable`, `disable`, `getSamplingPeriod`
- `getValues` -> acceleration vector m/s^2 (includes gravity)
- `getLookupTable`, `getLookupTableSize`

### C

```c
void wb_accelerometer_enable(WbDeviceTag tag, int sampling_period);
void wb_accelerometer_disable(WbDeviceTag tag);
int wb_accelerometer_get_sampling_period(WbDeviceTag tag);
const double *wb_accelerometer_get_values(WbDeviceTag tag);
const double *wb_accelerometer_get_lookup_table(WbDeviceTag tag);
int wb_accelerometer_get_lookup_table_size(WbDeviceTag tag);
```

## InertialUnit

### Methods

- `enable`, `disable`, `getSamplingPeriod`
- `getRollPitchYaw`
- `getQuaternion`
- `getNoise`

### C

```c
void wb_inertial_unit_enable(WbDeviceTag tag, int sampling_period);
void wb_inertial_unit_disable(WbDeviceTag tag);
int wb_inertial_unit_get_sampling_period(WbDeviceTag tag);
const double *wb_inertial_unit_get_roll_pitch_yaw(WbDeviceTag tag);
const double *wb_inertial_unit_get_quaternion(WbDeviceTag tag);
double wb_inertial_unit_get_noise(WbDeviceTag tag);
```

## Compass

### Methods

- `enable`, `disable`, `getSamplingPeriod`
- `getValues`
- `getLookupTable`, `getLookupTableSize`

### C

```c
void wb_compass_enable(WbDeviceTag tag, int sampling_period);
void wb_compass_disable(WbDeviceTag tag);
int wb_compass_get_sampling_period(WbDeviceTag tag);
const double *wb_compass_get_values(WbDeviceTag tag);
const double *wb_compass_get_lookup_table(WbDeviceTag tag);
int wb_compass_get_lookup_table_size(WbDeviceTag tag);
```

## TouchSensor

### Methods

- `enable`, `disable`, `getSamplingPeriod`
- `getType`
- `getValue` (bumper/force)
- `getValues` (force-3d)
- `getLookupTable`, `getLookupTableSize`

### C

```c
void wb_touch_sensor_enable(WbDeviceTag tag, int sampling_period);
void wb_touch_sensor_disable(WbDeviceTag tag);
int wb_touch_sensor_get_sampling_period(WbDeviceTag tag);
WbTouchSensorType wb_touch_sensor_get_type(WbDeviceTag tag);
double wb_touch_sensor_get_value(WbDeviceTag tag);
const double *wb_touch_sensor_get_values(WbDeviceTag tag);
const double *wb_touch_sensor_get_lookup_table(WbDeviceTag tag);
int wb_touch_sensor_get_lookup_table_size(WbDeviceTag tag);
```

## RangeFinder

### Methods

- `enable`, `disable`, `getSamplingPeriod`
- `getWidth`, `getHeight`
- `getFov`
- `getMinRange`, `getMaxRange`
- `getRangeImage`, `getRangeImageArray`
- `saveImage`

### C

```c
void wb_range_finder_enable(WbDeviceTag tag, int sampling_period);
void wb_range_finder_disable(WbDeviceTag tag);
int wb_range_finder_get_sampling_period(WbDeviceTag tag);
int wb_range_finder_get_width(WbDeviceTag tag);
int wb_range_finder_get_height(WbDeviceTag tag);
double wb_range_finder_get_fov(WbDeviceTag tag);
double wb_range_finder_get_min_range(WbDeviceTag tag);
double wb_range_finder_get_max_range(WbDeviceTag tag);
const float *wb_range_finder_get_range_image(WbDeviceTag tag);
int wb_range_finder_save_image(WbDeviceTag tag, const char *filename, int quality);
```

## Radar

### Methods

- `enable`, `disable`, `getSamplingPeriod`
- `getNumberOfTargets`, `getTargets`
- `getMinRange`, `getMaxRange`
- `getHorizontalFov`, `getVerticalFov`

### C

```c
void wb_radar_enable(WbDeviceTag tag, int sampling_period);
void wb_radar_disable(WbDeviceTag tag);
int wb_radar_get_sampling_period(WbDeviceTag tag);
int wb_radar_get_number_of_targets(WbDeviceTag tag);
const WbRadarTarget *wb_radar_get_targets(WbDeviceTag tag);
double wb_radar_get_min_range(WbDeviceTag tag);
double wb_radar_get_max_range(WbDeviceTag tag);
double wb_radar_get_horizontal_fov(WbDeviceTag tag);
double wb_radar_get_vertical_fov(WbDeviceTag tag);
```

### Radar target structure

`WbRadarTarget` fields:

- `distance`
- `received_power`
- `speed`
- `azimuth`

## LightSensor

### Methods

- `enable`, `disable`, `getSamplingPeriod`
- `getValue`
- `getLookupTable`, `getLookupTableSize`

### C

```c
void wb_light_sensor_enable(WbDeviceTag tag, int sampling_period);
void wb_light_sensor_disable(WbDeviceTag tag);
int wb_light_sensor_get_sampling_period(WbDeviceTag tag);
double wb_light_sensor_get_value(WbDeviceTag tag);
const double *wb_light_sensor_get_lookup_table(WbDeviceTag tag);
int wb_light_sensor_get_lookup_table_size(WbDeviceTag tag);
```

## PositionSensor

### Methods

- `enable`, `disable`, `getSamplingPeriod`
- `getValue`
- `getType` (rotational/linear)

### C

```c
void wb_position_sensor_enable(WbDeviceTag tag, int sampling_period);
void wb_position_sensor_disable(WbDeviceTag tag);
int wb_position_sensor_get_sampling_period(WbDeviceTag tag);
double wb_position_sensor_get_value(WbDeviceTag tag);
WbJointType wb_position_sensor_get_type(WbDeviceTag tag);
```

## Receiver

### Methods

- `enable`, `disable`, `getSamplingPeriod`
- `getQueueLength`
- `getData`, `getDataSize`
- typed unpack helpers (language-specific): string/int/float/bool helpers
- `nextPacket`
- `getSignalStrength`
- `getEmitterDirection`
- `getChannel`, `setChannel`

### C

```c
void wb_receiver_enable(WbDeviceTag tag, int sampling_period);
void wb_receiver_disable(WbDeviceTag tag);
int wb_receiver_get_sampling_period(WbDeviceTag tag);
int wb_receiver_get_queue_length(WbDeviceTag tag);
const void *wb_receiver_get_data(WbDeviceTag tag);
int wb_receiver_get_data_size(WbDeviceTag tag);
void wb_receiver_next_packet(WbDeviceTag tag);
double wb_receiver_get_signal_strength(WbDeviceTag tag);
const double *wb_receiver_get_emitter_direction(WbDeviceTag tag);
int wb_receiver_get_channel(WbDeviceTag tag);
void wb_receiver_set_channel(WbDeviceTag tag, int channel);
```

### Python highlights

```python
Receiver.getQueueLength()
Receiver.getData()
Receiver.getDataSize()
Receiver.getString()
Receiver.getInts()
Receiver.getFloats()
Receiver.getBools()
Receiver.nextPacket()
Receiver.getSignalStrength()
Receiver.getEmitterDirection()
Receiver.getChannel()
Receiver.setChannel(channel)
```

## Altimeter

### Methods

- `enable`, `disable`, `getSamplingPeriod`
- altitude reading (`getValue` in low-level C API; high-level wrapper may expose `getAltitude`)

### C

```c
void wb_altimeter_enable(WbDeviceTag tag, int sampling_period);
void wb_altimeter_disable(WbDeviceTag tag);
int wb_altimeter_get_sampling_period(WbDeviceTag tag);
double wb_altimeter_get_value(WbDeviceTag tag);
```

### Python / C++ / Java / MATLAB

- Python: `Altimeter.getValue()` (or wrapper alias `getAltitude()`)
- C++: `Altimeter::getValue()`
- Java: `Altimeter.getValue()`
- MATLAB: `wb_altimeter_get_value(tag)`

## 4) `.wbt` field reference by sensor node

Field sets below include practical sensor-specific fields used in world files. Inherited `Device`/`Solid` fields (such as `name`, `translation`, `rotation`, `children`) may also apply depending on parent class.

## Camera fields

- `name` (SFString)
- `width` (SFInt32)
- `height` (SFInt32)
- `fieldOfView` (SFFloat)
- `near` (SFFloat)
- `far` (SFFloat)
- `exposure` (SFFloat)
- `antiAliasing` (SFBool)
- `motionBlur` (SFFloat)
- `noise` (SFFloat)
- `lens` / distortion-related fields (node-version dependent)
- `focus` and depth-of-field related fields
- `spherical` (SFBool)
- `fisheye` (SFBool)
- `recognition` (SFNode CameraRecognition)

## Lidar fields

- `name` (SFString)
- `horizontalResolution` (SFInt32)
- `numberOfLayers` (SFInt32)
- `fieldOfView` (SFFloat)
- `verticalFieldOfView` (SFFloat)
- `minRange` (SFFloat)
- `maxRange` (SFFloat)
- `frequency` (SFFloat)
- `noise` (SFFloat)
- `rotationFrequency` / version-specific frequency fields
- `defaultFrequency` (SFFloat, when present)

## DistanceSensor fields

- `name` (SFString)
- `type` (SFString: `"infra-red" | "sonar" | "laser"`)
- `aperture` (SFFloat)
- `lookupTable` (MFVec3f)
- `gaussianWidth` (SFFloat, when model supports beam profile)
- `resolution` (SFFloat, when applicable)

## GPS fields

- `name` (SFString)
- `accuracy` (SFFloat)
- `speedNoise` (SFFloat)
- `coordinateSystem` (SFString: `"local" | "WGS84"`)

## Gyro fields

- `name` (SFString)
- `lookupTable` (MFVec3f)

## Accelerometer fields

- `name` (SFString)
- `lookupTable` (MFVec3f)

## InertialUnit fields

- `name` (SFString)
- `noise` (SFFloat)

## Compass fields

- `name` (SFString)
- `lookupTable` (MFVec3f)

## TouchSensor fields

- `name` (SFString)
- `type` (SFString: `"bumper" | "force" | "force-3d"`)
- `lookupTable` (MFVec3f)
- `resolution` (SFFloat)

## RangeFinder fields

- `name` (SFString)
- `width` (SFInt32)
- `height` (SFInt32)
- `fieldOfView` (SFFloat)
- `near` (SFFloat)
- `minRange` (SFFloat)
- `maxRange` (SFFloat)
- `noise` (SFFloat)

## Radar fields

- `name` (SFString)
- `minRange` (SFFloat)
- `maxRange` (SFFloat)
- `horizontalFieldOfView` (SFFloat)
- `verticalFieldOfView` (SFFloat)

## LightSensor fields

- `name` (SFString)
- `lookupTable` (MFVec3f)

## PositionSensor fields

- `name` (SFString)
- attached under `Joint`
- no standalone measurement-range fields; joint type defines units

## Receiver fields

- `name` (SFString)
- `channel` (SFInt32)
- `bufferSize` (SFInt32)

## Altimeter fields

- `name` (SFString)
- `accuracy` (SFFloat)

## 5) Performance notes (sensor-centric)

- `Camera` and `Lidar` are highest-cost sensors.
- Increasing sampling period reduces CPU load and can stabilize real-time factor.
- `RangeFinder` cost scales with pixel count.
- `Lidar` cost scales with `horizontalResolution * numberOfLayers` and point cloud output.
- Keep high-frequency scalar sensors (`Gyro`, `Accelerometer`, `PositionSensor`) at controller rate when required for control loops.
