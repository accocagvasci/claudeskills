---
name: webots-web-deployment
description: "Use this skill when deploying Webots simulations to the web, creating web animations, streaming simulations, or using webots.cloud. Covers W3D scene export, web animation recording, web streaming setup, Docker deployment, session/simulation server configuration. Triggers on: webots web, webots cloud, web animation, web streaming, W3D, web scene, docker webots, remote simulation, webots.cloud."
---

# Webots Web Deployment

Use this skill to deploy Webots projects to browser-based workflows through static scene export, animation export, live streaming, and cloud deployment.

## Scope and boundaries

- Focus on web deployment pipelines, runtime architecture, and publication workflows.
- Cover W3D export, HTML5 animation export, streaming sessions, and webots.cloud publication.
- Treat robot design, physics tuning, and generic simulation authoring as out of scope.
- Refer foundational simulation usage to the `webots-core` skill.
- Refer deep Supervisor programming details to the `webots-supervisor` skill.

## Choose deployment mode first

Select the target mode before implementation to avoid mixing incompatible delivery paths.

1. Use **W3D scene export** for static 3D scene sharing in a browser.
2. Use **web animation export** for deterministic playback of a recorded run.
3. Use **web streaming** for real-time remote viewing of a live simulation.
4. Use **webots.cloud interactive deployment** for cloud-hosted interactive sessions.

## Export a Web Scene (W3D)

Use W3D export when only scene visualization is required.

- Export from Webots menu: `File > Export W3D`.
- Generate W3D output as an XML-based scene description for browser rendering.
- Render exported scene with the `webots-view` web component.
- Plan around known unsupported nodes:
  - `Skin` nodes are not supported in W3D scene export.
  - `Pen` nodes are not supported in W3D scene export.
- Package generated W3D assets with any required textures and references before publishing.

## Export a Web Animation

Use animation export when playback is required without a running simulation backend.

- Export through menu path: `File > Export HTML5 Animation`.
- Generate an HTML artifact containing embedded JSON animation data.
- Distribute the generated HTML file as a standalone replay artifact.
- Open playback in modern browsers without a local Webots installation.
- Automate recording from Supervisor logic when scenario setup must be scripted:

```python
supervisor.animationStartRecording("output.html")
# ... run simulation ...
supervisor.animationStopRecording()
```

## Enable live Web Streaming

Use streaming when remote observers must watch current simulation state in real time.

- Start streaming from Webots preferences or command line `--stream` mode.
- Run Webots as the streaming backend server.
- Accept browser clients through WebSocket connections.
- Render live state in browser UIs with `webots-view`.
- Support multiple simultaneous viewers connected to the same stream.

Use these command patterns:

```bash
webots --stream
webots --stream="port=1234"
webots --batch --stream my_world.wbt
```

## Deploy to webots.cloud

Use webots.cloud for browser-native sharing and managed remote execution.

- Maintain source in a GitHub repository.
- Add `webots.yaml` at repository root for publish metadata and mode definition.
- Use static publication for non-interactive animation assets (`.wbt` + `.html`).
- Use Docker-backed publication for interactive simulations.
- Ensure world entry points and project folders are aligned with Webots project conventions.

Minimal publication metadata template:

```yaml
publish:
  type: demo
  title: "My Simulation"
  description: "A demo of my robot"
```

## Apply web server architecture model

Structure interactive deployments around two backend roles.

- Use a **Session Server** to allocate and manage simulation sessions.
- Use one or more **Simulation Servers** to run world instances.
- Connect browser clients through WebSocket-based communication.
- Route each browser session to an active simulation instance.
- Scale by increasing simulation server capacity behind session orchestration.

## Build Docker images for interactive deployment

Base Docker images on the official webots.cloud runtime family.

```dockerfile
FROM cyberbotics/webots.cloud:R2025a-ubuntu22.04
COPY . /usr/local/webots-project/
```

Container guidelines:

- Keep project content under a deterministic project path.
- Include worlds, controllers, PROTOs, and metadata files in the image.
- Pin image tags for reproducibility across environments.
- Validate startup behavior against target world and controller entry points.

## Use required Webots project repository structure

Organize repository files using standard Webots layout before publication.

- `worlds/` contains one or more `.wbt` world files.
- `controllers/` contains runtime controller implementations.
- `protos/` contains custom PROTO definitions.
- `webots.yaml` is placed at repository root.

Reference baseline structure:

```text
project-root/
  webots.yaml
  worlds/
    my_world.wbt
  controllers/
    my_controller/
      my_controller.py
  protos/
    MyRobot.proto
```

## Configure publication metadata in webots.yaml

Define deployment intent in `webots.yaml` before cloud publication.

- Set `publish.type` to the intended mode (`demo`, `competition`, or `animation`).
- Set `publish.title` for catalog display.
- Set `publish.description` for project context.
- Keep metadata concise and aligned with repository content.

## Execute deployment workflow

Use this sequence for consistent delivery:

1. Validate repository structure and required files.
2. Select target mode: W3D, animation, streaming, or cloud interactive.
3. Generate artifacts (W3D/HTML animation) or run streaming backend.
4. Configure `webots.yaml` and Docker image when cloud interactive mode is required.
5. Publish and verify browser playback, stream connectivity, and multi-client access.

## Validate after deployment

Run post-deployment checks to confirm functional web delivery.

- Open outputs in at least one Chromium-based browser and one Gecko/WebKit-based browser.
- Confirm scene rendering or animation playback loads without local Webots dependency.
- Confirm WebSocket stream connects and updates continuously.
- Confirm unsupported nodes do not silently break visuals in exported scenes.
- Confirm cloud metadata, title, and description appear as expected in published entry.

## Use bundled references

Load `references/web_deployment_reference.md` for protocol-level and configuration-level details, including:

- W3D format overview and export constraints.
- `webots-view` integration API.
- WebSocket message categories and flow expectations.
- `webots.yaml` option reference.
- Docker tag guidance.
- Session/simulation server configuration notes.
- Streaming command options and browser compatibility notes.
