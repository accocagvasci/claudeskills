# Webots Humans and Assets Reference

## Scope

This reference covers:
- `CharacterSkin`, `Pedestrian`, `C3dViewer`, and BVH utility API.
- Complete `projects/objects` PROTO catalog (310 PROTOs).
- Pre-built environment/world inventory relevant to humans and scene assets.
- `TexturedBackground` and `TexturedBackgroundLight` options.

This reference does not cover:
- `Skin` node internals.
- Robot model catalogs.

## CharacterSkin PROTO Complete Field Reference

**Source:** `projects/humans/skin_animated_humans/protos/CharacterSkin.proto`

| Field | Type | Default | Notes |
|---|---|---|---|
| `translation` | `SFVec3f` | `0 0 0` | Character base translation. |
| `rotation` | `SFRotation` | `0.57735 0.57735 0.57735 2.0944` | Character base rotation. |
| `scale` | `SFVec3f` | `1 1 1` | Character uniform/nonuniform scale. |
| `name` | `SFString` | `"skin"` | Node name. |
| `model` | `SFString` enum | `"Sandra"` | Character variant: `Anthony`, `Robert`, `Sandra`, `Sophia`. |
| `castShadows` | `SFBool` | `TRUE` | Toggle shadow casting. |

### CharacterSkin Model Variants

| Model | Description |
|---|---|
| `Anthony` | boy |
| `Robert` | man |
| `Sandra` | woman |
| `Sophia` | girl |

### CharacterSkin Practical Notes

- Derived from `Skin` node and backed by FBX mesh + texture sets.
- Asset generation pipeline in Webots sources is based on MakeHuman-exported FBX files.
- BVH playback is typically implemented through `bvh_util` C helper functions.

## Pedestrian PROTO Complete Field Reference

**Source:** `projects/humans/pedestrian/protos/Pedestrian.proto`

### Public Fields

| Field | Type | Default | Notes |
|---|---|---|---|
| `translation` | `SFVec3f` | `0 0 1.27` | Robot placement. |
| `rotation` | `SFRotation` | `0 0 1 0` | Robot heading/orientation. |
| `name` | `SFString` | `"pedestrian"` | Robot name. |
| `controller` | `SFString` | `"pedestrian"` | Walking controller. |
| `controllerArgs` | `MFString` | `[]` | Runtime options (speed/path/timestep). |
| `shirtColor` | `SFColor` | `0.25 0.55 0.20` | Shirt material color. |
| `pantsColor` | `SFColor` | `0.24 0.25 0.5` | Pants material color. |
| `shoesColor` | `SFColor` | `0.28 0.15 0.06` | Shoes material color. |
| `skinColor` | `SFColor` | `1.0 0.75 0.7` | Skin material color. |
| `enableBoundingObject` | `SFBool` | `FALSE` | Enable collision volumes per body part. |
| `bodySlot` | `MFNode` | `[]` | Extra nodes at body center. |
| `leftHandSlot` | `MFNode` | `[]` | Extra nodes attached to left hand. |
| `rightHandSlot` | `MFNode` | `[]` | Extra nodes attached to right hand. |

### Hidden Joint Fields (Passive Joint Positions)

| Field | Type | Default | Joint Group |
|---|---|---|---|
| `headAngle` | `SFFloat` | `0.0` | Head |
| `leftArmAngle` | `SFFloat` | `0.0` | Left arm |
| `leftLowerArmAngle` | `SFFloat` | `0.0` | Left arm |
| `leftHandAngle` | `SFFloat` | `0.0` | Left arm |
| `rightArmAngle` | `SFFloat` | `0.0` | Right arm |
| `rightLowerArmAngle` | `SFFloat` | `0.0` | Right arm |
| `rightHandAngle` | `SFFloat` | `0.0` | Right arm |
| `leftLegAngle` | `SFFloat` | `0.0` | Left leg |
| `leftLowerLegAngle` | `SFFloat` | `0.0` | Left leg |
| `leftFootAngle` | `SFFloat` | `0.0` | Left leg |
| `rightLegAngle` | `SFFloat` | `0.0` | Right leg |
| `rightLowerLegAngle` | `SFFloat` | `0.0` | Right leg |
| `rightFootAngle` | `SFFloat` | `0.0` | Right leg |

### Pedestrian Joint Names and Segment Names

**Passive joint position names (13 total):**
- `headAngle`
- `leftArmAngle`, `leftLowerArmAngle`, `leftHandAngle`
- `rightArmAngle`, `rightLowerArmAngle`, `rightHandAngle`
- `leftLegAngle`, `leftLowerLegAngle`, `leftFootAngle`
- `rightLegAngle`, `rightLowerLegAngle`, `rightFootAngle`

**Named top-level hinge nodes:**
- `LEFT_ARM`, `RIGHT_ARM`, `LEFT_LEG`, `RIGHT_LEG`, `HEAD`

**Segment/Solid names in the PROTO hierarchy:**
- `left arm`, `right arm`, `left leg`, `right leg`, `head`
- `LEFT_UPPER_ARM`, `LEFT_LOWER_ARM`, `LEFT_HAND`
- `RIGHT_UPPER_ARM`, `RIGHT_LOWER_ARM`, `RIGHT_HAND`
- `LEFT_UPPER_LEG`, `LEFT_LOWER_LEG`, `LEFT_FOOT`
- `RIGHT_UPPER_LEG`, `RIGHT_LOWER_LEG`, `RIGHT_FEET` (as defined in source)

### Pedestrian Controller Arguments

Common arguments used by `pedestrian` controller:
- `--trajectory=[x1 y1, x2 y2, ...]`
- `--speed=<m/s>`
- `--step=<milliseconds>`

## BVH Util Library Complete C API

**Source:** `projects/humans/skin_animated_humans/libraries/bvh_util/include/webots/bvh_util.h`

### Type

```c
typedef struct WbuBvhMotionPrivate *WbuBvhMotion;
```

### Loading and Lifecycle

```c
WbuBvhMotion wbu_bvh_read_file(const char *filename);
void wbu_bvh_cleanup(WbuBvhMotion motion);
const char *wbu_bvh_get_filename(WbuBvhMotion motion);
```

### Joint and Frame Introspection

```c
int wbu_bvh_get_joint_count(const WbuBvhMotion motion);
const char *wbu_bvh_get_joint_name(const WbuBvhMotion motion, int joint_id);
int wbu_bvh_get_frame_count(const WbuBvhMotion motion);
int wbu_bvh_get_frame_index(const WbuBvhMotion motion);
```

### Playback Control

```c
bool wbu_bvh_step(WbuBvhMotion motion);
bool wbu_bvh_goto_frame(WbuBvhMotion motion, int frame_number);
bool wbu_bvh_reset(WbuBvhMotion motion);
```

### Data Access and Retargeting

```c
void wbu_bvh_set_scale(WbuBvhMotion motion, double scale);
const double *wbu_bvh_get_root_translation(const WbuBvhMotion motion);
const double *wbu_bvh_get_joint_rotation(const WbuBvhMotion motion, int joint_id);
void wbu_bvh_set_model_t_pose(const WbuBvhMotion motion, const double *axisAngle, int joint_id, bool global);
```

### Function Notes

- `wbu_bvh_set_scale`: scales root translation data from BVH space to world meters.
- `wbu_bvh_set_model_t_pose`: aligns BVH joint orientation to target model T-pose (global/local).
- `wbu_bvh_step`: advances frame; returns `true` on success.
- `wbu_bvh_goto_frame`: random access to a specific frame.
- `wbu_bvh_get_joint_rotation`: returns axis-angle rotation pointer for joint at current frame.
- `wbu_bvh_get_root_translation`: returns current root translation pointer.

## C3dViewer PROTO Field Reference

**Source:** `projects/humans/c3d/protos/C3dViewer.proto`

| Field | Type | Default | Notes |
|---|---|---|---|
| `translation` | `SFVec3f` | `0 0 0` | Viewer robot placement. |
| `rotation` | `SFRotation` | `0 0 1 0` | Viewer robot orientation. |
| `c3dFile` | `SFString` | `""` | C3D file path to load. |
| `playbackSpeed` | `SFFloat` | `1.0` | Playback multiplier. |
| `markers` | `MFNode` | `[]` | Marker solids inserted/managed by controller. |
| `leftGroundReactionForceOffset` | `SFVec3f` | `0.28 0.28 0` | Left GRF offset. |
| `rightGroundReactionForceOffset` | `SFVec3f` | `-0.21 0.22 0` | Right GRF offset. |
| `bodyTransparency` | `SFFloat` | `1` | Body visualization transparency (`1` disables body mesh). |
| `bodyHeight` | `SFFloat` | `-1` | Body height override; negative uses C3D/default value. |
| `bodyOffset` | `SFFloat` | `0.25` | Vertical offset from `CenterOfMass` marker. |
| `window` | `SFString` | `"c3d_viewer_window"` | Robot window identifier. |
| `name` | `SFString` | `"c3d_viewer"` | Robot name. |

## Complete Object PROTO Catalog (projects/objects)

Total PROTO count: **310**

### advertising_board (1)

- AdvertisingBoard

### animals (8)

- Cat
- Cow
- Deer
- Dog
- Fox
- Horse
- Rabbit
- Sheep

### apartment_structure (9)

- Ceiling
- Door
- DoorKnob
- DoorLever
- GenericDoorAppearance
- Radiator
- Wall
- WallPlug
- Window

### backgrounds (2)

- TexturedBackground
- TexturedBackgroundLight

### balls (5)

- Ball
- FifaSoccerBall
- PingPongBall
- RobocupSoccerBall
- SoccerBall

### bathroom (4)

- BathroomSink
- Bathtube
- Toilet
- WashingMachine

### bedroom (2)

- Bed
- PhotoFrame

### buildings (46)

- Auditorium
- Barn
- BigGlassTower
- Building
- BuildingUnderConstruction
- BungalowStyleHouse
- Carwash
- Church
- CommercialBuilding
- ComposedHouse
- CyberboticsTower
- FastFoodRestaurant
- GasStation
- HollowBuilding
- Hotel
- HouseWithGarage
- LargeResidentialTower
- LargeResidentialTowerBase
- LargeResidentialTowerFloor
- LargeResidentialTowerRoof
- ModernHouse
- ModernSuburbanHouse
- MotelReception
- Museum
- OldResidentialBuilding
- RandomBuilding
- ResidentialBuilding
- ResidentialBuildingWithRoundFront
- ResidentialTower
- ResidentialTowerBase
- ResidentialTowerFloor
- ResidentialTowerRoof
- Silo
- SimpleBuilding
- SimpleTwoFloorsHouse
- SmallManor
- SmallResidentialBuilding
- SmallResidentialTower
- SmallResidentialTowerBase
- SmallResidentialTowerFloor
- StripBuilding
- SuburbanHouse
- TheThreeTowers
- UBuilding
- Warehouse
- Windmill

### cabinet (4)

- Cabinet
- CabinetDoor
- CabinetDrawer
- CabinetHandle

### chairs (4)

- Chair
- OfficeChair
- SimpleChair
- WoodenChair

### computers (5)

- ComputerMouse
- DesktopComputer
- Keyboard
- Laptop
- Monitor

### create_wall (1)

- CreateWall

### drinks (3)

- BeerBottle
- Can
- WaterBottle

### factory/containers (5)

- CardboardBox
- MetalStorageBox
- PlasticCrate
- PlasticFruitBox
- WoodenBox

### factory/conveyors (2)

- ConveyorBelt
- ConveyorPlatform

### factory/fire_extinguisher (1)

- FireExtinguisher

### factory/forklift (1)

- Forklift

### factory/gas_canister (1)

- GasCanister

### factory/manhole (1)

- SquareManhole

### factory/pallet (2)

- WoodenPallet
- WoodenPalletStack

### factory/pipes (3)

- LJoint
- PipeSection
- TJoint

### factory/tools (14)

- Bolt
- CapScrew
- ElectricalPlug
- EmergencyButton
- EyeScrew
- Hammer
- Nut
- PaintBucket
- PlatformCart
- ScrewHole
- Screwdriver
- SuctionCup
- Washer
- Wrench

### factory/valves (3)

- LargeValve
- LeverValve
- SmallValve

### floors (5)

- CircleArena
- Ditch
- Floor
- RectangleArena
- UnevenTerrain

### freight (2)

- IntermodalContainer
- IntermodalOfficeContainer

### fruits (3)

- Apple
- FruitBowl
- Orange

### garden (12)

- Barbecue
- DogHouse
- Gnome
- Pergolas
- PicketFence
- PicketFenceWithDoor
- Slide
- Swing
- SwingCouch
- TableWithUmbrella
- WateringCan
- Wheelbarrow

### gear (1)

- Gear

### geometries (4)

- Extrusion
- Rectangle
- TexturedBox
- TexturedParallelepiped

### hospital (6)

- Curtain
- DripStand
- EmergencyExitSign
- HandSanitizer
- HospitalBed
- MedicineBottle

### kitchen/breakfast (4)

- BiscuitBox
- CerealBox
- HoneyJar
- JamJar

### kitchen/components (3)

- HotPlate
- Sink
- Worktop

### kitchen/fridge (1)

- Fridge

### kitchen/oven (1)

- Oven

### kitchen/utensils (10)

- Carafe
- Cookware
- Fork
- Glass
- Knife
- Lid
- Plate
- Spoon
- Wineglass
- WoodenSpoon

### lego (2)

- LegoLargeMotor
- LegoWheel

### lights (5)

- CeilingLight
- CeilingSpotLight
- ConstructionLamp
- DoubleFluorescentLamp
- FloorLight

### living_room_furniture (3)

- Armchair
- Carpet
- Sofa

### mirror (1)

- Mirror

### obstacles (2)

- OilBarrel
- Ramp30deg

### paintings (2)

- LandscapePainting
- PortraitPainting

### panels (3)

- Panel
- PanelWithTubes
- PannelWithTubes

### plants (3)

- BunchOfSunFlowers
- FlowerPot
- PottedTree

### road (13)

- AddLaneRoadSegment
- AddLanesRoadSegment
- CrashBarrier
- Crossroad
- CurvedRoadSegment
- HelicoidalRoadSegment
- LaneSeparation
- Road
- RoadIntersection
- RoadLine
- RoadPillars
- Roundabout
- StraightRoadSegment

### robotstadium (2)

- RobotstadiumGoal
- RobotstadiumSoccerField

### rocks (1)

- Rock

### school_furniture (3)

- Blackboard
- Book
- Clock

### shapes (1)

- TexturedBoxShape

### solids (4)

- SolidBox
- SolidPipe
- SolidRoundedBox
- SolidTorus

### stairs (3)

- StraightStairs
- StraightStairsLanding
- StraightStairsRail

### street_furniture (33)

- Atm
- Bench
- BusStop
- BusStopBench
- BusStopMesh
- ClothRecyclingContainer
- DrinkingFountain
- ElectricalCabinet
- EmergencyPhone
- Fence
- FireHydrant
- Fountain
- GlassRecyclingContainer
- GuardShelter
- IceFreezerContainer
- Mailbox
- MetallicTrash
- NewsStand
- OldBench
- PhoneBooth
- PublicBin
- PublicToilet
- PublicToiletMesh
- SimpleBench
- SmallKiosk
- SnackStand
- StoneBench
- StoneFountain
- TrashBin
- TrashContainer
- UndergroundContainer
- UrbanFence
- WorkTrashContainer

### tables (3)

- Desk
- RoundTable
- Table

### telephone (2)

- OfficeTelephone
- Telephone

### television (1)

- Television

### toys (2)

- PaperBoat
- RubberDuck

### traffic (33)

- CautionPanel
- CautionSign
- ControlledStreetLight
- CrossRoadsTrafficLight
- DirectionPanel
- DivergentIndicator
- ExitPanel
- ExitSign
- GenericTrafficLight
- HighwayPole
- HighwaySign
- OrderPanel
- OrderSign
- ParkingLines
- ParkingMeter
- PedestrianCrossing
- Pole
- RectangularPanel
- SignPole
- SpeedLimitPanel
- SpeedLimitSign
- StopPanel
- StopSign
- StreetLight
- TrafficCone
- TrafficLight
- TrafficLightArrowLampGeometry
- TrafficLightBigPole
- TrafficLightHorizontal
- TrafficLightStandardLampGeometry
- WorkBarrier
- YieldPanel
- YieldSign

### trees (9)

- BigSassafras
- Cypress
- Forest
- Oak
- PalmTree
- Pine
- Sassafras
- SimpleTree
- Tree

## Pre-built Environment and World Inventory

### Humans Sample Worlds

- `projects/humans/skin_animated_humans/worlds/skin_animated_humans.wbt`: animated `CharacterSkin` + BVH retargeting reference world.
- `projects/humans/pedestrian/worlds/pedestrian.wbt`: walking `Pedestrian` trajectory/controller reference world.
- `projects/humans/c3d/worlds/c3d_viewer.wbt`: C3D marker/GRF playback reference world.

### Basic Arena Templates

- `CircleArena` (`projects/objects/floors/protos/CircleArena.proto`): circular bounded test arena.
- `RectangleArena` (`projects/objects/floors/protos/RectangleArena.proto`): rectangular bounded test arena.

### Indoor Environments

- `projects/samples/environments/indoor/worlds/apartment.wbt`: furnished apartment layout.
- `projects/samples/environments/indoor/worlds/complete_apartment.wbt`: richer apartment composition with denser assets.
- `projects/samples/environments/indoor/worlds/kitchen.wbt`: kitchen-focused interior environment.
- `projects/samples/environments/indoor/worlds/break_room.wbt`: office break-room style interior.

### Industrial and Factory Environments

- `projects/samples/environments/factory/worlds/factory.wbt`: complete factory floor environment.
- `projects/samples/environments/factory/worlds/hall.wbt`: industrial hall/open factory interior.

### Outdoor, City, Village, and Road/Highway Environments

- `projects/vehicles/worlds/city.wbt`: daytime urban city driving environment.
- `projects/vehicles/worlds/city_night.wbt`: night urban environment.
- `projects/vehicles/worlds/city_traffic.wbt`: city scenario with traffic context.
- `projects/vehicles/worlds/village.wbt`: village road environment.
- `projects/vehicles/worlds/village_center.wbt`: denser village center context.
- `projects/vehicles/worlds/village_realistic.wbt`: realistic village variant.
- `projects/vehicles/worlds/village_winter.wbt`: winter village environment.
- `projects/vehicles/worlds/highway.wbt`: highway environment.
- `projects/vehicles/worlds/highway_overtake.wbt`: highway overtaking scenario.
- `projects/samples/robotbenchmark/highway_driving/worlds/highway_driving.wbt`: benchmark highway driving setup.

### Sports and RoboCup Environments

- `projects/objects/robotstadium/protos/RobotstadiumSoccerField.proto`: RoboCup-style soccer field PROTO.
- `projects/objects/robotstadium/protos/RobotstadiumGoal.proto`: matching soccer goal PROTO.
- `projects/samples/contests/robocup/worlds/adult.wbt`: RoboCup adult-size field world.
- `projects/samples/contests/robocup/worlds/kid.wbt`: RoboCup kid-size field world.

### Home and Service-Robot Contest Environments

- `projects/samples/contests/rockin/worlds/home.wbt`: domestic home scenario.
- `projects/samples/contests/rockin/worlds/work.wbt`: work/industrial service scenario.

## TexturedBackground and TexturedBackgroundLight Options

### TexturedBackground

**Source:** `projects/objects/backgrounds/protos/TexturedBackground.proto`

Fields:
- `texture` (`SFString` enum, default `mountains`)
- `luminosity` (`SFFloat`, default `1`)
- `skybox` (`SFBool`, default `TRUE`)
- `skyColor` (`MFColor`, default `[0 0 0]`)

Supported HDR textures:
- `dusk`
- `empty_office`
- `entrance_hall`
- `factory`
- `mars`
- `noon_building_overcast`
- `noon_cloudy_countryside`
- `noon_park_empty`
- `mountains`
- `music_hall`
- `stadium`
- `stadium_dry`

Deprecated textures:
- `dawn_cloudy_empty`
- `morning_cloudy_empty`
- `noon_cloudy_empty`
- `noon_cloudy_mountains`
- `noon_stormy_empty`
- `noon_sunny_empty`
- `noon_sunny_garden`
- `twilight_cloudy_empty`

### TexturedBackgroundLight

**Source:** `projects/objects/backgrounds/protos/TexturedBackgroundLight.proto`

Fields:
- `texture` (`SFString` enum, default `mountains`) - keep equal to `TexturedBackground.texture`
- `luminosity` (`SFFloat`, default `1`) - intensity multiplier
- `castShadows` (`SFBool`, default `TRUE`)

Supported/deprecated texture identifiers follow the same set as `TexturedBackground`.

### Recommended Pairing Rule

Always pair these nodes with the same `texture` value:

```vrml
TexturedBackground {
  texture "mountains"
}
TexturedBackgroundLight {
  texture "mountains"
}
```
