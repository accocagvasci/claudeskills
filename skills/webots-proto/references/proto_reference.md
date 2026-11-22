# Webots PROTO Reference

Focused reference for Webots PROTO authoring and usage. This document covers PROTO syntax, field types, procedural template behavior, discovery/search rules, and common reusable PROTO nodes.

## 1) PROTO Syntax

Canonical form:

```vrml
PROTO ProtoName [
  field <FieldType> <fieldName> <defaultValue>
  field <FieldType> <fieldName> <defaultValue>
]
{
  <rootNode>
}
```

Minimal example:

```vrml
PROTO ColoredBox [
  field SFVec3f size 1 1 1
  field SFColor color 0.8 0.2 0.2
]
{
  Solid {
    children [
      Shape {
        appearance PBRAppearance { baseColor IS color }
        geometry Box { size IS size }
      }
    ]
  }
}
```

Instantiation:

```vrml
ColoredBox {
  size 0.5 0.3 0.2
  color 0.2 0.5 0.9
}
```

## 2) PROTO Keywords and Constructs

Core keywords and constructs used in `.proto` files:
- `PROTO` - defines a reusable node template.
- `field` - declares one interface parameter.
- `IS` - binds an interface field to an internal node field.
- `hiddenField` / hidden-field conventions - marks interface entries intended for internal use (project/style dependent), often paired with underscore-prefixed names.
- `EXTERNPROTO` - references a PROTO definition provided by another file or URL.
- `IMPORTABLE` (when used by project conventions) - marks PROTO compatibility/availability behavior in specific workflows.
- `DEF` and `USE` - standard VRML naming/reuse inside the instantiated node tree.
- `%< ... >%` - JavaScript statement block for procedural generation.
- `%<= ... >%` - JavaScript expression output block for procedural generation.

Notes:
- `IS` requires compatible field types.
- `DEF` names in a PROTO instance are local to that instance.
- `EXTERNPROTO` paths can be local or remote URLs.

## 3) Field Types and Value Rules

### Single-value field types

- `SFBool`
  - Values: `TRUE` or `FALSE`.

- `SFInt32`
  - Values: signed 32-bit integer (`-2147483648` to `2147483647`).

- `SFFloat`
  - Values: finite floating-point number.
  - Project-level constraints (length, mass, radius, speed, etc.) are node-specific and should be validated by PROTO design.

- `SFString`
  - Values: quoted string.
  - Escape special characters per VRML string rules.

- `SFVec2f`
  - Values: two floats (`x y`).

- `SFVec3f`
  - Values: three floats (`x y z`).

- `SFRotation`
  - Values: axis-angle (`x y z angle`), where axis must be non-zero.
  - Axis is interpreted as a direction vector; normalization is expected by convention.
  - `angle` in radians.

- `SFColor`
  - Values: three floats (`r g b`) typically in `[0.0, 1.0]`.

- `SFNode`
  - Values: one node or `NULL`.

### Multi-value field types

- `MFBool` - list of `SFBool`.
- `MFInt32` - list of `SFInt32`.
- `MFFloat` - list of `SFFloat`.
- `MFString` - list of `SFString`.
- `MFVec2f` - list of `SFVec2f`.
- `MFVec3f` - list of `SFVec3f`.
- `MFRotation` - list of `SFRotation`.
- `MFColor` - list of `SFColor`.
- `MFNode` - list of nodes.

MF values are provided as bracketed arrays in VRML syntax:

```vrml
field MFVec3f points [ 0 0 0, 1 0 0, 1 1 0 ]
field MFNode extraChildren [
  Shape { geometry Sphere { radius 0.05 } }
  Solid { }
]
```

## 4) IS Binding Rules

Use `IS` to map interface fields into internal node fields:

```vrml
translation IS translation
baseColor IS bodyColor
```

Rules:
- Bind compatible types only.
- Bind one interface field to multiple internal targets when intentional.
- Avoid binding hidden/internal fields into external-facing semantics.

Typical errors:
- Type mismatch between interface and target field.
- Binding to non-existent target field name.
- Attempting to bind in syntactically invalid locations.

## 5) Procedural PROTO JavaScript

Procedural PROTOs generate node content through JavaScript template sections.

Template delimiters:
- `%< statement(s) >%` - execute JS statements.
- `%<= expression >%` - emit expression result as text into generated node source.

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
    children [
      Shape {
        geometry Box {
          size %<= fields.segmentWidth.value >% 0.1 1.0
        }
      }
    ]
  }
  %< } >%
}
```

### `fields` object API

Interface values are available through a generated `fields` object:
- `fields.<name>` - metadata container for one declared PROTO field.
- `fields.<name>.value` - current resolved field value used during generation.

Example:

```javascript
const n = fields.segments.value;
const w = fields.segmentWidth.value;
```

### Helper usage in procedural blocks

Available helper surface is JavaScript itself plus values exposed by Webots template context:
- Standard JavaScript language features (`if`, `for`, `while`, functions, template literals).
- Standard global objects (`Math`, `Number`, `String`, `Boolean`, `Array`, `JSON`).
- Field access via `fields` object.

Practical helper patterns:

```javascript
const n = Math.max(1, fields.segments.value);
const w = Math.max(0.01, fields.segmentWidth.value);
const x = i * w;
```

Guidelines:
- Emit only valid VRML node text.
- Clamp dimensions/counts before generation.
- Keep procedural output deterministic for identical inputs.

## 6) Scoping and Encapsulation

PROTO scoping rules:
- Interface fields are the public API.
- Internal fields and internal node structure are encapsulated.
- `DEF` names are local per PROTO instance (no global collision across instances).
- Nested PROTOs do not gain implicit access to outer PROTO fields.

To pass outer values to nested PROTO internals, explicitly expose fields and bind with `IS`.

## 7) Hidden Fields

Hidden/internal interface entries are used for implementation details:
- Common practice: underscore-prefixed names (for example `_internalScale`).
- May be declared using project conventions with hidden field declarations.
- Intended to reduce Scene Tree clutter while preserving internal control.

Operational notes:
- Hidden fields are typically not exposed for routine editing in UI flows.
- Hidden values can still be observed or manipulated in advanced workflows (for example Supervisor-level automation), depending on node and API behavior.

## 8) PROTO File Search Paths

Webots resolves PROTOs through ordered search locations. Effective availability is determined by file placement and declared dependencies.

Typical path sources:
- World-local/project-local `protos/` directories.
- Referenced package/library paths used by the current project.
- Built-in Webots PROTO library paths.
- `EXTERNPROTO` explicit paths (local files or URLs).

Recommended project structure:

```text
my_project/
  worlds/
    demo.wbt
  protos/
    MyRobot.proto
    props/
      MyCrate.proto
```

Resolution guidance:
- Keep custom PROTOs in `protos/` near project worlds.
- Prefer explicit `EXTERNPROTO` declarations for cross-folder dependencies.
- Use stable relative paths for versioned repositories.

## 9) Remote PROTO Loading

Use `EXTERNPROTO` with URL strings to load remote PROTO definitions:

```vrml
EXTERNPROTO "https://example.com/protos/MyRobot.proto"
```

Common remote formats:
- HTTPS absolute URL to `.proto` file.
- Webots cloud-hosted resources with HTTPS URL form.

Practical rules:
- Use immutable/versioned URLs for reproducibility.
- Keep dependency count small to reduce load and breakage risk.
- Ensure remote assets stay publicly reachable for shared projects.

## 10) Common Built-in PROTOs

Examples frequently used in Webots projects:
- `RectangleArena` - rectangular bounded arena.
- `WoodenBox` - simple box obstacle/prop.
- `E-puck` - differential-drive educational robot.
- `Thymio2` - educational mobile robot.
- `Pioneer3dx` - mobile robot platform.
- `Khepera` family variants.
- `Crazyflie` (when available in installed package set).

Availability depends on installed Webots version and package set.

## 11) Validation Checklist

Before shipping a PROTO:
- Interface fields have clear names, defaults, units, and expected ranges.
- All `IS` mappings are type-compatible.
- Procedural blocks generate valid node syntax for edge values.
- Hidden/internal fields are not part of public-facing API contracts.
- PROTO resolves correctly from project `protos/` and any `EXTERNPROTO` dependencies.
