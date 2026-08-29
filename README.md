# 🏛️ Procedural 3D & Frame-Sequence Scroll Sites

[![Claude Skill](https://img.shields.io/badge/Claude-Skill-6366f1?style=for-the-badge&logo=anthropic)](https://claude.ai)
[![Three.js](https://img.shields.io/badge/Three.js-r170+-black?style=for-the-badge&logo=three.js)](https://threejs.org/)
[![WebGL / WebGPU](https://img.shields.io/badge/WebGL%20%2F%20WebGPU-Live%20Rendered-38bdf8?style=for-the-badge)](https://developer.mozilla.org/en-US/docs/Web/API/WebGL_API)

> **Build cinematic, scroll-driven 3D websites with two high-performance rendering engines: Mode A (100% Code-Generated Procedural 3D via Three.js/WebGL) and Mode B (Zero-Stutter Image-Sequence Frame Scrubber preloaded into RAM). Also supports Hybrid Mode C (live 3D objects composited over image-sequence backgrounds).**

---

## ⚡ Dual-Engine Architecture

```
                                  ┌──────────────────────────────────────────────────────────┐
                                  │           SCROLL & MOTION CHOREOGRAPHY ENGINE            │
                                  │  • Physics Damping (lerp)   • Non-Linear Easing Math     │
                                  │  • 6-DOF Spline Trajectory  • Interactive Cursor Parallax│
                                  └─────────────────────────────┬────────────────────────────┘
                                                                │
                                ┌───────────────────────────────┴───────────────────────────────┐
                                │                                                               │
                                ▼                                                               ▼
        ┌──────────────────────────────────────────────┐              ┌──────────────────────────────────────────────┐
        │       MODE A: PURE PROCEDURAL 3D (WebGL)      │              │       MODE B: IMAGE-SEQUENCE FRAME SCRUBBER  │
        │ • 100% code-generated mathematical geometry  │              │ • Preloads image frame array into RAM buffer │
        │ • Real-time dynamic lights & contact shadows │              │ • Zero-stutter HTML5 2D Canvas scrubbing     │
        │ • Zero video assets, zero image downloads    │              │ • Sub-frame lerp + Aspect-ratio cover fit    │
        │ • In-memory procedural canvas textures       │              │ • Perfect when user supplies image frames    │
        └──────────────────────────────────────────────┘              └──────────────────────────────────────────────┘
                                │                                                               │
                                └───────────────────────────────┬───────────────────────────────┘
                                                                │
                                                                ▼
                                ┌──────────────────────────────────────────────────────────────┐
                                │             HYBRID MODE C: 3D + IMAGE COMPOSITION            │
                                │ • Layer 1 (Back): Image sequence canvas scrubber             │
                                │ • Layer 2 (Front): Transparent Three.js WebGL canvas         │
                                │ • Synchronized 6-DOF transforms matching background footage  │
                                └──────────────────────────────────────────────────────────────┘
```

---

## 📚 Complete Reference System (11 Comprehensive Guides)

| Document | Highlights |
| :--- | :--- |
| **[`SKILL.md`](./SKILL.md)** | **Master Instruction Manual**: System architecture, 10-phase production build order, 10-point self-check audit gate, and execution matrix. |
| **[`references/scaffold-and-overlay.md`](./references/scaffold-and-overlay.md)** | **Copy-Paste Scaffolds**: Pre-wired standalone HTML5 & React scaffolds with damping, ACES tone mapping, shadow catcher, mobile FOV, and glass cards. |
| **[`references/6dof-scrollytelling-director.md`](./references/6dof-scrollytelling-director.md)** | **6-DOF Motion Director**: Mathematical trajectories for Dolly, Truck, Pedestal, Pan, Tilt, Roll, Spiral Ascent, Spline Fly-Throughs, Vertigo Dolly-Zoom, and LookAt tracking. |
| **[`references/image-sequence-scrubber.md`](./references/image-sequence-scrubber.md)** | **Mode B & C Engine**: Preloading image arrays into RAM, `object-fit: cover` canvas math, DPR retina scaling, sub-pixel damping, and hybrid 3D composition. |
| **[`references/procedural-geometry.md`](./references/procedural-geometry.md)** | **5 Core Blueprints**: Japanese Shrine Pagoda, Sci-Fi Vessel, Cyberpunk Spire, Smart Device, and Floating Low-Poly Island. |
| **[`references/advanced-procedural-blueprints.md`](./references/advanced-procedural-blueprints.md)** | **6 Advanced Blueprints**: Organic DNA Double Helix, Concept Supercar, Holo Data Globe, Classical Parthenon, Quantum Reactor, and Mountain Valley. |
| **[`references/lighting-and-color-palettes.md`](./references/lighting-and-color-palettes.md)** | **Curated Color Palettes**: Tested hex palettes for *Ethereal Dusk*, *Cyberpunk Obsidian*, *Apple Minimalist Studio*, *Bioluminescent Deep Sea*, and *Solar Flare Amber*. |
| **[`references/procedural-materials-and-textures.md`](./references/procedural-materials-and-textures.md)** | **Zero-Asset Textures**: In-memory 2D Canvas generation for brushed metal, Japanese wood grain, cyber grids, window facades, procedural normal maps, and GLSL shaders. |
| **[`references/scroll-choreography.md`](./references/scroll-choreography.md)** | **Motion Design**: Dual-layer damping, non-linear easing curves (`smoothstep`, `smootherstep`, cubic curves), interactive cursor parallax, and idle harmonic breathing. |
| **[`references/polish-and-performance.md`](./references/polish-and-performance.md)** | **Visual Polish & Perf**: ACES filmic tone mapping, 4-point cross-lighting, soft contact shadow catcher, mobile portrait FOV compensation, and zero-allocation render loops. |
| **[`references/scene-templates.md`](./references/scene-templates.md)** | **3 Art-Directed Mood Presets**: Ready-to-use configurations for *"Shrine at Dusk"*, *"Floating Product"*, and *"Night Cityscape"*. |
| **[`references/troubleshooting-and-faq.md`](./references/troubleshooting-and-faq.md)** | **Diagnostic Matrix**: Instant solutions for black canvas, unclickable overlay text, mobile portrait clipping, shadow acne, and memory leaks. |

---

## 🎨 5 Turnkey Standalone Runnable Examples

Explore the complete standalone HTML examples in [`examples/`](./examples/):
* **[`examples/shrine-at-dusk.html`](./examples/shrine-at-dusk.html)**: Japanese Pagoda with twilight cross-lighting and sunset embers.
* **[`examples/cyberpunk-megastructure.html`](./examples/cyberpunk-megastructure.html)**: Cyberpunk Spire with in-memory procedural window facade textures.
* **[`examples/luxury-product-showcase.html`](./examples/luxury-product-showcase.html)**: Floating Luxury Device with Apple-style studio lighting and brushed aluminum.
* **[`examples/image-sequence-scrubber.html`](./examples/image-sequence-scrubber.html)**: **Mode B Demo**: In-memory preloaded frame sequence scrubber with sub-frame damping.
* **[`examples/6dof-camera-flythrough.html`](./examples/6dof-camera-flythrough.html)**: **6-DOF Demo**: Catmull-Rom spline camera flying through an architectural sci-fi tunnel with banking Dutch roll.

---

## 🛠️ Installation & Usage

### 1. In Claude / AI Coding Assistants
1. Download the [`procedural-3d-scroll-sites.skill`](./procedural-3d-scroll-sites.skill) package file.
2. Install it in your Claude skills settings or place it in your assistant's skill directory.
3. Prompt your assistant with any 3D scroll, scrollytelling, or image-scrub request:
   > *"Build a cinematic scroll-driven 3D landing page with a procedural Japanese shrine hero and dark mode aesthetic."*  
   > *(Or)* *"I have 120 image frames for a product unboxing, build a smooth scroll scrubber for them."*

### 2. Standalone Code Usage
Copy any production-ready scaffold from [`references/scaffold-and-overlay.md`](./references/scaffold-and-overlay.md) or [`examples/`](./examples/) directly into your project.

---

## 📐 10-Point Self-Check Gate

1. **Squint Silhouette Test**: Does the object read instantly at a glance without text?
2. **Plan-Shape Coherence**: Do roofs/caps match the footprint shape beneath them?
3. **Deep Overhang Ratio**: Do eaves and bevels extend significantly beyond the body (1.5–1.8x)?
4. **Color & Material Contrast**: Is there high contrast between structural trim and core panels?
5. **Tone Mapping Calibration**: Is `ACESFilmicToneMapping` + `SRGBColorSpace` active?
6. **Scene Grounding & Shadow**: Is the object grounded with real-time soft contact shadows?
7. **Non-Linear Motion Easing**: Does scroll motion accelerate/decelerate via `smoothstep`?
8. **Continuous Scroll Coverage**: Is there visual transformation across the entire 0→1 scroll range?
9. **Idle Micro-Motion ("Breathing")**: Does the scene gently breathe when scrolling stops?
10. **Mobile Portrait Framing**: Does the camera FOV adjust automatically to prevent geometry clipping?

---

## 📄 License

MIT License — free for personal and commercial use.
