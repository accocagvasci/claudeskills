# Webots Controller API Reference (Robot-Level, All Languages)

Scope this document to controller lifecycle, simulation stepping, robot metadata, device discovery, controller arguments, and process behavior. Exclude per-sensor/per-actuator method catalogs.

## 1) Language Bootstrap and Entry Points

Use one bootstrap per controller language.

```python
# Python
from controller import Robot
robot = Robot()
```

```c
// C
#include <webots/robot.h>

int main(int argc, char **argv) {
  wb_robot_init();
  /* ... */
  wb_robot_cleanup();
  return 0;
}
```

```cpp
// C++
#include <webots/Robot.hpp>
using namespace webots;

int main(int argc, char **argv) {
  Robot robot;
  /* ... */
  return 0;
}
```

```java
// Java
import com.cyberbotics.webots.controller.Robot;

public class MyController {
  public static void main(String[] args) {
    Robot robot = new Robot();
    /* ... */
  }
}
```

```matlab
% MATLAB
% Controller file/function name must match controller name in Robot.controller
TIME_STEP = wb_robot_get_basic_time_step();
wb_robot_init();
while wb_robot_step(TIME_STEP) ~= -1
  % ...
end
wb_robot_cleanup();
```

## 2) Critical Simulation Loop Contract

Treat this as the mandatory contract for all controller languages:

1. Initialize robot API.
2. Read `basicTimeStep`.
3. Loop on `step(control_step)` until return value is `-1`.
4. Cleanup and exit.

Behavior guarantees:

- `step` advances simulation time and synchronizes data exchange.
- Sensor data updates only during `step` calls.
- Actuator commands are committed on the next `step`.
- `step == -1` means simulation/controller termination path.

## 3) Robot Class / Robot Module API (Core)

### C API (authoritative low-level API)

- `void wb_robot_init(void);`
- `int wb_robot_step(int duration);`
- `void wb_robot_cleanup(void);`
- `WbDeviceTag wb_robot_get_device(const char *name);`
- `int wb_robot_get_number_of_devices(void);`
- `WbDeviceTag wb_robot_get_device_by_index(int index);`
- `int wb_robot_get_basic_time_step(void);`
- `double wb_robot_get_time(void);`
- `const char *wb_robot_get_name(void);`
- `const char *wb_robot_get_model(void);`
- `const char *wb_robot_get_project_path(void);`
- `const char *wb_robot_get_world_path(void);`
- `const char *wb_robot_get_custom_data(void);`
- `void wb_robot_set_custom_data(const char *data);`
- `int wb_robot_get_mode(void);`
- `void wb_robot_set_mode(int mode, const char *arg);`
- `int wb_robot_get_synchronization(void);`
- `void wb_robot_battery_sensor_enable(int sampling_period);`
- `void wb_robot_battery_sensor_disable(void);`
- `int wb_robot_battery_sensor_get_sampling_period(void);`
- `double wb_robot_battery_sensor_get_value(void);`
- `const char *wb_robot_get_urdf(const char *prefix);`
- `void wb_robot_pin_to_static_environment(bool pin);`
- `int wb_robot_wait_for_user_input_event(int event_type, int timeout);`
- `const void *wb_robot_get_data(void);`
- `int wb_robot_get_data_size(void);`
- `void wb_robot_set_data(const void *data, int size);`

Notes:

- Keep `duration` in `wb_robot_step(duration)` as an integer multiple of `WorldInfo.basicTimeStep`.
- Keep pointers returned by `wb_robot_get_*` functions as read-only.
- Treat `wb_robot_get_device(name)` as name-sensitive lookup.

### Python `Robot` mapping (core methods)

- `Robot()`
- `step(time_step: int) -> int`
- `getBasicTimeStep() -> float`
- `getTime() -> float`
- `getDevice(name: str) -> Device`
- `getNumberOfDevices() -> int`
- `getDeviceByIndex(index: int) -> Device`
- `getName() -> str`
- `getModel() -> str`
- `getProjectPath() -> str`
- `getWorldPath() -> str`
- `getCustomData() -> str`
- `setCustomData(data: str) -> None`
- `getMode() -> int`
- `setMode(mode: int, arg: str) -> None`
- `getSynchronization() -> int`
- `batterySensorEnable(sampling_period: int) -> None`
- `batterySensorDisable() -> None`
- `batterySensorGetSamplingPeriod() -> int`
- `batterySensorGetValue() -> float`
- `getUrdf(prefix: str) -> str`
- `pinToStaticEnvironment(pin: bool) -> None`
- `waitForUserInputEvent(event_type: int, timeout: int) -> int`
- `getData() -> bytes`
- `setData(data: bytes) -> None`

### C++ `webots::Robot` mapping (core methods)

- `Robot()` / destructor
- `int step(int duration)`
- `int getBasicTimeStep() const`
- `double getTime() const`
- `Device *getDevice(const std::string &name)`
- `int getNumberOfDevices() const`
- `Device *getDeviceByIndex(int index) const`
- `std::string getName() const`
- `std::string getModel() const`
- `std::string getProjectPath() const`
- `std::string getWorldPath() const`
- `std::string getCustomData() const`
- `void setCustomData(const std::string &data)`
- `int getMode() const`
- `void setMode(int mode, const std::string &arg)`
- `bool getSynchronization() const`
- `void batterySensorEnable(int sampling_period)`
- `void batterySensorDisable()`
- `int batterySensorGetSamplingPeriod() const`
- `double batterySensorGetValue() const`
- `std::string getUrdf(const std::string &prefix) const`
- `void pinToStaticEnvironment(bool pin)`
- `int waitForUserInputEvent(int event_type, int timeout)`
- `const void *getData() const`
- `int getDataSize() const`
- `void setData(const void *data, int size)`

### Java `com.cyberbotics.webots.controller.Robot` mapping (core methods)

- `Robot()`
- `int step(int duration)`
- `double getBasicTimeStep()`
- `double getTime()`
- `Device getDevice(String name)`
- `int getNumberOfDevices()`
- `Device getDeviceByIndex(int index)`
- `String getName()`
- `String getModel()`
- `String getProjectPath()`
- `String getWorldPath()`
- `String getCustomData()`
- `void setCustomData(String data)`
- `int getMode()`
- `void setMode(int mode, String arg)`
- `boolean getSynchronization()`
- `void batterySensorEnable(int samplingPeriod)`
- `void batterySensorDisable()`
- `int batterySensorGetSamplingPeriod()`
- `double batterySensorGetValue()`
- `String getUrdf(String prefix)`
- `void pinToStaticEnvironment(boolean pin)`
- `int waitForUserInputEvent(int eventType, int timeout)`
- `byte[] getData()`
- `void setData(byte[] data)`

### MATLAB mapping (core functions)

- `wb_robot_init()`
- `wb_robot_step(duration)`
- `wb_robot_cleanup()`
- `wb_robot_get_device(name)`
- `wb_robot_get_number_of_devices()`
- `wb_robot_get_device_by_index(index)`
- `wb_robot_get_basic_time_step()`
- `wb_robot_get_time()`
- `wb_robot_get_name()`
- `wb_robot_get_model()`
- `wb_robot_get_project_path()`
- `wb_robot_get_world_path()`
- `wb_robot_get_custom_data()`
- `wb_robot_set_custom_data(data)`
- `wb_robot_get_mode()`
- `wb_robot_set_mode(mode, arg)`
- `wb_robot_get_synchronization()`
- `wb_robot_battery_sensor_enable(sampling_period)`
- `wb_robot_battery_sensor_disable()`
- `wb_robot_battery_sensor_get_sampling_period()`
- `wb_robot_battery_sensor_get_value()`
- `wb_robot_get_urdf(prefix)`
- `wb_robot_pin_to_static_environment(pin)`
- `wb_robot_wait_for_user_input_event(event_type, timeout)`
- `wb_robot_get_data()`
- `wb_robot_set_data(data)`

## 4) Required Functions Called Out Explicitly

Always include these in minimal controller templates and diagnostics:

- Initialization: `wb_robot_init()`
- Loop advance: `wb_robot_step(...)`
- Finalization: `wb_robot_cleanup()`
- Device access: `wb_robot_get_device(...)`
- Device count: `wb_robot_get_number_of_devices()`
- Timing: `wb_robot_get_basic_time_step()`, `wb_robot_get_time()`
- Metadata: `wb_robot_get_name()`, `wb_robot_get_model()`

## 5) Device Tag System (C and Cross-Language Concept)

`WbDeviceTag` is an opaque handle that identifies one device attached to the current Robot node.

Core rules:

1. Obtain tag by exact name: `wb_robot_get_device("name")`.
2. Keep and reuse returned tag for all subsequent device API calls.
3. Enumerate tags via `wb_robot_get_number_of_devices()` + `wb_robot_get_device_by_index(i)`.
4. Treat tag value as process-local opaque identifier; do not serialize or infer type from raw value.
5. Resolve concrete device type by typed API usage (or runtime checks where available), not by tag numeric content.

Cross-language note:

- Python/C++/Java wrap the same concept as `Device` objects returned by `getDevice(...)`.
- Name matching remains strict in every language.

## 6) C/C++ Makefile Template

Use Webots-provided build variables and include paths from controller environment.

```make
# controllers/my_controller/Makefile
WEBOTS_HOME ?= /usr/local/webots

CFLAGS  = -O2 -Wall -Wextra -I"$(WEBOTS_HOME)/include/controller/c"
CXXFLAGS = -O2 -Wall -Wextra -std=c++17 -I"$(WEBOTS_HOME)/include/controller/cpp"
LDFLAGS = -L"$(WEBOTS_HOME)/lib/controller" -Wl,-rpath,"$(WEBOTS_HOME)/lib/controller"

all: my_controller my_controller_cpp

my_controller: my_controller.c
	$(CC) $(CFLAGS) $< -o $@ $(LDFLAGS) -lController

my_controller_cpp: my_controller.cpp
	$(CXX) $(CXXFLAGS) $< -o $@ $(LDFLAGS) -lCppController

clean:
	rm -f my_controller my_controller_cpp
```

Adapt library names/paths to installed Webots version and platform packaging.

## 7) Controller Arguments

Define controller arguments in Robot field `controllerArgs`.

- Python: read from `sys.argv`.
- C/C++: read from `argc/argv` in `main`.
- Java: read from `String[] args` in `main`.
- MATLAB: parse command-line-style data through wrapper/environment conventions if used.

## 8) Termination and Process Behavior

Termination behavior:

- Return from loop when `step(...) == -1`.
- Execute cleanup path promptly (`wb_robot_cleanup()` in C/MATLAB; normal object teardown in OO APIs).
- Avoid long blocking work after step returns `-1`.
- Expect forced termination (`SIGKILL`) if process does not exit quickly; practical timeout is about 1 second.

Operational implication:

- Flush logs/state incrementally during runtime instead of relying on long shutdown handlers.
- Keep shutdown code minimal and deterministic.
