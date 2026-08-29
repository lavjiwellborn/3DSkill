---
name: procedural-3d-scroll-sites
description: How to build cinematic, "ultra 3D animated" websites where the entire 3D scene (buildings, temples, product objects, environments) is generated from code and rendered live, pixel-by-pixel, by the GPU — with the object's rotation/position/camera tied directly to scroll — instead of generating a video (Kling, Seedance, Runway, Sora, etc.), extracting it into frames, and playing the frames back as a sprite/frame-sequence animation. ALWAYS use this skill when the user asks for a "3D animated" or "3D animative" website, a scroll-driven 3D scene, an object that rotates/rises/moves as the page scrolls, a "cinematic", "immersive", or "premium feel" landing page with a 3D hero, or explicitly says they want real/procedural/code-generated 3D instead of converting AI video into frames — even if they never say "Three.js" or "WebGL" by name. Also trigger this any time the user describes the frame-extraction-from-video workflow and wants an alternative to it.
---

# Procedural 3D Scroll Sites

## The Problem This Skill Solves (The Anti-Sprite Doctrine)

The common amateur workaround for "3D websites": generate an AI video clip (via Kling, Sora, Runway, etc.), extract 150 individual image frames, and scrub through the image sequence on scroll.

**Why that approach fails:**
* Massive asset weight (30MB+ of raster images destroying page speed and mobile loading).
* Fixed camera paths and fixed resolutions that look blurry/pixelated on Retina and 4K displays.
* Impossible to dynamically tweak colors, lighting, timing, camera angles, or text placement without regenerating the entire video.

**The Procedural 3D Approach:**
There are **zero video clips and zero raster frame images**. The 3D scene is constructed mathematically from geometric primitives, shaded with procedural in-memory textures, lit with real-time dynamic lights, and rendered fresh at 60+ FPS directly by the client's GPU via **Three.js / WebGL / WebGPU**. Scroll position and cursor coordinates drive live matrix transforms on real 3D objects.

---

## Core System Architecture

```
┌─────────────────────────────────────────────────────────────┐
│ HTML Viewport                                               │
│                                                             │
│  ┌───────────────────────────────────────────────────────┐  │
│  │ <canvas id="webgl-canvas"> (Fixed, inset: 0, z: 0)     │  │
│  │  • Real-time procedural 3D hero & particles           │  │
│  │  • ACES Filmic Tone Mapping + Dynamic Cross-Lighting  │  │
│  │  • Ground shadow catcher + Fog depth                  │  │
│  └───────────────────────────────────────────────────────┘  │
│                                                             │
│  ┌───────────────────────────────────────────────────────┐  │
│  │ <main class="scroll-container"> (z-index: 10)         │  │
│  │  • pointer-events: none; (passes scroll to window)    │  │
│  │                                                       │  │
│  │   <section class="scroll-section" style="100vh">      │  │
│  │     <div class="glass-card" (pointer-events: auto)>   │  │
│  │       Hero Heading + Text                             │  │
│  │     </div>                                            │  │
│  │   </section>                                          │  │
│  │                                                       │  │
│  │   <section class="scroll-section" style="100vh">...   │  │
│  └───────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────┘
```

1. **Fixed Fullscreen `<canvas>`**: Sits at `position: fixed; inset: 0; z-index: 0; pointer-events: none;`. Renders the live 3D world.
2. **Natural DOM Scroll Flow**: Sections with `min-height: 100vh` scroll naturally over the canvas, creating pacing and scroll runway.
3. **Dual Damping Loop**: `requestAnimationFrame` smoothly lerps both **scroll progress** and **cursor parallax coordinates** toward target values before updating the 3D matrices.
4. **Glassmorphic Overlays**: Content cards with `backdrop-filter: blur()`, `text-shadow`, and `pointer-events: auto` guarantee crystal-clear typography over dynamic 3D lighting.

---

## Complete Reference & Examples System

Read these dedicated reference guides to implement every subsystem with perfection:

| Reference Guide / Example | What It Covers |
| :--- | :--- |
| **[`references/scaffold-and-overlay.md`](file:///e:/SKILL/references/scaffold-and-overlay.md)** | **Start here.** Complete, copy-paste-ready HTML5 & React scaffolds with pre-wired damping, lighting, shadow catcher, mobile FOV, and glass cards. |
| **[`references/procedural-geometry.md`](file:///e:/SKILL/references/procedural-geometry.md)** | Geometric blueprint library (Pagoda, Sci-Fi Vessel, Cyberpunk Spire, Smart Device, Low-Poly Island), Lathe profile math, and plan-shape rules. |
| **[`references/lighting-and-color-palettes.md`](file:///e:/SKILL/references/lighting-and-color-palettes.md)** | **Curated Color Palettes**: Tested hex codes for *Ethereal Dusk*, *Cyberpunk Obsidian*, *Apple Minimalist Studio*, *Bioluminescent Deep Sea*, and *Solar Flare Amber*. |
| **[`references/scroll-choreography.md`](file:///e:/SKILL/references/scroll-choreography.md)** | Mathematical easing (`smoothstep`, `smootherstep`, cubic curves), interactive cursor parallax, idle breathing engine, and camera splines. |
| **[`references/polish-and-performance.md`](file:///e:/SKILL/references/polish-and-performance.md)** | ACES tone mapping, cross-lighting temperature rigs, shadow catcher, mobile portrait FOV math, zero-allocation render loop, and GPU cleanup. |
| **[`references/procedural-materials-and-textures.md`](file:///e:/SKILL/references/procedural-materials-and-textures.md)** | Zero-download procedural textures (in-memory canvas generation for brushed metal, wood grain, cyber grids, building facades, normal maps) & GLSL shaders. |
| **[`references/scene-templates.md`](file:///e:/SKILL/references/scene-templates.md)** | 3 instant art-directed presets: *"Shrine at Dusk"*, *"Floating Product"*, *"Night Cityscape"*. |
| **[`references/troubleshooting-and-faq.md`](file:///e:/SKILL/references/troubleshooting-and-faq.md)** | Diagnostic matrix for black canvas, unclickable buttons, shadow acne, mobile portrait cutoff, scroll lockup, and performance drops. |
| **[`examples/shrine-at-dusk.html`](file:///e:/SKILL/examples/shrine-at-dusk.html)** | Fully functional standalone HTML demo of the Pagoda with twilight cross-lighting and sunset embers. |
| **[`examples/cyberpunk-megastructure.html`](file:///e:/SKILL/examples/cyberpunk-megastructure.html)** | Fully functional standalone HTML demo of the Cyberpunk Tower with in-memory window facade textures. |
| **[`examples/luxury-product-showcase.html`](file:///e:/SKILL/examples/luxury-product-showcase.html)** | Fully functional standalone HTML demo of the Luxury Device with Apple-style studio lighting. |

---

## 10-Phase Production Build Order

Follow this exact sequence to ensure nothing breaks:

1. **Scaffold & Color Science**: Initialize renderer with ACES tone mapping, sRGB color space, and shadow mapping immediately:
   ```js
   renderer.toneMapping = THREE.ACESFilmicToneMapping;
   renderer.toneMappingExposure = 1.0;
   renderer.outputColorSpace = THREE.SRGBColorSpace;
   renderer.shadowMap.enabled = true;
   renderer.shadowMap.type = THREE.PCFSoftShadowMap;
   ```
2. **Lighting & Grounding**: Build the 3-point cross-lighting rig (warm key + cool rim + hemisphere bounce) and add the `ShadowMaterial` ground plane catcher.
3. **Procedural Geometry Construction**: Build the hero object inside a single parent `THREE.Group` using blueprints from `procedural-geometry.md`.
4. **Procedural Surface Detailing**: Apply procedural canvas textures (brushed metal, wood grain, or grid emissive) from `procedural-materials-and-textures.md`.
5. **Atmospheric Environment**: Add `scene.fog = new THREE.FogExp2(color, density)` and atmospheric particle points for depth.
6. **Scroll & Damping Engine**: Wire the scroll progress calculator and apply `smoothstep` non-linear easing across the keyframe timeline.
7. **Cursor Parallax & Idle Life**: Layer subtle mouse/touch tilt and `Math.sin(time)` idle breathing oscillations so the scene is alive even when not scrolling.
8. **HTML/CSS Overlay Integration**: Lay out sections with `min-height: 100vh` and `.glass-card` text panels with `backdrop-filter: blur(16px)`.
9. **Responsive & Mobile Adaptation**: Implement the portrait aspect ratio FOV compensation formula so wide models are never cut off on smartphones.
10. **Self-Check Gate Verification**: Run through the mandatory 10-point audit below before considering the work finished.

---

## Mandatory 10-Point Self-Check Gate

Evaluate your output honestly against these 10 visual and technical criteria:

1. **Squint Silhouette Test**: Does the object read instantly as the intended subject (shrine, ship, tower, device) without reading any text?
2. **Plan-Shape Coherence**: Do roofs/caps match the footprint shape beneath them (square roof on square box, round lathe on round cylinder)?
3. **Deep Overhang Ratio**: Do eaves, ledges, and bevels extend significantly beyond the body beneath them (overhang factor 1.5–1.8)?
4. **Color & Material Contrast**: Is there high contrast between structural trim and core panels (e.g. red lacquer vs cream plaster, dark titanium vs bright emissive)?
5. **Tone Mapping Calibration**: Is `ACESFilmicToneMapping` active? (Are highlights softly compressed rather than blown out to flat harsh white?)
6. **Scene Grounding & Shadow**: Is the object grounded with real-time soft contact shadows on a shadow catcher plane rather than floating in an empty void?
7. **Non-Linear Motion Easing**: Does scroll motion accelerate and decelerate smoothly via `smoothstep` or cubic easing rather than linear jerkiness?
8. **Continuous Scroll Coverage**: Is there meaningful visual transformation across the entire 0→1 scroll range with zero dead zones?
9. **Idle Micro-Motion ("Breathing")**: Does the object/camera maintain subtle harmonic float when the user stops scrolling?
10. **Mobile Portrait Framing**: When the viewport width is narrower than height (mobile portrait), does the camera FOV adjust automatically to prevent geometry clipping?

---

## Target Environment Execution Matrix

* **Standalone Web / Production Build**:
  Import Three.js r170+ directly via ESM CDN:
  ```html
  <script type="module">
    import * as THREE from 'https://cdn.jsdelivr.net/npm/three@0.170.0/build/three.module.js';
  </script>
  ```
* **React / Next.js / Vite Projects**:
  `npm install three @types/three` and use the component architecture from `references/scaffold-and-overlay.md`.
* **Claude.ai Artifact Sandbox**:
  Use preloaded `three` (r128 API rules: no `CapsuleGeometry`, build capsules from cylinder + spheres; no external asset loading; ensure complete `useEffect` cleanup).
