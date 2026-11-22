# Community skills: 3D design, games and simulation

These skills were copied from other people's open-source GitHub repos into this one. They cover the jobs Fusion 360-style CAD software does (designing parts to 3D print), building 3D games, and simulating robots. Each one is described below in plain English, with an example of what you could ask for and how it could be used in class.

> **Before you use them:** community skills are written by individuals, not Anthropic. Read the `SKILL.md` and any scripts in a folder before using it on school machines. Most need Python or other free tools installed. They work best in **Claude Code**. In the Claude app you can upload a skill's folder as a custom skill.
>
> **Fusion 360 itself:** Claude has an official **Autodesk Fusion connector** (and one for Blender), so it doesn't need a skill. In Fusion, turn it on under *Preferences → General → API → Fusion MCP Server*, then connect it in the Claude desktop app.

---

## 3D design and CAD (designing physical parts)

### `parametric-3d-printing`
**What it does:** You describe a physical object. Claude designs it as an adjustable ("parametric") 3D model with CadQuery, exports an STL you can print, shows preview images, and keeps changing it with you until it's right.
**Try asking:** *"Design a mount that holds an ultrasonic sensor on the front of our robot. The sensor is 45 × 20 mm and the robot has two M3 holes 30 mm apart."*
**In class:** quick custom parts for robots (sensor mounts, battery holders, cable clips), Gridfinity storage for the lab, and showing students how changing one measurement updates the whole design.
**Needs:** Python 3.10–3.12 and `pip install -r requirements.txt`.
**Licence:** PolyForm **Noncommercial** 1.0.0 (by flowful-ai). Fine for school teaching use; don't use it for paid or commercial work.

### `openscad`
**What it does:** Designs 3D-printable parts as OpenSCAD code. It has a quick mode for simple parts, renders previews so Claude can check its own work, can rebuild a model from an existing STL, and exports for printing.
**Try asking:** *"Make a phone stand at a 60° angle with a slot for the charging cable, then export it as an STL."*
**In class:** OpenSCAD is free and the designs are plain code, so it's a natural bridge between programming and design. Students can read and change the variables themselves.
**Needs:** OpenSCAD installed (free).
**Licence:** MIT (by andreahaku).

### `cad`
**What it does:** The closest to Fusion 360. Designs parts, assemblies and moving mechanisms in build123d Python, shows them live in a browser viewer, and measures, inspects and exports them (STEP, STL, 3MF, GLB, DXF).
**Try asking:** *"Model a two-part gearbox housing for a TT motor with a snap-fit lid, and export the STEP file."*
**In class:** senior design and engineering projects. STEP files open in Fusion 360, so students can carry on editing there.
**Needs:** `uv tool install cad` (the skill explains the setup).
**Licence:** MIT (Thompson Labs / earthtojake, text-to-cad).

### `robot-simulation`
**What it does:** Writes and checks robot description files (URDF, SRDF, MuJoCo MJCF), so a robot designed in CAD can be loaded into a simulator, ROS or MoveIt.
**Try asking:** *"Turn my two-wheeled robot design into a URDF with two driven wheels and a caster, and check it for errors."*
**In class:** the link between designing a robot and simulating it. It pairs with `cad` and `mujoco`.
**Licence:** MIT (Thompson Labs / earthtojake, text-to-cad).

---

## Robot and physics simulation

### `mujoco`
**What it does:** Builds, checks and runs MuJoCo robot scenes (MuJoCo is a free, fast physics simulator). It asks sensible questions and runs checks that catch obvious physics mistakes, such as a robot that falls through the floor or a missing gripper.
**Try asking:** *"Build a MuJoCo scene with a simple robot arm that picks up a cube from a table, then check it's stable."*
**In class:** extension work for strong robotics students, and an introduction to how real robots are tested in simulation before being built.
**Needs:** `pip install mujoco`.
**Licence:** MIT (coolbeevip).

### Webots skills (12 skills that work together)
`webots-core`, `webots-controller-programming`, `webots-sensors`, `webots-actuators`, `webots-world-building`, `webots-physics`, `webots-robots-catalog`, `webots-proto`, `webots-supervisor`, `webots-humans-assets`, `webots-advanced`, `webots-web-deployment`

**What they do:** Help set up Webots (a free robot simulator used in schools and universities), build simulated worlds, and write and debug robot controllers in Python or C. Each skill covers one area (sensors, motors, physics and so on) and hands off to the others.
**Try asking:** *"In Webots, make an e-puck robot follow a black line using its ground sensors, in Python."*
**In class:** Year 9 robotics. Students can practise line-following, obstacle avoidance and maze missions in simulation before using real robots, or when there aren't enough robots to go round. `webots-web-deployment` covers showing simulations in a web browser.
**Needs:** Webots installed (free).
**Licence:** MIT (bowtiedswan).

---

## 3D games

### Three.js game skills (9 skills that work together)
Start with **`threejs-game-director`**. It plans the game and calls on the others as needed:
`threejs-gameplay-systems`, `threejs-aaa-graphics-builder`, `threejs-game-ui-designer`, `threejs-debug-profiler`, `threejs-qa-release`, `threejs-3d-generator`, `threejs-image-generator`, `threejs-audio-generator`

**What they do:** Build complete 3D games that run in a web browser with Three.js, from the gameplay through graphics, menus and scoring to bug-fixing and getting it ready to share.
**Try asking:** *"Build a 3D browser game where a robot drives around a warehouse collecting batteries before the timer runs out, with a start menu and high score."*
**In class:** game design units, worked examples for students, and quick game-based activities (such as robot missions or revision games) that run in a browser with nothing to install.
**Note:** `threejs-3d-generator`, `threejs-image-generator` and `threejs-audio-generator` use **paid AI services** and need API keys (Tripo, Gemini, ElevenLabs). The other six work without them. Check school policy before using those three with students.
**Licence:** MIT (Majid Manzarpour).

---

## Where each skill came from

| Skill(s) | Source repo | Licence |
|---|---|---|
| parametric-3d-printing | https://github.com/flowful-ai/cad-skill | PolyForm Noncommercial 1.0.0 |
| openscad | https://github.com/andreahaku/openscad_claude_skill | MIT |
| cad, robot-simulation | https://github.com/earthtojake/text-to-cad | MIT |
| mujoco | https://github.com/coolbeevip/mujoco-skills | MIT |
| webots-* | https://github.com/bowtiedswan/webots-skills | MIT |
| threejs-* | https://github.com/majidmanzarpour/threejs-game-skills | MIT |

Copied in November 2026. To get the latest versions, check the source repos.

**Also worth a look (not copied because it's very large):** https://github.com/gamedev-skills/awesome-gamedev-agent-skills has 66 game skills for Godot, Unity, Unreal, Roblox, pygame, Phaser and more, with a router that picks the right one (Apache 2.0).
