Solar System Explorer
An interactive 3D solar system built with Three.js, featuring realistic planet textures, orbital motion, lighting, and smooth camera controls. Designed as an educational and visual exploration tool that runs entirely in the browser.

🌍 Overview
Solar System Explorer is a lightweight, browser‑based 3D experience that lets users explore the planets, moons, and the Sun using intuitive controls.
It uses physically‑based rendering, accurate scale ratios (adjusted for visibility), and high‑quality textures sourced under Creative Commons licensing.

This project is ideal for:

Learning Three.js fundamentals

Experimenting with 3D rendering in the browser

Demonstrating orbital mechanics visually

Expanding into a full educational tool or game

✨ Features
3D models of the Sun and planets with realistic textures

Orbit simulation with adjustable speeds

Interactive camera controls (orbit, zoom, pan)

Dynamic lighting simulating sunlight

Scalable architecture for adding moons, rings, UI panels, or data overlays

Modular code structure for easy extension

🛠️ Tech Stack
Three.js — WebGL rendering

JavaScript / ES Modules

Vite / Webpack / Local server (depending on your setup)

Creative Commons textures from Solar System Scope

📦 Installation & Setup
Clone the repository:

bash
git clone https://github.com/your-username/solar-system-explorer.git
cd solar-system-explorer
Install dependencies (if using a bundler):

bash
npm install
Run a local development server:

bash
npm run dev
Or, if using a simple static setup, open index.html in a local server:

bash
npx serve .
📁 Project Structure
Code
/src
/textures
/models
/scripts
planets.js
orbits.js
renderer.js
controls.js
index.js
index.html
README.md
🖼️ Texture Attribution (Required)
This project uses textures from Solar System Scope, licensed under CC BY 4.0.

Code
Planet textures © Solar System Scope (www.solarsystemscope.com)
Licensed under CC BY 4.0 (https://creativecommons.org/licenses/by/4.0/)
Original assets: https://www.solarsystemscope.com/textures/
If you modified the textures (resized, compressed, etc.), add:

Code
Some textures have been resized or processed for performance.
🚀 Future Improvements
Planned or suggested enhancements:

Add moons and moon orbits

Add UI panels with scientific data

Add time controls (pause, fast‑forward, real‑time mode)

Add asteroid belt and dwarf planets

Add VR mode using WebXR

Add sound design and ambient effects

🤝 Contributing
Contributions are welcome.
Feel free to open issues, submit pull requests, or suggest new features.

📄 License
This project is released under the MIT License (or your chosen license).
Texture assets remain under CC BY 4.0 as required by their creators.
© 2026 Solar System Explorer. All rights reserved. Istvan Biczyk
Textures provided by Solar System Scope Licensed under Creative Commons Attribution 4.0 International (CC BY 4.0)
