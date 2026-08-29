# 🏛️ Procedural 3D Scroll Sites

[![Claude Skill](https://img.shields.io/badge/Claude-Skill-6366f1?style=for-the-badge&logo=anthropic)](https://claude.ai)
[![Three.js](https://img.shields.io/badge/Three.js-r170+-black?style=for-the-badge&logo=three.js)](https://threejs.org/)
[![WebGL / WebGPU](https://img.shields.io/badge/WebGL%20%2F%20WebGPU-Live%20Rendered-38bdf8?style=for-the-badge)](https://developer.mozilla.org/en-US/docs/Web/API/WebGL_API)

> **Build cinematic, scroll-driven 3D websites generated and rendered live from pure code (Three.js/WebGL) — with zero AI video frame extractions, zero raster sprite sheets, and zero external texture assets.**

---

## 🚀 The Anti-Sprite Doctrine

The common amateur workaround for "3D websites" is generating an AI video clip (via Sora, Kling, Runway, etc.), slicing it into 150 individual PNG frames, and scrubbing through the frame sequence on scroll.

### Why Sprite Sequences Fail:
* ❌ **Massive Bloat**: 30MB+ of raster images destroying mobile performance and Core Web Vitals.
* ❌ **Fixed Resolution**: Blurry or pixelated on modern Retina and 4K displays.
* ❌ **Rigid & Uneditable**: Impossible to alter colors, lighting, timing, camera paths, or responsive layouts without re-rendering the entire video.

### The Procedural 3D Solution:
* ✅ **100% Code-Driven**: Objects are built mathematically from geometric primitives.
* ✅ **Live GPU Rendering**: Every pixel is rendered fresh at 60+ FPS directly by the client GPU.
* ✅ **Zero Asset Downloads**: In-memory HTML5 Canvas textures and procedural shaders.
* ✅ **Dynamic Responsiveness**: Real matrix transforms, non-linear damping, interactive cursor parallax, and automatic mobile portrait FOV compensation.

---

## 📚 Complete Reference System

This repository contains the complete skill and reference architecture:

| Document | Highlights |
| :--- | :--- |
| **[`SKILL.md`](./SKILL.md)** | **Master Instruction Manual**: System architecture, 10-phase production build order, 10-point self-check audit gate, and environment compatibility matrix. |
| **[`references/scaffold-and-overlay.md`](./references/scaffold-and-overlay.md)** | **Copy-Paste Scaffolds**: Pre-wired standalone HTML5 & React / Next.js scaffolds with damping, ACES tone mapping, shadow catcher, mobile FOV, and glassmorphic cards. |
| **[`references/procedural-geometry.md`](./references/procedural-geometry.md)** | **5 Procedural Blueprints**: Complete code recipes for Japanese Shrine Pagoda, Sci-Fi Vessel, Cyberpunk Tower, Luxury Smart Device, and Floating Low-Poly Island. |
| **[`references/lighting-and-color-palettes.md`](./references/lighting-and-color-palettes.md)** | **Curated Color Palettes**: Tested hex palettes for *Ethereal Dusk*, *Cyberpunk Obsidian*, *Apple Minimalist Studio*, *Bioluminescent Deep Sea*, and *Solar Flare Amber*. |
| **[`references/procedural-materials-and-textures.md`](./references/procedural-materials-and-textures.md)** | **Zero-Asset Textures**: In-memory 2D Canvas generation for brushed metal, Japanese wood grain, cyber grids, window facades, procedural normal maps, and GLSL water/hologram shaders. |
| **[`references/scroll-choreography.md`](./references/scroll-choreography.md)** | **Motion Design**: Dual-layer damping, non-linear easing curves (`smoothstep`, `smootherstep`, cubic curves), interactive cursor parallax, and idle harmonic breathing. |
| **[`references/polish-and-performance.md`](./references/polish-and-performance.md)** | **Visual Polish & Perf**: ACES filmic tone mapping, 4-point cross-lighting, soft contact shadow catcher, mobile portrait FOV compensation, and zero-allocation render loops. |
| **[`references/scene-templates.md`](./references/scene-templates.md)** | **3 Art-Directed Mood Presets**: Ready-to-use configurations for *"Shrine at Dusk"*, *"Floating Product"*, and *"Night Cityscape"*. |
| **[`references/troubleshooting-and-faq.md`](./references/troubleshooting-and-faq.md)** | **Diagnostic Matrix**: Instant solutions for black canvas, unclickable overlay text, mobile portrait clipping, shadow acne, and memory leaks. |

---

## 🎨 Complete Standalone Runnable Examples

Check out the turnkey HTML examples in [`examples/`](./examples/):
* **[`examples/shrine-at-dusk.html`](./examples/shrine-at-dusk.html)**: Japanese Pagoda with twilight cross-lighting and sunset embers.
* **[`examples/cyberpunk-megastructure.html`](./examples/cyberpunk-megastructure.html)**: Futuristic Megastructure with procedural glowing window facades.
* **[`examples/luxury-product-showcase.html`](./examples/luxury-product-showcase.html)**: Floating Luxury Device with Apple-style studio lighting and brushed metal.

---

## 🛠️ Installation & Usage

### 1. In Claude / AI Coding Assistants
To install this skill into Claude or Antigravity:
1. Download the [`procedural-3d-scroll-sites.skill`](./procedural-3d-scroll-sites.skill) package file.
2. Install it in your Claude skills settings or place it in your assistant's skill directory.
3. Prompt your assistant with any 3D scroll or landing page request:
   > *"Build a cinematic scroll-driven 3D landing page with a procedural Japanese shrine hero and dark mode aesthetic."*

### 2. Standalone Code Usage
You can also copy the production-ready scaffolds directly from [`references/scaffold-and-overlay.md`](./references/scaffold-and-overlay.md) or [`examples/`](./examples/) into any project.

---

## 📐 10-Point Self-Check Gate

Before publishing any procedural 3D website, run through this 10-point audit:

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
