# Community skills: design, 3D, games and simulation

These skills were copied from other people's open-source GitHub repos into this one. They cover **web and UI design**, the jobs Fusion 360-style CAD software does (designing parts to 3D print), building 3D games, simulating robots, reverse engineering compiled software, animation and motion video, and 3D with Blender. Each one is described below in plain English, with an example of what you could ask for and how it could be used in class.

> **Before you use them:** community skills are written by individuals (or companies), not Anthropic. Read the `SKILL.md` and any scripts in a folder before using it on school machines. Most need Python, Node, or other free tools installed. They work best in **Claude Code**. In the Claude app you can upload a skill's folder as a custom skill.
>
> **Fusion 360 itself:** Claude has an official **Autodesk Fusion connector** (and one for Blender), so it doesn't need a skill. In Fusion, turn it on under *Preferences → General → API → Fusion MCP Server*, then connect it in the Claude desktop app.

---

## Web and UI design

These were added in November 2026 from a list of design-focused repos. They help Claude build good-looking, accessible web pages and app interfaces instead of generic "AI-looking" ones. Several overlap on purpose — try one, and swap if it doesn't suit. They're most useful for the ICT/web-design side of Digital Technologies, student project websites, slide decks and posters.

> **Heads-up on what some of these need:**
> - **`astryx`** needs its npm packages and CLI installed (`npm i -g @astryxdesign/cli`, plus `@astryxdesign/core` in the project).
> - **`design`** (part of the ui-ux-pro-max set) can generate logos/icons via **paid AI services** (Gemini, Atlas Cloud, MuAPI). Those parts need API keys; the rest of the skill works without them.
> - **`ui-styling`** bundles ~5 MB of fonts so it can do canvas design offline — that's why that folder is large.

### `ui-ux-pro-max` and its set: `design`, `design-system`, `ui-styling`, `brand`, `banner-design`, `slides`
**What they do:** A connected set of UI/UX design skills (80+ styles, colour palettes and font pairings). `ui-ux-pro-max` is the brain that reviews and fixes interfaces; `design` is the all-in-one (brand, tokens, logos, banners, presentations); `design-system` builds design tokens; `ui-styling` does shadcn/Tailwind/canvas styling; `brand` handles voice and identity; `banner-design` makes social/ad/web/print banners; `slides` builds HTML presentations with charts.
**Try asking:** *"Design a clean, accessible landing page for our school robotics club, give me three style options with matching fonts and colours."*
**In class:** web-design units, student project sites, posters and classroom slide decks.
**Licence:** MIT (Next Level Builder).

### `impeccable`
**What it does:** A design critic and polisher. Point it at a website, landing page, dashboard, app UI or component and it reviews, audits, polishes or animates it — the "never misses" design reviewer.
**Try asking:** *"Critique this page's layout, spacing and typography, then fix the worst problems."*
**In class:** teaching students to evaluate and improve their own interface designs against real design standards.
**Licence:** Apache 2.0 (Paul Bakaus).

### `hallmark`
**What it does:** Builds new websites and pages that look deliberately designed rather than AI-generated — strong layout, type and detail from the start.
**Try asking:** *"Build a one-page site introducing our Year 9 robot-mission project that doesn't look like a template."*
**In class:** showing students the difference between a generic page and a crafted one.
**Licence:** MIT (Nutlope / Hallmark contributors).

### `shadcn`
**What it does:** Adds, searches, fixes and composes **shadcn/ui** components (the popular React + Radix + Tailwind component library) in a project.
**Try asking:** *"Add a dialog and a data table from shadcn/ui to this React app and wire them up."*
**In class:** senior web/app development projects using React.
**Needs:** a React/Tailwind project. **Licence:** MIT (shadcn).

### `magic-ui`
**What it does:** Adds and customises **Magic UI** animated components (moving buttons, marquees, effects) in React/Next.js projects, installed through the shadcn registry.
**Try asking:** *"Add an animated gradient hero and a marquee of our sponsors to this Next.js page."*
**In class:** adding polish and motion to student web projects.
**Needs:** a React/Next.js project. **Licence:** MIT (Magic UI).

### `leonardo-colors`
**What it does:** Generates accessible colour palettes with Adobe's Leonardo tool and checks them against WCAG contrast rules, so text stays readable.
**Try asking:** *"Make an accessible colour theme around teal that passes WCAG AA for normal text."*
**In class:** teaching colour contrast and accessibility — a real, checkable standard students can aim for.
**Licence:** Apache 2.0 (Adobe).

### `astryx`
**What it does:** Builds and themes UI using Meta's open **Astryx** design system and component library.
**Try asking:** *"Set up Astryx in this project and build a settings screen using its components."*
**In class:** senior projects, as an example of a professional design system.
**Needs:** the Astryx npm packages and CLI (see heads-up above). **Licence:** MIT (Meta).

---

## Reverse engineering

### `reverse-engineer-anything`
**What it does:** Uses the open-source REA toolset (built on mainstream tools like Frida and Ghidra) to analyse compiled software — native binaries, .NET/Java, Electron/JavaScript apps, firmware and browser targets. It works from shipped artifacts or runtime behaviour to explain how a feature works, compare versions, decompile code, or guide a rebuild.
**Try asking:** *"Work out how this Electron app stores its settings and what its update check sends."*
**Good for:** understanding and debugging your own software, interoperability, learning how things work under the hood, and defensive analysis. Legitimate dual-use — the same skills apply across research, security and curiosity.
**Needs:** the REA tooling (the skill explains setup). **Licence:** MIT (morluto). *Only the general `reverse-engineer-anything` skill was taken; the repo's other `rea-*` skills are for maintaining the REA product itself and aren't included.*

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

## Animation, motion and video (from code)

Added November 2026. These build animations and motion-graphics videos by writing code, rather than in a timeline editor. They overlap a fair bit — `cinetic`, `motionmaxxing`, `anidoodle` and `huashu-art-motion` can all direct a short motion film — so try one and drop the rest if you don't need them. Most render final video with **FFmpeg** (free; see the note at the bottom), and some can follow a soundtrack or narration.

### `reanimate`
**What it does:** Rebuilds an animation you show it — a live site, a screen recording, an mp4/gif, a Lottie file — frame by frame into your own code, then records the result so you can compare.
**Try asking:** *"Recreate the loading animation in this screen recording as a self-contained HTML/CSS file."*
**In class:** teaching how an effect actually works by reconstructing it. **Licence:** MIT (Niaz Morshed).

### `motion-design`
**What it does:** Applies professional timing and easing (the feel a trained animator gives motion) to web/UI animation.
**Try asking:** *"Animate this card list appearing with natural, staggered easing."*
**In class:** the difference good timing makes. **Licence:** MIT (LottieFiles).

### `cinetic`
**What it does:** Directs, builds and renders premium motion films from code — launch videos, product/feature clips, logo stings, UI walkthroughs, teasers.
**Try asking:** *"Make a 15-second launch video for our robotics club with a logo sting and kinetic text."*
**Licence:** MIT (Leonxlnx).

### `motionmaxxing`
**What it does:** Similar to cinetic — builds professional motion-graphics films (promos, reveals, kinetic type, social ads) aiming for a senior-designer feel rather than generic output.
**Try asking:** *"Turn these three feature bullet points into a punchy 20-second promo."*
**Licence:** Apache 2.0 (Tejash Makwana).

### `anidoodle`
**What it does:** Code-drawn stills, films, 3D motion graphics, explainers and web animations in 32 styles, with optional music, narration and subtitles.
**Try asking:** *"Make a 30-second doodle-style explainer of how an ultrasonic sensor works, with narration."*
**In class:** animated explainers for any subject. **Licence:** Apache 2.0 (Alex Greenshpun).

### `huashu-art-motion`
**What it does:** Art-style video animation — 36+ hand-made styles plus narration-driven explainer formats. **Note:** this skill's instructions are written in **Chinese**. Claude can still follow them and produce English output, but the skill files themselves aren't in English.
**Try asking:** *"Animate this diagram in an ink-painting style with an English voiceover script."*
**Licence:** MIT (alchaincyf).

---

## 3D with Blender (28 skills that work together)

`blender-core`, `blender-scene-setup`, `blender-modeling` and hard-surface, `blender-sculpting`, `blender-retopology-uv`, `blender-texturing`/`-pbr-materials`/`-texture-baking`, `blender-character-rigging`, `blender-facial-shapekeys`, `blender-hair-grooming`, `blender-clothing`, `blender-animation`, `blender-simulation`, `blender-geometry-nodes`, `blender-lighting-lookdev`, `blender-rendering`, `blender-stylized-npr`, `blender-weather-fx`, `blender-time-of-day`, `blender-environment-kits`, `blender-export-pipelines`, `blender-game-optimization`, `blender-video-editing`, `blender-addon-development`, `blender-pipeline-automation`, `blender-asset-qa`, `blender-workflow-recipes`.

**What they do:** Drive **Blender** (the free, professional 3D suite) for modelling, sculpting, rigging, animation, simulation, materials, lighting, rendering and export. Start with `blender-core`; the others build on it.
**Try asking:** *"In Blender, set up a studio-lit scene, import our robot model, and render a turntable animation."*
**In class:** 3D design and animation, visualising robot/CAD models, game assets, and senior media projects. Pairs with the CAD and Three.js skills.
**Needs:** Blender installed (free); works live via a Blender MCP server or headless from the command line. **Licence:** MIT (EL4CTEO).

---

## Finding skills

### `find-skills`
**What it does:** A meta-skill for **Claude Code**. Instead of naming a skill, you say the outcome you want and Claude uses the `npx skills` CLI and the [skills.sh](https://skills.sh/) registry to find, combine and install the most suitable skills.
**Try asking:** *"Find me a skill for writing accessible colour palettes"* — or just describe the job and let it locate the tools.
**To make it active in Claude Code** (this skill works best installed where Claude Code picks it up automatically), run once:
```
npx skills add https://github.com/vercel-labs/skills --skill find-skills
```
**Licence:** MIT (vercel-labs).

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
| ui-ux-pro-max, design, design-system, ui-styling, brand, banner-design, slides | https://github.com/nextlevelbuilder/ui-ux-pro-max-skill | MIT |
| impeccable | https://github.com/pbakaus/impeccable | Apache 2.0 |
| hallmark | https://github.com/Nutlope/hallmark | MIT |
| shadcn | https://github.com/shadcn-ui/ui | MIT |
| magic-ui | https://github.com/magicuidesign/magicui | MIT |
| leonardo-colors | https://github.com/adobe/leonardo | Apache 2.0 |
| astryx | https://github.com/meta/astryx | MIT |
| reverse-engineer-anything | https://github.com/morluto/rea | MIT |
| reanimate | https://github.com/NiazMorshed2007/reanimate | MIT |
| motion-design | https://github.com/LottieFiles/motion-design-skill | MIT |
| cinetic | https://github.com/Leonxlnx/cinetic | MIT |
| motionmaxxing | https://github.com/Tejashmakwana/motionmaxxing | Apache 2.0 |
| anidoodle | https://github.com/alexgreensh/anidoodle | Apache 2.0 |
| huashu-art-motion | https://github.com/alchaincyf/huashu-art-motion | MIT |
| blender-* (28) | https://github.com/EL4CTEO/blender-skills | MIT |
| find-skills | https://github.com/vercel-labs/skills | MIT |

Copied in November 2026. To get the latest versions, check the source repos.

**Also worth a look (not copied because it's very large):** https://github.com/gamedev-skills/awesome-gamedev-agent-skills has 66 game skills for Godot, Unity, Unreal, Roblox, pygame, Phaser and more, with a router that picks the right one (Apache 2.0).

## What was suggested but NOT added, and why

From the design lists, these were left out:

- **Not actually skills — they're libraries, tools or apps with no `SKILL.md`, so there's nothing to "add" as a skill:** Tailwind CSS, shadcn/ui *(the library itself — but the `shadcn` skill that drives it WAS added)*, Lucide (icon set), Rough.js (hand-drawn graphics library), WebGL Fluid Simulation (a demo), Excalidraw and tldraw (drawing apps — tldraw ships skills, but only for developing tldraw itself), Figma Context MCP (an MCP server — connect it as a connector instead), and `rembg` (a Python background-removal tool — run it directly). `frontend-design` was already in this repo (identical to upstream), so it wasn't duplicated.
- **More tools, not skills (from the motion/video list):** Manim (Python animation engine), three.quarks (three.js particles), react-postprocessing (React/three.js effects), Tweakpane (a GUI slider library), EffectCraft (a Rust compositor), librosa (Python audio/beat analysis), WhisperX (Python caption timing), FFmpeg (the video encoder the motion skills call to render), and Openverse (a free-media search **API**, not a repo). These are the building blocks the animation skills *use* — install them when a skill asks, rather than adding them here.
- **No licence file, so not copied:** `remotion-dev/skills` (https://github.com/remotion-dev/skills) — 13 official Remotion video skills, but the repo ships no LICENSE, so there's no clear permission to redistribute them inside this repo. Separately, **Remotion itself is only free for individuals and companies of up to three people** — larger teams need a paid licence. If you want them, install them straight from that repo rather than copying them in.
- **Needs a hosted vendor service:** `stitch` (https://github.com/google-labs-code/stitch-skills) — 16 well-built skills, but every one depends on **Google Stitch**, so they do nothing without a Stitch account. Left out to keep the collection usable out of the box; grab them from the source repo if you start using Stitch.
- **A whole AGPL-licensed application, better kept standalone:** **OpenMontage** (https://github.com/OpenMontageInc/OpenMontage) — a genuine, impressive open-source agentic video-production system with ~92 skills. Not vendored in because it's licensed **GNU AGPL-3.0** (strong copyleft, unlike the MIT/Apache skills here — mixing it in pulls those obligations into the repo), it's a full application rather than a drop-in skill set, and many of its skills are paid-API wrappers or face-swap / AI-avatar (deepfake-capable) tools. Clone it as its own project instead: `git clone https://github.com/OpenMontageInc/OpenMontage`.
- **Offensive-security / exploitation toolkits — not added on safety grounds:** **reverse-skill** (https://github.com/zhaoxuya520/reverse-skill) and **PentAGI** (https://github.com/vxcontrol/pentagi) — credential dumping, privilege escalation, container escape, NTLM relay, automated penetration testing. These are built to attack systems, not to understand or defend them, so they're left out whatever the repo is for. (From `rea`, only the general `reverse-engineer-anything` skill was added — see above; its `rea-*` product-maintenance skills were skipped as not generally useful.)
