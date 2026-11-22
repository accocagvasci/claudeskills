---
name: webots-proto
description: "Use this skill when creating, modifying, or using PROTO nodes in Webots - reusable parameterized node templates for robots, objects, or environments. Covers PROTO definition syntax, field types, IS keyword, procedural PROTOs with JavaScript, scoping rules, and design guidelines. Triggers on: webots proto, .proto file, PROTO definition, IS keyword, parameterized node, reusable robot, procedural proto, custom node."
---

# Webots PROTO Skill

Build reusable, parameterized node templates with Webots PROTO files.

Use this skill to define `.proto` assets, expose stable interfaces, instantiate custom nodes in worlds, and generate procedural node content with JavaScript templates.

## Scope

Cover PROTO authoring and usage only:
- PROTO definition syntax and interface design
- Field typing and `IS` bindings
- Procedural PROTO generation with JavaScript template blocks
- PROTO encapsulation, scoping, hidden fields, search paths, and remote loading

Do not cover:
- Basic world-building workflows (handled by `webots-world-building`)
- Controller programming/runtime robot behavior logic

## Core Concept

Treat PROTOs as reusable templates (class-like wrappers) around scene tree nodes:
- Define once in a `.proto` file
- Parameterize via interface fields
- Instantiate many times in worlds or inside other PROTOs
- Keep internals encapsulated while exposing a small public interface

Store project PROTO files in `protos/` so Webots can discover them through standard search paths.

Webots also provides many built-in PROTOs for robots, objects, and environments that can be reused directly.

## PROTO Definition Syntax

Use this structure:

```vrml
PROTO Name [
  field <Type> <fieldName> <defaultValue>
  field <Type> <fieldName> <defaultValue>
]
{
  # Node tree
}
```

Reference example:

```vrml
PROTO MyRobot [
  field SFVec3f    translation  0 0 0
  field SFRotation rotation     0 0 1 0
  field SFString   name         "my_robot"
  field SFString   controller   "my_controller"
  field SFFloat    wheelRadius  0.05
  field SFColor    bodyColor    0.8 0.1 0.1
]
{
  Robot {
    translation IS translation
    rotation IS rotation
    name IS name
    controller IS controller
    children [
      Shape {
        appearance PBRAppearance {
          baseColor IS bodyColor
        }
        geometry Cylinder {
          radius 0.1
          height 0.05
        }
      }
      HingeJoint {
        jointParameters HingeJointParameters {
          axis 0 1 0
        }
        device [
          RotationalMotor { name "left_motor" }
        ]
        endPoint Solid {
          children [
            Shape {
              geometry Cylinder {
                radius IS wheelRadius
                height 0.02
              }
            }
          ]
        }
      }
    ]
  }
}
```

## Field Types

Support all standard SF/MF VRML-style field types in PROTO interfaces:

- Single-value fields: `SFBool`, `SFInt32`, `SFFloat`, `SFString`, `SFVec2f`, `SFVec3f`, `SFRotation`, `SFColor`, `SFNode`
- Multi-value fields: `MFBool`, `MFInt32`, `MFFloat`, `MFString`, `MFVec2f`, `MFVec3f`, `MFRotation`, `MFColor`, `MFNode`

Design notes:
- Use `SFNode`/`MFNode` for composition hooks (slot-like extensibility)
- Use `SFString` for names/controllers and file-like metadata fields
- Use `SFColor` for visual parameters and `SFRotation` for orientation APIs

## IS Keyword

Bind external interface fields to internal node fields with `IS`:

```vrml
translation IS translation
```

Rules:
- Bind only compatible field types
- Bind at any depth in the internal node tree
- Reuse one exposed field in multiple internal locations when a shared parameter is desired
- Avoid exposing internal-only implementation details; keep those as constants or hidden fields

## PROTO Instantiation

Instantiate in a world or parent PROTO exactly like built-in nodes:

```vrml
MyRobot {
  translation 1 0 0
  bodyColor 0 0.5 1
  wheelRadius 0.08
}
```

Override only relevant fields; defaults provide the rest.

## Procedural PROTOs (JavaScript)

Use template blocks to generate node text dynamically:

- `%< ... >%` for control flow or statements
- `%<= ... >%` for expression output
- Access interface values via `fields.<name>.value`

Example:

```vrml
PROTO ProceduralWall [
  field SFInt32 segments 5
  field SFFloat segmentWidth 1.0
]
{
  %< for (let i = 0; i < fields.segments.value; i++) { >%
  Solid {
    translation %<= i * fields.segmentWidth.value >% 0 0
    children [ Shape { geometry Box { size %<= fields.segmentWidth.value >% 0.1 1.0 } } ]
  }
  %< } >%
}
```

Procedural design guidance:
- Keep generated output deterministic for identical field values
- Clamp or validate dimensions before emitting geometry
- Prefer simple loops/conditionals over deeply nested generated logic
- Emit valid VRML node text only

## Scene Tree Usage and Discovery

Use PROTO nodes in the Scene Tree through Add-node workflows:
- Add button -> PROTO nodes list
- Search by node name when many PROTOs are available
- Instantiate built-in or project-local PROTOs

Support remote PROTO usage from URL-based declarations (including `webots.cloud` hosted assets) when portability or shared distribution is required.

## Scoping and Encapsulation Rules

Apply strict encapsulation semantics:
- PROTO interface fields are the only public contract
- Internal fields/nodes are not directly accessible outside the PROTO instance
- `DEF` names declared inside a PROTO stay local to that PROTO instance
- Nested PROTO definitions do not get implicit access to outer PROTO fields

Pass values explicitly through interfaces when nesting reusable components.

## Hidden Fields

Use hidden/internal fields for implementation state:
- Prefix internal fields with `_` or mark as hidden according to PROTO conventions
- Keep them out of normal Scene Tree editing surfaces
- Retain access from Supervisor/API workflows when internal control is needed

Use hidden fields for tuning, cache/state selectors, or compatibility fields that should not clutter public interfaces.

## Design Guidelines

Build robust reusable PROTOs with these constraints:
- Expose minimal interfaces focused on user intent
- Provide safe defaults that produce valid geometry and behavior
- Document each exposed field (purpose, unit, valid range)
- Prefer composition (`SFNode`/`MFNode` hooks) over rigid deep hierarchies
- Keep naming stable to preserve backward compatibility
- Test multiple parameter combinations, including edge values

## Practical Workflow

Follow this repeatable flow:
1. Define the public interface first (field names, types, defaults, ranges).
2. Implement static internal node tree and bind exposed fields via `IS`.
3. Add procedural generation only where static structures become repetitive.
4. Instantiate in a test world with varied parameters.
5. Verify scoping behavior, hidden-field behavior, and remote/local loading.
6. Freeze interface names and document expected value ranges.

## Troubleshooting Checklist

When a PROTO fails to behave as expected, check:
- Field type mismatches in `IS` links
- Invalid default values (e.g., malformed rotation/color)
- Generated procedural text that is not valid node syntax
- Name collisions or assumptions about global `DEF` visibility
- Missing PROTO search-path registration or incorrect remote URL

For field-level syntax, keyword reference, search-path rules, and procedural details, use `references/proto_reference.md`.
