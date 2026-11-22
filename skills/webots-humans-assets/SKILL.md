---
name: webots-humans-assets
description: "Use this skill when adding human models, pedestrians, animated characters, or environment assets (furniture, buildings, nature, vehicles) to a Webots simulation. Covers CharacterSkin for animated humans, Pedestrian for walking models, BVH animation playback, C3D motion capture, and the complete object/environment asset library. Triggers on: webots human, pedestrian, CharacterSkin, BVH animation, furniture, environment, objects, assets, indoor, outdoor, buildings."
---

# Webots Humans and Assets

## Purpose

Use this skill to build human-centric and environment-rich Webots scenes quickly and correctly:
- Add animated human meshes through `CharacterSkin` and BVH retargeting.
- Add walking rigid humans through `Pedestrian` and trajectory arguments.
- Load biomechanics motion capture through `C3dViewer`.
- Populate worlds with the complete `projects/objects` PROTO library.
- Start from pre-built environment worlds when faster than assembling from scratch.

Use `references/humans_assets_reference.md` as the authoritative field/API catalog.

## Trigger Conditions

Activate when requests include terms such as:
- webots human, animated human, CharacterSkin
- pedestrian, walking person, crowd
- BVH animation, motion retargeting
- C3D, motion capture, markers, ground reaction force
- environment assets, furniture, indoor, outdoor, buildings, street furniture, traffic, static vehicles

## Boundaries

Keep this skill focused on humans and environment assets:
- Do not explain `Skin` node internals beyond practical mapping requirements.
- Do not cover robot catalogs or robot model selection.
- Defer low-level physics node internals to `webots-physics`.

## Human Model Selection Workflow

1. Choose the human representation:
   - Use `CharacterSkin` for mesh-based, bone-driven animation from BVH.
   - Use `Pedestrian` for lightweight scripted walking with path/speed args.
   - Use `C3dViewer` for marker-based biomechanics playback and analysis.
2. Add the node, set placement (`translation`, `rotation`), then configure behavior fields.
3. Validate scale/orientation immediately in simulation before scene duplication.

## CharacterSkin (Animated Human Mesh)

Use `CharacterSkin` when BVH animation or pose retargeting is required.

```vrml
CharacterSkin {
  translation 0 0 0
  rotation 0 0 1 0
  scale 1 1 1
  name "skin"
  model "Sandra"    # Options: "Anthony", "Robert", "Sandra", "Sophia"
  castShadows FALSE
}
```

Apply these model choices:
- `Anthony`: boy
- `Robert`: man
- `Sandra`: woman
- `Sophia`: girl

Practical notes:
- Treat `CharacterSkin` as a `Skin`-derived PROTO with predefined FBX mesh and PBR textures.
- Keep character scale close to `1 1 1`; adjust BVH translation scale instead of nonuniform body scaling when possible.
- Keep one animation controller instance per animated character to simplify joint mapping.

## BVH Playback and Retargeting Flow

Follow this sequence for robust playback:
1. Load BVH data (`wbu_bvh_read_file`).
2. Set translation scaling (`wbu_bvh_set_scale`) for meter alignment.
3. Build per-joint mapping between BVH joint indices and Webots bone identifiers.
4. Set T-pose alignment for each mapped joint (`wbu_bvh_set_model_t_pose`) using axis-angle data.
5. Step BVH every control loop (`wbu_bvh_step`), read joint rotations and root translation, then apply to the skin/bones.
6. Cleanup on exit (`wbu_bvh_cleanup`).

```c
// Load BVH file
WbBvhDataRef motion = wbu_bvh_read_file("animation.bvh");
wbu_bvh_set_scale(motion, 0.01);  // scale translation data

// Set T-pose mapping for each joint
wbu_bvh_set_model_t_pose(motion, axisAngle, jointId, global);

// Play animation
while (wb_robot_step(timestep) != -1) {
    wbu_bvh_step(motion);
    for (int i = 0; i < wbu_bvh_get_joint_count(motion); i++) {
        double *rotation = wbu_bvh_get_joint_rotation(motion, i);
        // Apply rotation to Skin bone
    }
}
wbu_bvh_cleanup(motion);
```

## Pedestrian (Rigid Walking Human)

Use `Pedestrian` for scripted walk cycles with simple scene integration.

```vrml
Pedestrian {
  translation 0 0 1.27
  rotation 0 0 1 0
  name "pedestrian"
  controller "pedestrian"
  controllerArgs ["--speed=1.5", "--trajectory=[2 0, 0 2, -2 0]"]
  shirtColor 0.25 0.55 0.20
  pantsColor 0.24 0.25 0.5
  shoesColor 0.28 0.15 0.06
  skinColor 1.0 0.75 0.7
  enableBoundingObject FALSE
}
```

Use this model behavior correctly:
- 13 passive joints exist (3 per arm, 3 per leg, 1 head).
- Walking gait logic is provided by the Python `pedestrian` controller.
- `controllerArgs` typically include:
  - `--trajectory=[x1 y1, x2 y2, ...]`
  - `--speed=<m/s>`
  - `--step=<controller step ms>`
- Attach props through `leftHandSlot` and `rightHandSlot`.

## C3D Motion Capture Viewer

Use `C3dViewer` to visualize biomechanics C3D files:
- Marker trajectories are rendered as spheres.
- Ground reaction force offsets are configurable.
- Playback speed and body visualization settings are configurable.
- The robot window (`c3d_viewer_window`) exposes runtime controls.

## Environment Asset Placement Workflow

Use built-in PROTO insertion for all object assets:

```text
Add button > PROTO nodes (Webots Projects) > objects > [category] > [asset]
```

Use this category-first strategy:
1. Place structural envelopes first (floors, walls, roads, buildings).
2. Place major furniture/infrastructure assets.
3. Add detail assets (street furniture, tools, props, vegetation).
4. Add background/static vehicles last to tune density and readability.

## Asset Families to Prioritize

Use the object library in these practical groups:
- Furniture and interiors: tables, chairs, desks, sofas, beds, cabinets, kitchen and bathroom fixtures.
- Infrastructure: buildings, doors/windows, stairs, roads, intersections, street equipment.
- Industrial/factory: conveyors, pallets, crates, valves, tools, canisters.
- Nature: trees, plants, rocks, and static animals.
- Street furniture: benches, bus stops, bins, hydrants, kiosks, fences.
- Static/background vehicles and road context assets.

## Pre-built Environments and Templates

Start from pre-built worlds when layout speed matters:
- Basic arenas: `RectangleArena`, `CircleArena` PROTO floors.
- Indoor environments: apartment/kitchen/break-room style worlds.
- Outdoor environments: city, village, road/highway worlds.
- Sports: RoboCup-style soccer field and match worlds.
- Factory floors and industrial halls.

Use the detailed world inventory in `references/humans_assets_reference.md`.

## Validation Checklist

Before finalizing scene composition:
1. Confirm all humans stand on contact surfaces without penetration.
2. Confirm animation scale and heading match ENU world axes.
3. Confirm pedestrian trajectory points remain navigable around obstacles.
4. Confirm static asset density preserves robot navigation corridors.
5. Confirm `TexturedBackground` and `TexturedBackgroundLight` textures match.
6. Confirm simulation remains real-time capable after asset expansion.

## Reference File

Load `references/humans_assets_reference.md` for:
- complete `CharacterSkin`, `Pedestrian`, and `C3dViewer` field references,
- full BVH utility C API,
- complete categorized object PROTO catalog,
- pre-built world environment index,
- textured background/light option matrix.
