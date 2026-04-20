# 🌍 Solar System Explorer

An interactive, browser-based 3D solar system simulation built with Three.js. Explore all eight planets with realistic orbital mechanics, high-resolution textures, physically motivated lighting, and a polished mission-control aesthetic — no build step, no installation, runs directly in any modern browser.

![Solar System Explorer](https://img.shields.io/badge/Three.js-r128-orange?style=flat-square) ![License](https://img.shields.io/badge/license-MIT-blue?style=flat-square) ![Status](https://img.shields.io/badge/status-live-brightgreen?style=flat-square)

---

## 🚀 Live Demo

**[View Live on GitHub Pages](https://yourusername.github.io/solar-system-explorer)**

---

## 📋 Table of Contents

- [🌍 Solar System Explorer](#-solar-system-explorer)
  - [🚀 Live Demo](#-live-demo)
  - [📋 Table of Contents](#-table-of-contents)
  - [✨ Features](#-features)
  - [🛠 Getting Started](#-getting-started)
    - [Option 1 — Open directly in a browser](#option-1--open-directly-in-a-browser)
    - [Option 2 — Local development server](#option-2--local-development-server)
    - [Requirements](#requirements)
  - [📁 Project Structure](#-project-structure)
  - [⚙️ How It Works](#️-how-it-works)
    - [Scene Architecture](#scene-architecture)
    - [Orbital Mechanics](#orbital-mechanics)
    - [Lighting Model](#lighting-model)
    - [Asset Loading](#asset-loading)
    - [Camera System](#camera-system)
    - [Raycasting](#raycasting)
  - [🎮 Controls](#-controls)
  - [🔭 Scientific Data](#-scientific-data)
  - [⚠️ Known Limitations](#️-known-limitations)
  - [🔮 Potential Improvements \& Future Features](#-potential-improvements--future-features)
    - [Scientific Accuracy](#scientific-accuracy)
    - [Additional Bodies](#additional-bodies)
    - [Visual Enhancements](#visual-enhancements)
    - [User Experience](#user-experience)
    - [Technical](#technical)
  - [📄 Credits \& Attributions](#-credits--attributions)
  - [📝 Licence](#-licence)

---

## ✨ Features

- **All 8 planets** rendered with 2K NASA-sourced texture maps
- **Elliptical orbits** based on real eccentricity and inclination data from NASA planetary fact sheets
- **Axial tilt** correctly applied to every planet, including Uranus's extreme 98° tilt and Venus's retrograde rotation
- **Earth's cloud layer** as a separate rotating mesh with an alpha map
- **Saturn's rings** with a radially remapped texture and transparency
- **Uranus's rings** rendered as a tilted ring geometry
- **Earth's Moon** orbiting with a realistic 27.32-day period
- **10,000-star procedural starfield** with colour variation and size distribution
- **Sun corona glow** using layered additive-blended sprites
- **Time acceleration** — pause, 1×, 100×, 10,000×, and 100,000× speed modes
- **Orbit line toggle** — show or hide orbital paths
- **Label toggle** — show or hide planet name overlays
- **True scale mode** — switch between visual scale and astronomically accurate proportions
- **Click-to-focus** — smooth camera lerp to any planet with an information panel
- **Planet quick-nav** — bottom strip for instant planet switching
- **Responsive** — handles window resize correctly at all times

---

## 🛠 Getting Started

### Option 1 — Open directly in a browser

Download or clone the repository and open `index.html` by double-clicking it. No server required.

```bash
git clone https://github.com/yourusername/solar-system-explorer.git
cd solar-system-explorer
# Open index.html in your browser
```

### Option 2 — Local development server

If you are editing the code, a local server gives you better error reporting and avoids any `file://` protocol quirks:

```bash
# Using VS Code Live Server extension — click "Go Live" in the status bar

# Or using Python
python -m http.server 5500

# Or using Node.js
npx serve .
```

Then visit `http://127.0.0.1:5500` in your browser.

### Requirements

- A modern browser with WebGL support (Chrome, Firefox, Edge, Safari 15+)
- An internet connection on first load to fetch fonts from Google Fonts
- The `textures/` folder must be present alongside `index.html`

---

## 📁 Project Structure

```
solar-system-explorer/
│
├── index.html          # Main application — HTML structure and canvas
├── css/
│   └── style.css       # All styling, CSS variables, UI component styles
├── js/
│   └── main.js         # Three.js scene, simulation logic, UI wiring
├── textures/           # Local texture files (2K maps)
│   ├── 2k_sun.jpg
│   ├── 2k_mercury.jpg
│   ├── 2k_venus_surface.jpg
│   ├── 2k_earth_daymap.jpg
│   ├── 2k_earth_clouds.jpg
│   ├── 2k_mars.jpg
│   ├── 2k_jupiter.jpg
│   ├── 2k_saturn.jpg
│   ├── 2k_saturn_ring_alpha.png
│   ├── 2k_uranus.jpg
│   └── 2k_neptune.jpg
└── README.md
```

---

## ⚙️ How It Works

### Scene Architecture

The simulation is built on three core Three.js primitives:

**Renderer** — `THREE.WebGLRenderer` with antialiasing enabled and pixel ratio capped at 2 (retina screens have ratios up to 3, but going above 2 yields diminishing visual returns at significant GPU cost). Output encoding is set to `sRGBEncoding` so texture colours are gamma-corrected correctly.

**Scene** — A `THREE.Scene` acts as the root container for every object: planets, orbit lines, the starfield, lights, and the Sun.

**Camera** — A `THREE.PerspectiveCamera` with a 60° field of view. The `near` clipping plane is set to `0.1` and `far` to `100000` to accommodate the full range from close-up planet inspection to a view of Neptune's orbit.

---

### Orbital Mechanics

Each planet uses an **orbit pivot pattern** — the most important architectural decision in the project:

```
Scene (origin = Sun)
└── orbitPivot (Object3D at origin, rotated around Y each frame)
    └── planetGroup (positioned at x = orbitRadius)
        └── planet mesh
        └── cloud mesh (Earth only)
        └── ring mesh (Saturn, Uranus)
        └── label sprite
```

Rotating `orbitPivot.rotation.y` moves the planet in a circle around the Sun. This is far simpler than computing `sin` / `cos` positions manually every frame and naturally handles inclination by pre-tilting the pivot on the X axis.

**Angular velocity** is derived from each planet's real orbital period:

```
ω = 2π / T        (radians per Earth day)
Δangle = ω × Δt_days × timeScale
```

Where `Δt_days` is the frame delta time converted from seconds (`delta / 86400`). This makes motion **frame-rate independent** — the planet advances the same angle per real second at 30fps as it does at 120fps.

Orbit lines are drawn as `THREE.Line` objects using `THREE.EllipseCurve`, with the semi-minor axis calculated from the real eccentricity value:

```
b = a × √(1 - e²)
```

---

### Lighting Model

A single `THREE.PointLight` is placed at the Sun's position `(0, 0, 0)` with a decay value of `1` rather than the physically correct `2`. Real inverse-square falloff makes outer planets too dark to see surface detail, so `1` is a deliberate artistic compromise.

A dim `THREE.AmbientLight` prevents the night sides of planets from being pure black. In real space there is no ambient light, but without it, textures on the dark hemisphere are invisible.

The Sun itself uses `THREE.MeshBasicMaterial` — a material that ignores all lighting. This is intentional: the Sun is a light source, not a receiver. Using `MeshStandardMaterial` on the Sun would incorrectly shade it based on the very light it emits.

---

### Asset Loading

Textures are loaded asynchronously using `THREE.TextureLoader`. The loading screen tracks progress via a two-variable counter system:

```js
let totalAssets = 0; // incremented synchronously before each load starts
let loadedCount = 0; // incremented in each texture callback

function registerAsset() {
  totalAssets++;
}
function updateLoader() {
  loadedCount++; /* dismiss when loadedCount >= totalAssets */
}
```

The key design principle is that `registerAsset()` is called **inside the same conditional block** as the load it registers. This guarantees `totalAssets` only counts assets that will definitely fire a callback, preventing the loader from either freezing (count too high) or dismissing early (count too low).

Each `textureLoader.load()` call provides both a success callback and an error callback, both of which call `updateLoader()`. This ensures the loader always progresses even if a texture fails to load.

---

### Camera System

Navigation uses `THREE.OrbitControls` with damping enabled (`dampingFactor: 0.06`) for smooth momentum after the user releases the mouse.

**Planet focus** is implemented as a frame-by-frame lerp rather than a tween library:

```js
// Each frame, close 4.5% of the remaining distance
camera.position.lerp(desiredPosition, 0.045);
controls.target.lerp(planetWorldPosition, 0.06);
```

`lerp(a, b, t)` computes `a + (b - a) × t`. With `t = 0.045`, the camera closes 4.5% of the gap each frame, producing a natural exponential ease-out — fast at the start, gently settling at the end — with no external dependencies.

The `OrbitControls` target is lerped simultaneously so the camera orbits around the focused planet rather than the Sun.

---

### Raycasting

Mouse picking uses `THREE.Raycaster` to detect which planet mesh the cursor is over or clicking. Mouse pixel coordinates are converted to **Normalised Device Coordinates** (NDC, ranging from -1 to +1) before being passed to the raycaster:

```js
mouseNDC.x = (e.clientX / window.innerWidth) * 2 - 1;
mouseNDC.y = -(e.clientY / window.innerHeight) * 2 + 1;
```

Note the Y axis is negated — browser pixel coordinates increase downward, but NDC increases upward.

An important edge case: click events bubble up the DOM, so clicking a planet nav dot would trigger both the dot's own handler and the document-level raycast handler. This is resolved with `e.stopPropagation()` on nav dot clicks, and a secondary guard that checks whether the click target is inside `#planet-nav` or `#info-panel` before calling `releaseFocus()`.

---

## 🎮 Controls

| Input             | Action                                            |
| ----------------- | ------------------------------------------------- |
| `Click` planet    | Focus camera on planet, open info panel           |
| `Drag`            | Orbit camera around current target                |
| `Scroll`          | Zoom in / out                                     |
| `Esc`             | Release planet focus, return to free camera       |
| `Click` nav dot   | Jump focus to that planet                         |
| `Click` × Dismiss | Close info panel                                  |
| Speed buttons     | Set time scale (Pause / 1× / 100× / 10K× / 100K×) |
| Orbits button     | Toggle orbital path lines                         |
| Labels button     | Toggle planet name overlays                       |
| True Scale button | Toggle between visual scale and real proportions  |

---

## 🔭 Scientific Data

All orbital and physical data is sourced from NASA's planetary fact sheets. The following values are stored per planet and used in the simulation:

| Property                | Used For                                   |
| ----------------------- | ------------------------------------------ |
| `orbitalPeriod` (days)  | Angular velocity calculation               |
| `eccentricity`          | Orbit line ellipse shape                   |
| `inclination` (degrees) | Orbit pivot X rotation                     |
| `axialTilt` (degrees)   | Planet mesh Z rotation                     |
| `rotationPeriod` (days) | Self-rotation speed, negative = retrograde |
| `realOrbitAU`           | True scale orbit radius                    |
| `realRadiusKm`          | True scale planet size                     |

**A note on scale:** The default view uses logarithmic orbital radii and exaggerated planet sizes. This is a deliberate usability choice — at true astronomical scale, Neptune's orbit is 30× Earth's, making it impossible to view the inner and outer solar system simultaneously at a useful zoom level. The **True Scale** toggle reveals the real proportions and demonstrates just how empty the solar system actually is.

---

## ⚠️ Known Limitations

- **Orbital position is not historically accurate.** Planets start at random positions and advance from there. The simulation does not model real ephemeris data, so it cannot show where the planets actually are on a given date.
- **Keplerian orbits are simplified.** The orbit pivot rotates at a constant angular velocity (mean motion), rather than accelerating near perihelion and slowing near aphelion as Kepler's second law requires. The difference is visually subtle for most planets.
- **No gravitational interaction.** Planets do not influence each other's orbits.
- **Moon coverage is limited.** Only Earth's Moon is modelled. Jupiter's Galilean moons, Saturn's Titan, and others are not included.
- **No asteroid belt or Kuiper belt.**
- **No dwarf planets** (Pluto, Ceres, Eris, etc.).

---

## 🔮 Potential Improvements & Future Features

### Scientific Accuracy

- **True Keplerian motion** — solve Kepler's equation numerically each frame to make planets accelerate near perihelion and slow near aphelion
- **Real ephemeris positions** — use NASA's HORIZONS API to initialise each planet at its correct position for the current date
- **Proper inverse-square light falloff** — with HDR rendering and tone mapping to keep outer planets visible

### Additional Bodies

- **Major moons** — add Jupiter's Galilean moons (Io, Europa, Ganymede, Callisto) and Saturn's Titan, each with their own orbital mechanics
- **Asteroid belt** — rendered as a particle system between Mars and Jupiter
- **Kuiper belt** — a second particle band beyond Neptune
- **Dwarf planets** — Pluto, Eris, Ceres with accurate orbits
- **Comets** — with elongated elliptical orbits and a particle tail that orients away from the Sun

### Visual Enhancements

- **Normal maps** — add surface bump detail to Earth, Mars, and the Moon using normal map textures
- **Specular maps** — Earth's oceans should be reflective; landmasses should be matte
- **Night side map** — Earth's city lights visible on the dark hemisphere using an emissive texture
- **Atmospheric scattering shader** — a GLSL shader on Earth and other bodies with atmospheres to simulate the blue limb glow
- **Shadow casting** — planets casting shadows on their moons (requires shadow maps or manual shadow sphere technique)
- **Sun surface shader** — animated GLSL noise shader on the Sun's surface to simulate convection and solar flares
- **Volumetric Saturn rings** — multi-layer ring geometry with varying density and transparency

### User Experience

- **Date picker** — set a real calendar date and jump all planets to their correct positions using ephemeris data
- **Orbital trails** — show the path a planet has traced over the last N simulation days
- **Comparison mode** — side-by-side size comparison of any two bodies
- **Search** — type a planet name to instantly focus the camera
- **Mobile touch support** — pinch-to-zoom and improved touch orbit controls
- **VR mode** — WebXR support for viewing the solar system in a headset
- **Screenshot button** — export the current view as a PNG

### Technical

- **Migrate to Three.js ES modules** — use `import` syntax with a bundler (Vite) for better tree-shaking and maintainability
- **Web Worker for orbital calculations** — offload physics to a worker thread to keep the main thread free for rendering
- **LOD (Level of Detail)** — swap in lower-geometry sphere meshes when a planet is far from the camera to save GPU budget
- **Texture compression** — use `.basis` or `.ktx2` compressed texture formats for faster loading

---

## 📄 Credits & Attributions

**Textures**
All planet and Sun texture maps are provided by [Solar System Scope](https://www.solarsystemscope.com/textures/) under the [Creative Commons Attribution 4.0 International licence (CC BY 4.0)](https://creativecommons.org/licenses/by/4.0/).

**Libraries**

- [Three.js r128](https://threejs.org/) — MIT Licence
- [OrbitControls](https://threejs.org/docs/#examples/en/controls/OrbitControls) — part of the Three.js examples, MIT Licence

**Fonts**

- [Orbitron](https://fonts.google.com/specimen/Orbitron) by Matt McInerney — SIL Open Font Licence
- [Courier Prime](https://fonts.google.com/specimen/Courier+Prime) by Alan Dague-Greene — SIL Open Font Licence

**Scientific Data**

- Planetary orbital and physical data sourced from [NASA Planetary Fact Sheets](https://nssdc.gsfc.nasa.gov/planetary/factsheet/)

---

## 📝 Licence

This project is open source and available under the [MIT Licence](LICENSE).
