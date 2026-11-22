# Webots World Building Reference

Reference for world-building node fields, geometry options, texture/model format support, and contact/damping configuration. Scope is limited to scene construction and physics/material setup in `.wbt` worlds.

## Coordinate System And Syntax

- Coordinate frame: ENU (`X=East`, `Y=North`, `Z=Up`) in modern Webots releases.
- World files: VRML97-derived syntax with Webots extensions.

## Node Field Reference

### Solid

Common world-building fields:

| Field | Type | Purpose |
|---|---|---|
| `translation` | `SFVec3f` | Position in parent frame |
| `rotation` | `SFRotation` | Axis-angle orientation |
| `scale` | `SFVec3f` | Local non-uniform scaling |
| `children` | `MFNode` | Visual/structural child nodes |
| `name` | `SFString` | Node label |
| `model` | `SFString` | Model metadata label |
| `description` | `SFString` | Optional semantic description |
| `contactMaterial` | `SFString` | Material tag for contact rules |
| `immersionProperties` | `MFNode` | Medium interaction settings |
| `boundingObject` | `SFNode` | Collision geometry node |
| `physics` | `SFNode` | `Physics` node for dynamics |
| `radarCrossSection` | `SFFloat` | Radar signature value |
| `recognitionColors` | `MFColor` | Vision-recognition colors |
| `linearVelocity` | `SFVec3f` | Initial/forced linear velocity |
| `angularVelocity` | `SFVec3f` | Initial/forced angular velocity |
| `locked` | `SFBool` | Keep solid fixed in simulation |
| `translationStep` | `SFFloat` | Editor translation snapping |
| `rotationStep` | `SFFloat` | Editor rotation snapping |

### Shape

| Field | Type | Purpose |
|---|---|---|
| `appearance` | `SFNode` | `Appearance` or `PBRAppearance` |
| `geometry` | `SFNode` | Geometry node (`Box`, `Mesh`, etc.) |
| `castShadows` | `SFBool` | Shadow casting control |
| `isPickable` | `SFBool` | Picking/raycast availability |

### Appearance (legacy)

| Field | Type | Purpose |
|---|---|---|
| `material` | `SFNode` | `Material` properties |
| `texture` | `SFNode` | `ImageTexture`/texture node |
| `textureTransform` | `SFNode` | `TextureTransform` UV transform |
| `name` | `SFString` | Label |

### PBRAppearance (preferred)

| Field | Type | Purpose |
|---|---|---|
| `baseColor` | `SFColor` | Albedo fallback color |
| `baseColorMap` | `SFNode` | Albedo texture |
| `transparency` | `SFFloat` | Transparency amount |
| `roughness` | `SFFloat` | Surface micro-roughness |
| `roughnessMap` | `SFNode` | Roughness texture |
| `metalness` | `SFFloat` | Metallic factor |
| `metalnessMap` | `SFNode` | Metalness texture |
| `IBLStrength` | `SFFloat` | Image-based lighting influence |
| `normalMap` | `SFNode` | Normal map texture |
| `normalMapFactor` | `SFFloat` | Normal map intensity |
| `occlusionMap` | `SFNode` | Ambient occlusion texture |
| `occlusionMapStrength` | `SFFloat` | AO texture influence |
| `emissiveColor` | `SFColor` | Emissive fallback color |
| `emissiveColorMap` | `SFNode` | Emissive texture |
| `emissiveIntensity` | `SFFloat` | Emission strength |
| `textureTransform` | `SFNode` | UV transform |
| `name` | `SFString` | Label |

### Material (legacy)

| Field | Type | Purpose |
|---|---|---|
| `ambientIntensity` | `SFFloat` | Ambient contribution |
| `diffuseColor` | `SFColor` | Diffuse reflectance |
| `specularColor` | `SFColor` | Specular highlight color |
| `emissiveColor` | `SFColor` | Self-illumination color |
| `shininess` | `SFFloat` | Specular lobe sharpness |
| `transparency` | `SFFloat` | Alpha-style transparency |

## Geometry Node Field Reference

### Box

| Field | Type | Purpose |
|---|---|---|
| `size` | `SFVec3f` | Dimensions along X/Y/Z |

### Sphere

| Field | Type | Purpose |
|---|---|---|
| `radius` | `SFFloat` | Sphere radius |
| `subdivision` | `SFInt32` | Tessellation detail |

### Cylinder

| Field | Type | Purpose |
|---|---|---|
| `radius` | `SFFloat` | Radius |
| `height` | `SFFloat` | Height |
| `bottom` | `SFBool` | Bottom cap enable |
| `side` | `SFBool` | Lateral surface enable |
| `top` | `SFBool` | Top cap enable |
| `subdivision` | `SFInt32` | Radial tessellation |

### Capsule

| Field | Type | Purpose |
|---|---|---|
| `radius` | `SFFloat` | Capsule radius |
| `height` | `SFFloat` | Cylindrical section height |
| `subdivision` | `SFInt32` | Overall tessellation |

### Cone

| Field | Type | Purpose |
|---|---|---|
| `bottomRadius` | `SFFloat` | Base radius |
| `height` | `SFFloat` | Cone height |
| `bottom` | `SFBool` | Base cap enable |
| `side` | `SFBool` | Side surface enable |
| `subdivision` | `SFInt32` | Circular tessellation |

### Plane

| Field | Type | Purpose |
|---|---|---|
| `size` | `SFVec2f` | Extent in local plane axes |

### ElevationGrid

| Field | Type | Purpose |
|---|---|---|
| `xDimension` | `SFInt32` | Sample count on X |
| `xSpacing` | `SFFloat` | Cell size along X |
| `yDimension` | `SFInt32` | Sample count on Y |
| `ySpacing` | `SFFloat` | Cell size along Y |
| `height` | `MFFloat` | Height values per grid sample |
| `thickness` | `SFFloat` | Bottom extrusion thickness |

### IndexedFaceSet

| Field | Type | Purpose |
|---|---|---|
| `coord` | `SFNode` | `Coordinate` points |
| `coordIndex` | `MFInt32` | Polygon indices (`-1` separators) |
| `normal` | `SFNode` | `Normal` vectors |
| `normalIndex` | `MFInt32` | Optional normal indices |
| `texCoord` | `SFNode` | `TextureCoordinate` values |
| `texCoordIndex` | `MFInt32` | Optional UV indices |
| `color` | `SFNode` | Vertex/face color set |
| `colorIndex` | `MFInt32` | Color indices |
| `ccw` | `SFBool` | Vertex winding convention |
| `convex` | `SFBool` | Convex-face optimization hint |
| `creaseAngle` | `SFFloat` | Normal smoothing angle |
| `normalPerVertex` | `SFBool` | Per-vertex/per-face normals |
| `colorPerVertex` | `SFBool` | Per-vertex/per-face colors |
| `solid` | `SFBool` | Back-face culling semantic |

### Mesh

| Field | Type | Purpose |
|---|---|---|
| `url` | `MFString` | Mesh file path list |
| `ccw` | `SFBool` | Winding interpretation |

### CadShape

| Field | Type | Purpose |
|---|---|---|
| `url` | `MFString` | CAD file path list |
| `ccw` | `SFBool` | Winding interpretation |

## Texture And UV Nodes

### ImageTexture

| Field | Type | Purpose |
|---|---|---|
| `url` | `MFString` | Texture filename list |
| `repeatS` | `SFBool` | Horizontal tiling |
| `repeatT` | `SFBool` | Vertical tiling |
| `filtering` | `SFInt32` | Filtering mode |

### TextureTransform

| Field | Type | Purpose |
|---|---|---|
| `center` | `SFVec2f` | Transform pivot |
| `rotation` | `SFFloat` | UV rotation |
| `scale` | `SFVec2f` | UV scale |
| `translation` | `SFVec2f` | UV offset |

## Physics-Related Reference

### Physics

| Field | Type | Purpose |
|---|---|---|
| `density` | `SFFloat` | Density (use `-1` with explicit `mass`) |
| `mass` | `SFFloat` | Total mass |
| `centerOfMass` | `MFVec3f` | COM offsets |
| `inertiaMatrix` | `MFFloat` | Inertia tensor entries |
| `damping` | `SFNode` | `Damping` node |

### Damping field reference

`Damping` node fields:

| Field | Type | Purpose |
|---|---|---|
| `linear` | `SFFloat` | Linear velocity damping |
| `angular` | `SFFloat` | Angular velocity damping |

### ContactProperties field reference

`ContactProperties` node fields (used in `WorldInfo.contactProperties`):

| Field | Type | Purpose |
|---|---|---|
| `material1` | `SFString` | First contact material tag |
| `material2` | `SFString` | Second contact material tag |
| `coulombFriction` | `MFFloat` | Tangential friction coefficients |
| `frictionRotation` | `SFRotation` | Friction direction frame |
| `rollingFriction` | `SFFloat` | Rolling resistance |
| `bounce` | `SFFloat` | Restitution factor |
| `bounceVelocity` | `SFFloat` | Min speed for restitution |
| `forceDependentSlip` | `MFFloat` | Slip scaling under load |
| `softERP` | `SFFloat` | Error reduction parameter |
| `softCFM` | `SFFloat` | Constraint force mixing |
| `slip` | `MFFloat` | Slip coefficients |

## Supported Texture Formats

Commonly supported raster and HDR formats for texture workflows:

- `.jpg`, `.jpeg`
- `.png`
- `.bmp`
- `.tga`
- `.hdr`
- `.exr`

Prefer power-of-two texture dimensions for predictable mipmapping/performance on large scenes.

## Supported 3D Model Import Formats

### Mesh node import formats

- `.obj`
- `.stl`
- `.dae` (COLLADA)
- `.fbx`

### CadShape CAD-oriented formats

- `.step`, `.stp`
- `.iges`, `.igs`
- `.brep`, `.brp`
- `.sat`

Validate scale and up-axis on import, then wrap with simplified `boundingObject` for real-time collision performance.
