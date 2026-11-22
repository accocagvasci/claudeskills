# Webots Supervisor API Reference

This reference covers the Supervisor-specific API surface for C, C++, Python, Java, and MATLAB.

Scope constraints:
- Focus on Supervisor scene control, node/field access, simulation control, and recording.
- Exclude basic controller lifecycle explanation and sensor/actuator APIs.

Canonical source model:
- C API (`wb_supervisor_*`) is the authoritative base.
- C++, Python, and Java bindings expose equivalent functionality through `Supervisor`, `Node`, `Field`, and `Proto` classes.
- MATLAB calls the C API function names directly.

## Language mapping model

- C: `wb_supervisor_node_get_from_def`
- C++: `Supervisor::getFromDef`
- Python: `Supervisor.getFromDef`
- Java: `Supervisor.getFromDef`
- MATLAB: `wb_supervisor_node_get_from_def`

Name conversion pattern for OO bindings:
- Strip `wb_supervisor_` prefix.
- Convert snake case to camelCase.
- Move receiver domain to class:
  - `node_*` -> `Node` methods
  - `field_*` -> `Field` methods
  - `proto_*` -> `Proto` methods
  - global supervisor functions -> `Supervisor` methods

## Field type enum (`WbFieldType`)

```c
typedef enum {
  WB_NO_FIELD = 0x00,
  WB_SF_BOOL = 0x01,
  WB_SF_INT32,
  WB_SF_FLOAT,
  WB_SF_VEC2F,
  WB_SF_VEC3F,
  WB_SF_ROTATION,
  WB_SF_COLOR,
  WB_SF_STRING,
  WB_SF_NODE,
  WB_MF = 0x10,
  WB_MF_BOOL,
  WB_MF_INT32,
  WB_MF_FLOAT,
  WB_MF_VEC2F,
  WB_MF_VEC3F,
  WB_MF_ROTATION,
  WB_MF_COLOR,
  WB_MF_STRING,
  WB_MF_NODE
} WbFieldType;
```

## Simulation mode enum (`WbSimulationMode`)

```c
typedef enum {
  WB_SUPERVISOR_SIMULATION_MODE_PAUSE = 0,
  WB_SUPERVISOR_SIMULATION_MODE_REAL_TIME,
  WB_SUPERVISOR_SIMULATION_MODE_FAST
} WbSimulationMode;
```

## Node type enum (`WbNodeType`)

```c
typedef enum {
  WB_NODE_NO_NODE,
  // 3D rendering
  WB_NODE_APPEARANCE,
  WB_NODE_BACKGROUND,
  WB_NODE_BILLBOARD,
  WB_NODE_BOX,
  WB_NODE_CAD_SHAPE,
  WB_NODE_CAPSULE,
  WB_NODE_COLOR,
  WB_NODE_CONE,
  WB_NODE_COORDINATE,
  WB_NODE_CYLINDER,
  WB_NODE_DIRECTIONAL_LIGHT,
  WB_NODE_ELEVATION_GRID,
  WB_NODE_FOG,
  WB_NODE_GROUP,
  WB_NODE_IMAGE_TEXTURE,
  WB_NODE_INDEXED_FACE_SET,
  WB_NODE_INDEXED_LINE_SET,
  WB_NODE_MATERIAL,
  WB_NODE_MESH,
  WB_NODE_MUSCLE,
  WB_NODE_NORMAL,
  WB_NODE_PBR_APPEARANCE,
  WB_NODE_PLANE,
  WB_NODE_POINT_LIGHT,
  WB_NODE_POINT_SET,
  WB_NODE_POSE,
  WB_NODE_SHAPE,
  WB_NODE_SPHERE,
  WB_NODE_SPOT_LIGHT,
  WB_NODE_TEXTURE_COORDINATE,
  WB_NODE_TEXTURE_TRANSFORM,
  WB_NODE_TRANSFORM,
  WB_NODE_VIEWPOINT,
  // robots
  WB_NODE_ROBOT,
  // devices
  WB_NODE_ACCELEROMETER,
  WB_NODE_ALTIMETER,
  WB_NODE_BRAKE,
  WB_NODE_CAMERA,
  WB_NODE_COMPASS,
  WB_NODE_CONNECTOR,
  WB_NODE_DISPLAY,
  WB_NODE_DISTANCE_SENSOR,
  WB_NODE_EMITTER,
  WB_NODE_GPS,
  WB_NODE_GYRO,
  WB_NODE_INERTIAL_UNIT,
  WB_NODE_LED,
  WB_NODE_LIDAR,
  WB_NODE_LIGHT_SENSOR,
  WB_NODE_LINEAR_MOTOR,
  WB_NODE_PEN,
  WB_NODE_POSITION_SENSOR,
  WB_NODE_PROPELLER,
  WB_NODE_RADAR,
  WB_NODE_RANGE_FINDER,
  WB_NODE_RECEIVER,
  WB_NODE_ROTATIONAL_MOTOR,
  WB_NODE_SKIN,
  WB_NODE_SPEAKER,
  WB_NODE_TOUCH_SENSOR,
  WB_NODE_VACUUM_GRIPPER,
  // misc
  WB_NODE_BALL_JOINT,
  WB_NODE_BALL_JOINT_PARAMETERS,
  WB_NODE_CHARGER,
  WB_NODE_CONTACT_PROPERTIES,
  WB_NODE_DAMPING,
  WB_NODE_FLUID,
  WB_NODE_FOCUS,
  WB_NODE_HINGE_JOINT,
  WB_NODE_HINGE_JOINT_PARAMETERS,
  WB_NODE_HINGE_2_JOINT,
  WB_NODE_IMMERSION_PROPERTIES,
  WB_NODE_JOINT_PARAMETERS,
  WB_NODE_LENS,
  WB_NODE_LENS_FLARE,
  WB_NODE_PHYSICS,
  WB_NODE_RECOGNITION,
  WB_NODE_SLIDER_JOINT,
  WB_NODE_SLOT,
  WB_NODE_SOLID,
  WB_NODE_SOLID_REFERENCE,
  WB_NODE_TRACK,
  WB_NODE_TRACK_WHEEL,
  WB_NODE_WORLD_INFO,
  WB_NODE_ZOOM,
  // experimental
  WB_NODE_MICROPHONE,
  WB_NODE_RADIO
} WbNodeType;
```

## World and simulation functions

### C (complete)

```c
void wb_supervisor_world_load(const char *filename);
bool wb_supervisor_world_save(const char *filename);
void wb_supervisor_world_reload();

void wb_supervisor_simulation_quit(int status);
void wb_supervisor_simulation_reset();
void wb_supervisor_simulation_reset_physics();
WbSimulationMode wb_supervisor_simulation_get_mode();
void wb_supervisor_simulation_set_mode(WbSimulationMode mode);
```

### C++ / Python / Java / MATLAB

- C++: `Supervisor::worldLoad/worldSave/worldReload`, `simulationQuit/simulationReset/simulationResetPhysics/simulationGetMode/simulationSetMode`
- Python: `Supervisor.worldLoad/worldSave/worldReload`, `simulationQuit/simulationReset/simulationResetPhysics/simulationGetMode/simulationSetMode`
- Java: same method names as Python
- MATLAB: same C function names

## Label/HUD function

### C (complete)

```c
void wb_supervisor_set_label(
  int id,
  const char *text,
  double x,
  double y,
  double size,
  int color,
  double transparency,
  const char *font
);
```

### C++ / Python / Java / MATLAB

- C++ / Python / Java: `Supervisor::setLabel(...)`
- MATLAB: `wb_supervisor_set_label(...)`

## Image, movie, animation recording

### C (complete)

```c
void wb_supervisor_export_image(const char *filename, int quality);

void wb_supervisor_movie_start_recording(
  const char *filename,
  int width,
  int height,
  int codec,
  int quality,
  int acceleration,
  bool caption
);
void wb_supervisor_movie_stop_recording();
bool wb_supervisor_movie_is_ready();
bool wb_supervisor_movie_failed();

bool wb_supervisor_animation_start_recording(const char *filename);
bool wb_supervisor_animation_stop_recording();
```

### C++ / Python / Java / MATLAB

- C++ / Python / Java: `exportImage`, `movieStartRecording`, `movieStopRecording`, `movieIsReady`, `movieFailed`, `animationStartRecording`, `animationStopRecording`
- MATLAB: same C function names

## COMPLETE `wb_supervisor_node_*` API

### C (complete)

```c
WbNodeRef wb_supervisor_node_get_root();
WbNodeRef wb_supervisor_node_get_self();
int wb_supervisor_node_get_id(WbNodeRef node);
WbNodeRef wb_supervisor_node_get_from_id(int id);
WbNodeRef wb_supervisor_node_get_from_device(WbDeviceTag tag);
WbNodeRef wb_supervisor_node_get_from_def(const char *def);
WbNodeRef wb_supervisor_node_get_from_proto_def(WbNodeRef node, const char *def);
WbNodeRef wb_supervisor_node_get_parent_node(WbNodeRef node);
WbNodeRef wb_supervisor_node_get_selected();
WbNodeType wb_supervisor_node_get_type(WbNodeRef node);

WbFieldRef wb_supervisor_node_get_field(WbNodeRef node, const char *field_name);
WbFieldRef wb_supervisor_node_get_field_by_index(WbNodeRef node, const int index);
int wb_supervisor_node_get_number_of_fields(WbNodeRef node);
WbFieldRef wb_supervisor_node_get_base_node_field(WbNodeRef node, const char *field_name);
WbFieldRef wb_supervisor_node_get_base_node_field_by_index(WbNodeRef node, int index);
int wb_supervisor_node_get_number_of_base_node_fields(WbNodeRef node);

void wb_supervisor_node_remove(WbNodeRef node);
void wb_supervisor_node_save_state(WbNodeRef node, const char *state_name);
void wb_supervisor_node_load_state(WbNodeRef node, const char *state_name);
void wb_supervisor_node_set_joint_position(WbNodeRef node, double position, int index);
WbProtoRef wb_supervisor_node_get_proto(WbNodeRef node);

const char *wb_supervisor_node_get_def(WbNodeRef node);
const char *wb_supervisor_node_get_type_name(WbNodeRef node);
const char *wb_supervisor_node_get_base_type_name(WbNodeRef node);
bool wb_supervisor_node_is_proto(WbNodeRef node);
const double *wb_supervisor_node_get_center_of_mass(WbNodeRef node);

const double *wb_supervisor_node_get_contact_point(WbNodeRef node, int index);
WbNodeRef wb_supervisor_node_get_contact_point_node(WbNodeRef node, int index);
int wb_supervisor_node_get_number_of_contact_points(WbNodeRef node, bool include_descendants);
WbContactPoint *wb_supervisor_node_get_contact_points(WbNodeRef node, bool include_descendants, int *size);

const double *wb_supervisor_node_get_orientation(WbNodeRef node);
const double *wb_supervisor_node_get_position(WbNodeRef node);
const double *wb_supervisor_node_get_pose(WbNodeRef node, WbNodeRef from_node);
bool wb_supervisor_node_get_static_balance(WbNodeRef node);
const double *wb_supervisor_node_get_velocity(WbNodeRef node);
void wb_supervisor_node_set_velocity(WbNodeRef node, const double velocity[6]);
void wb_supervisor_node_reset_physics(WbNodeRef node);
void wb_supervisor_node_restart_controller(WbNodeRef node);
const char *wb_supervisor_node_export_string(WbNodeRef node);

void wb_supervisor_node_move_viewpoint(WbNodeRef node);
void wb_supervisor_node_set_visibility(WbNodeRef node, WbNodeRef from, bool visible);

void wb_supervisor_node_add_force(WbNodeRef node, const double force[3], bool relative);
void wb_supervisor_node_add_force_with_offset(
  WbNodeRef node,
  const double force[3],
  const double offset[3],
  bool relative
);
void wb_supervisor_node_add_torque(WbNodeRef node, const double torque[3], bool relative);

void wb_supervisor_node_enable_pose_tracking(WbNodeRef node, int sampling_period, WbNodeRef from_node);
void wb_supervisor_node_disable_pose_tracking(WbNodeRef node, WbNodeRef from_node);

void wb_supervisor_node_enable_contact_points_tracking(
  WbNodeRef node,
  int sampling_period,
  bool include_descendants
);
void wb_supervisor_node_disable_contact_points_tracking(WbNodeRef node);
```

### C++ / Python / Java (Node + Supervisor methods)

- Lookup/identity: `getRoot`, `getSelf`, `getFromDef`, `getFromId`, `getFromDevice`, `getSelected`, `Node.getId`, `Node.getDef`, `Node.getParentNode`
- Node typing/proto: `Node.getType`, `Node.getTypeName`, `Node.getBaseTypeName`, `Node.isProto`, `Node.getProto`, `Node.getFromProtoDef`
- Node fields: `Node.getField`, `Node.getFieldByIndex`, `Node.getNumberOfFields`, `Node.getBaseNodeField`, `Node.getBaseNodeFieldByIndex`, `Node.getNumberOfBaseNodeFields`
- Pose/kinematics: `Node.getPosition`, `Node.getOrientation`, `Node.getPose`, `Node.getVelocity`, `Node.setVelocity`, `Node.getCenterOfMass`, `Node.getStaticBalance`
- Contacts: `Node.getContactPoint`, `Node.getContactPointNode`, `Node.getNumberOfContactPoints`, `Node.getContactPoints`, `Node.enableContactPointsTracking`, `Node.disableContactPointsTracking`
- State/control: `Node.saveState`, `Node.loadState`, `Node.setJointPosition`, `Node.resetPhysics`, `Node.restartController`, `Node.remove`, `Node.exportString`, `Node.moveViewpoint`, `Node.setVisibility`
- Forces: `Node.addForce`, `Node.addForceWithOffset`, `Node.addTorque`
- Tracking: `Node.enablePoseTracking`, `Node.disablePoseTracking`

### MATLAB

- Same C function names and parameters (`wb_supervisor_node_*`).

## COMPLETE `wb_supervisor_field_*` API

### C (complete)

```c
const char *wb_supervisor_field_get_name(WbFieldRef field);
WbFieldType wb_supervisor_field_get_type(WbFieldRef field);
const char *wb_supervisor_field_get_type_name(WbFieldRef field);
int wb_supervisor_field_get_count(WbFieldRef field);
WbFieldRef wb_supervisor_field_get_actual_field(WbFieldRef field);

void wb_supervisor_field_enable_sf_tracking(WbFieldRef field, int sampling_period);
void wb_supervisor_field_disable_sf_tracking(WbFieldRef field);

bool wb_supervisor_field_get_sf_bool(WbFieldRef field);
int wb_supervisor_field_get_sf_int32(WbFieldRef field);
double wb_supervisor_field_get_sf_float(WbFieldRef field);
const double *wb_supervisor_field_get_sf_vec2f(WbFieldRef field);
const double *wb_supervisor_field_get_sf_vec3f(WbFieldRef field);
const double *wb_supervisor_field_get_sf_rotation(WbFieldRef field);
const double *wb_supervisor_field_get_sf_color(WbFieldRef field);
const char *wb_supervisor_field_get_sf_string(WbFieldRef field);
WbNodeRef wb_supervisor_field_get_sf_node(WbFieldRef field);

bool wb_supervisor_field_get_mf_bool(WbFieldRef field, int index);
int wb_supervisor_field_get_mf_int32(WbFieldRef field, int index);
double wb_supervisor_field_get_mf_float(WbFieldRef field, int index);
const double *wb_supervisor_field_get_mf_vec2f(WbFieldRef field, int index);
const double *wb_supervisor_field_get_mf_vec3f(WbFieldRef field, int index);
const double *wb_supervisor_field_get_mf_color(WbFieldRef field, int index);
const double *wb_supervisor_field_get_mf_rotation(WbFieldRef field, int index);
const char *wb_supervisor_field_get_mf_string(WbFieldRef field, int index);
WbNodeRef wb_supervisor_field_get_mf_node(WbFieldRef field, int index);

void wb_supervisor_field_set_sf_bool(WbFieldRef field, bool value);
void wb_supervisor_field_set_sf_int32(WbFieldRef field, int value);
void wb_supervisor_field_set_sf_float(WbFieldRef field, double value);
void wb_supervisor_field_set_sf_vec2f(WbFieldRef field, const double values[2]);
void wb_supervisor_field_set_sf_vec3f(WbFieldRef field, const double values[3]);
void wb_supervisor_field_set_sf_rotation(WbFieldRef field, const double values[4]);
void wb_supervisor_field_set_sf_color(WbFieldRef field, const double values[3]);
void wb_supervisor_field_set_sf_string(WbFieldRef field, const char *value);

void wb_supervisor_field_set_mf_bool(WbFieldRef field, int index, bool value);
void wb_supervisor_field_set_mf_int32(WbFieldRef field, int index, int value);
void wb_supervisor_field_set_mf_float(WbFieldRef field, int index, double value);
void wb_supervisor_field_set_mf_vec2f(WbFieldRef field, int index, const double values[2]);
void wb_supervisor_field_set_mf_vec3f(WbFieldRef field, int index, const double values[3]);
void wb_supervisor_field_set_mf_rotation(WbFieldRef field, int index, const double values[4]);
void wb_supervisor_field_set_mf_color(WbFieldRef field, int index, const double values[3]);
void wb_supervisor_field_set_mf_string(WbFieldRef field, int index, const char *value);

void wb_supervisor_field_insert_mf_bool(WbFieldRef field, int index, bool value);
void wb_supervisor_field_insert_mf_int32(WbFieldRef field, int index, int value);
void wb_supervisor_field_insert_mf_float(WbFieldRef field, int index, double value);
void wb_supervisor_field_insert_mf_vec2f(WbFieldRef field, int index, const double values[2]);
void wb_supervisor_field_insert_mf_vec3f(WbFieldRef field, int index, const double values[3]);
void wb_supervisor_field_insert_mf_rotation(WbFieldRef field, int index, const double values[4]);
void wb_supervisor_field_insert_mf_color(WbFieldRef field, int index, const double values[3]);
void wb_supervisor_field_insert_mf_string(WbFieldRef field, int index, const char *value);

void wb_supervisor_field_remove_mf(WbFieldRef field, int index);
void wb_supervisor_field_import_mf_node_from_string(WbFieldRef field, int position, const char *node_string);
void wb_supervisor_field_remove_sf(WbFieldRef field);
void wb_supervisor_field_import_sf_node_from_string(WbFieldRef field, const char *node_string);
```

### C++ / Python / Java (Field methods)

- Introspection: `getName`, `getType`, `getTypeName`, `getCount`, `getActualField`
- Tracking: `enableSFTracking`, `disableSFTracking`
- SF getters/setters: `getSFBool/getSFInt32/getSFFloat/getSFVec2f/getSFVec3f/getSFRotation/getSFColor/getSFString/getSFNode`; `setSFBool/setSFInt32/setSFFloat/setSFVec2f/setSFVec3f/setSFRotation/setSFColor/setSFString`
- MF getters/setters: `getMFBool/getMFInt32/getMFFloat/getMFVec2f/getMFVec3f/getMFColor/getMFRotation/getMFString/getMFNode`; `setMFBool/setMFInt32/setMFFloat/setMFVec2f/setMFVec3f/setMFRotation/setMFColor/setMFString`
- MF insert/remove/import: `insertMFBool/insertMFInt32/insertMFFloat/insertMFVec2f/insertMFVec3f/insertMFRotation/insertMFColor/insertMFString`, `removeMF`, `importMFNodeFromString`
- SF remove/import: `removeSF`, `importSFNodeFromString`

### MATLAB

- Same C function names and parameters (`wb_supervisor_field_*`).

## Proto inspection API

### C (complete)

```c
const char *wb_supervisor_proto_get_type_name(WbProtoRef proto);
bool wb_supervisor_proto_is_derived(WbProtoRef proto);
WbProtoRef wb_supervisor_proto_get_parent(WbProtoRef proto);
WbFieldRef wb_supervisor_proto_get_field(WbProtoRef proto, const char *field_name);
WbFieldRef wb_supervisor_proto_get_field_by_index(WbProtoRef proto, int index);
int wb_supervisor_proto_get_number_of_fields(WbProtoRef proto);
```

### C++ / Python / Java / MATLAB

- C++ / Python / Java: `Proto` class equivalents (`getTypeName`, `isDerived`, `getParent`, `getField`, `getFieldByIndex`, `getNumberOfFields`)
- MATLAB: same C function names

## VR headset supervisor helpers

### C

```c
bool wb_supervisor_virtual_reality_headset_is_used();
const double *wb_supervisor_virtual_reality_headset_get_position();
const double *wb_supervisor_virtual_reality_headset_get_orientation();
```

Equivalent wrappers exist in OO bindings following the same naming pattern.

## Deprecated Supervisor functions

### Deprecated node tracking aliases

```c
void wb_supervisor_node_enable_contact_point_tracking(
  WbNodeRef node,
  int sampling_period,
  bool include_descendants
);
void wb_supervisor_node_disable_contact_point_tracking(
  WbNodeRef node,
  bool include_descendants
);
```

### Deprecated simulation/world aliases

```c
void wb_supervisor_simulation_revert();
void wb_supervisor_load_world(const char *filename);
bool wb_supervisor_save_world(const char *filename);
void wb_supervisor_simulation_physics_reset();
```

### Deprecated field and movie status APIs

```c
void wb_supervisor_field_remove_mf_node(WbFieldRef field, int position);

#define WB_SUPERVISOR_MOVIE_READY 0
#define WB_SUPERVISOR_MOVIE_RECORDING 1
#define WB_SUPERVISOR_MOVIE_SAVING 2
#define WB_SUPERVISOR_MOVIE_WRITE_ERROR 3
#define WB_SUPERVISOR_MOVIE_ENCODING_ERROR 4
#define WB_SUPERVISOR_MOVIE_SIMULATION_ERROR 5

int wb_supervisor_movie_get_status();
void wb_supervisor_start_movie(const char *file, int width, int height, int codec, int quality, int acceleration, bool caption);
void wb_supervisor_stop_movie();
int wb_supervisor_get_movie_status();
```

## High-value usage notes for experiments and RL

- Supervisor extends Robot: call both Robot APIs and Supervisor APIs in one controller.
- Episode reset pattern: `simulationReset`/`simulationResetPhysics`, field writes, warmup steps, observation reads.
- PROTO internal fields accessed through base-node field APIs are read-only.
- Re-fetch node and field references after world reload or PROTO regeneration.
- Tracking APIs (`enablePoseTracking`, `enableContactPointsTracking`, `enableSFTracking`) reduce repeated request overhead.
