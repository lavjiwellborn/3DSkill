---
name: procedural-3d-scroll-sites
description: How to build cinematic, "ultra 3D animated" websites with two powerful rendering engines: Mode A (Pure Procedural 3D generated live from code via Three.js/WebGL without video assets) and Mode B (High-Performance Image-Sequence Frame Scrubber that preloads frames into RAM and scrubs smoothly on scroll with zero stutter). Also supports Hybrid Mode C (compositing live 3D objects over image-sequence backgrounds). Features full 6-DOF (Degrees of Freedom) camera choreography (Dolly, Truck, Pedestal, Pan, Tilt, Roll, Spline Flight), ACES tone mapping, cross-lighting, contact shadows, procedural in-memory canvas textures, and interactive cursor parallax. ALWAYS use this skill for 3D scroll experiences, scrollytelling websites, procedural WebGL, image frame scrubbing, or Apple-style interactive product reveals.
---

# Procedural 3D & Frame-Sequence Scroll Sites

## ⚡ START HERE — Decision Router (Mandatory First Step)

**Before reading any other file**, open the Decision Router to map the user's prompt to the exact files you need, in the exact order:

📄 **[`references/decision-router.md`](file:///e:/SKILL/references/decision-router.md)** — Routes any prompt to the correct mode (A/B/C), geometry blueprint, lighting palette, camera trajectory, and build sequence.

If this is your first time using this skill, also read the Golden Path walkthrough for a complete end-to-end example:

📄 **[`references/golden-path-walkthrough.md`](file:///e:/SKILL/references/golden-path-walkthrough.md)** — Step-by-step tutorial building one complete site from prompt to finished output.

---

## Dual-Engine Architecture

This skill equips AI coding assistants to build award-winning, 60+ FPS scroll-driven interactive websites across **two distinct rendering modes and a hybrid composition mode**:

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

## 1. When to Use Which Mode

* **Use Mode A (Pure Procedural 3D)**: When the user asks for a 3D animated website, procedural geometry, Japanese shrine/pagoda, sci-fi vessel, cyberpunk tower, low-poly landscape, tech device, or wants lightweight, zero-download code-generated 3D.
* **Use Mode B (Image-Sequence Frame Scrubber)**: When the user provides reference image frames (e.g. `frame_0001.jpg` to `frame_0150.jpg` or an array of image URLs) and wants an Apple-style, frame-accurate scrub on scroll.
* **Use Hybrid Mode C**: When the user wants real-time 3D interactive particles, text labels, or geometric overlays sitting on top of pre-rendered background frames.

---

## 2. Master Reference Documentation

Read these dedicated reference guides to implement every subsystem with precision:

| Reference Guide | Core Topic |
| :--- | :--- |
| **[`references/decision-router.md`](file:///e:/SKILL/references/decision-router.md)** | **⚡ START HERE**: Prompt → Mode → Files → Build Order routing logic. |
| **[`references/golden-path-walkthrough.md`](file:///e:/SKILL/references/golden-path-walkthrough.md)** | **End-to-End Tutorial**: Complete build from prompt to 12-point self-check gate. |
| **[`references/scaffold-and-overlay.md`](file:///e:/SKILL/references/scaffold-and-overlay.md)** | **Production Scaffolds**: Copy-paste HTML5 & React scaffolds with damping, ACES tone mapping, shadow catcher, mobile FOV, accessibility, CDN error handling, and glass cards. |
| **[`references/6dof-scrollytelling-director.md`](file:///e:/SKILL/references/6dof-scrollytelling-director.md)** | **6-DOF Motion Director**: Formulas for Dolly, Truck, Pedestal, Pan, Tilt, Roll, Spiral Ascent, Spline Fly-Throughs, Vertigo Dolly-Zoom, and LookAt tracking. |
| **[`references/image-sequence-scrubber.md`](file:///e:/SKILL/references/image-sequence-scrubber.md)** | **Mode B & C Engine**: In-memory preloader, aspect-ratio cover math, DPR scaling, sub-pixel damping, and hybrid 3D composition. |
| **[`references/procedural-geometry.md`](file:///e:/SKILL/references/procedural-geometry.md)** | **5 Geometric Blueprints**: Pagoda, Sci-Fi Vessel, Cyberpunk Spire, Smart Device, and Floating Island with Lathe math. |
| **[`references/advanced-procedural-blueprints.md`](file:///e:/SKILL/references/advanced-procedural-blueprints.md)** | **6 Additional Blueprints**: DNA Double Helix, Concept Supercar, Holo Data Globe, Classical Parthenon, Quantum Reactor, and Mountain Valley. |
| **[`references/lighting-and-color-palettes.md`](file:///e:/SKILL/references/lighting-and-color-palettes.md)** | **Curated Color Palettes**: Tested hex codes for *Ethereal Dusk*, *Cyberpunk Obsidian*, *Apple Minimalist Studio*, *Bioluminescent Deep Sea*, and *Solar Flare Amber*. |
| **[`references/procedural-materials-and-textures.md`](file:///e:/SKILL/references/procedural-materials-and-textures.md)** | **Zero-Asset Textures**: In-memory 2D Canvas generation for brushed metal, Japanese wood grain, cyber grids, window facades, procedural normal maps, and GLSL shaders. |
| **[`references/scroll-choreography.md`](file:///e:/SKILL/references/scroll-choreography.md)** | **Motion Design**: Dual-layer damping, non-linear easing curves (`smoothstep`, `smootherstep`, cubic curves), interactive cursor parallax, and idle breathing. |
| **[`references/polish-and-performance.md`](file:///e:/SKILL/references/polish-and-performance.md)** | **Visual Polish & Perf**: ACES tone mapping, 4-point cross-lighting, shadow catcher, mobile portrait FOV math, zero-allocation loop, and GPU cleanup. |
| **[`references/scene-templates.md`](file:///e:/SKILL/references/scene-templates.md)** | **3 Art-Directed Mood Presets**: *"Shrine at Dusk"*, *"Floating Product"*, and *"Night Cityscape"*. |
| **[`references/troubleshooting-and-faq.md`](file:///e:/SKILL/references/troubleshooting-and-faq.md)** | **Diagnostic Matrix**: Instant solutions for black canvas, unclickable buttons, shadow acne, mobile portrait cutoff, CDN failures, and performance drops. |

---

## 3. Turnkey Standalone Runnable Examples

| Runnable Example | What It Demonstrates |
| :--- | :--- |
| **[`examples/shrine-at-dusk.html`](file:///e:/SKILL/examples/shrine-at-dusk.html)** | Japanese Pagoda with twilight cross-lighting, sunset embers, and glass cards. |
| **[`examples/cyberpunk-megastructure.html`](file:///e:/SKILL/examples/cyberpunk-megastructure.html)** | Cyberpunk Spire with in-memory window facade textures and cyan/magenta lighting. |
| **[`examples/luxury-product-showcase.html`](file:///e:/SKILL/examples/luxury-product-showcase.html)** | Luxury Hardware Device with Apple-style studio lighting and brushed metal extrusion. |
| **[`examples/image-sequence-scrubber.html`](file:///e:/SKILL/examples/image-sequence-scrubber.html)** | **Mode B Demo**: In-memory frame preloader and sub-frame scroll scrubber. |
| **[`examples/6dof-camera-flythrough.html`](file:///e:/SKILL/examples/6dof-camera-flythrough.html)** | **6-DOF Demo**: Catmull-Rom spline camera flying through an architectural sci-fi tunnel with banking Dutch roll. |
| **[`examples/drone-aerial-descent.html`](file:///e:/SKILL/examples/drone-aerial-descent.html)** | **Advanced Multi-Scene Demo**: Autonomous drone aerial flight through a cyberpunk neon canyon into a hillside villa with full HUD telemetry. |

---

## 4. 10-Phase Production Build Order

1. **Scaffold & Color Science**: Initialize renderer with ACES tone mapping, sRGB color space, and shadow mapping immediately:
   ```js
   renderer.toneMapping = THREE.ACESFilmicToneMapping;
   renderer.toneMappingExposure = 1.0;
   renderer.outputColorSpace = THREE.SRGBColorSpace;
   renderer.shadowMap.enabled = true;
   renderer.shadowMap.type = THREE.PCFSoftShadowMap;
   ```
2. **Lighting & Grounding**: Build the 3-point cross-lighting rig (warm key + cool rim + hemisphere bounce) and add the `ShadowMaterial` ground plane catcher.
3. **Geometry Construction**: Build the hero object inside a single parent `THREE.Group` using blueprints from `procedural-geometry.md` or `advanced-procedural-blueprints.md`.
4. **Procedural Surface Detailing**: Apply procedural canvas textures (brushed metal, wood grain, or grid emissive) from `procedural-materials-and-textures.md`.
5. **Atmospheric Environment**: Add `scene.fog = new THREE.FogExp2(color, density)` and atmospheric particle points for depth.
6. **Scroll & Damping Engine**: Wire the scroll progress calculator and apply `smoothstep` non-linear easing across the keyframe timeline.
7. **6-DOF Trajectory & Cursor Parallax**: Layer 6-DOF camera moves from `6dof-scrollytelling-director.md` and interactive cursor tilt.
8. **HTML/CSS Overlay Integration**: Lay out sections with `min-height: 100vh` and `.glass-card` text panels with `backdrop-filter: blur(16px)`.
9. **Responsive & Mobile Adaptation**: Implement the portrait aspect ratio FOV compensation formula so wide models are never cut off on smartphones.
10. **Self-Check Gate Verification**: Run through the mandatory 12-point audit below before considering the work finished.

---

## 5. Mandatory 12-Point Self-Check Gate

> **EVERY item must pass before output is considered finished.** If any item fails, fix it and re-check.

1. **Squint Silhouette Test**: Does the object read instantly as the intended subject without reading any text?
2. **Plan-Shape Coherence**: Do roofs/caps match the footprint shape beneath them (square on square, round on round)?
3. **Deep Overhang Ratio**: Do eaves and bevels extend significantly beyond the body beneath them (overhang factor 1.5–1.8)?
4. **Color & Material Contrast**: Is there high contrast between structural trim and core panels?
5. **Tone Mapping Calibration**: Is `ACESFilmicToneMapping` + `SRGBColorSpace` active?
6. **Scene Grounding & Shadow**: Is the object grounded with real-time soft contact shadows on a shadow catcher plane?
7. **Non-Linear Motion Easing**: Does scroll motion accelerate and decelerate smoothly via `smoothstep` or cubic easing?
8. **Continuous Scroll Coverage**: Is there meaningful visual transformation across the entire 0→1 scroll range with zero dead zones?
9. **Idle Micro-Motion ("Breathing")**: Does the object/camera maintain subtle harmonic float when scrolling stops?
10. **Mobile Portrait Framing**: When viewport width is narrower than height, does the camera FOV adjust automatically to prevent geometry clipping?
11. **Reduced Motion Respect**: Is `window.matchMedia('(prefers-reduced-motion: reduce)')` checked? If true, idle breathing, particle drift, and continuous rotation are disabled or reduced to near-zero amplitude.
12. **Text Contrast & Readability**: Do all glass card text panels maintain WCAG AA contrast (4.5:1 for body text) against the 3D background? Is `backdrop-filter: blur()` applied so text is never unreadable over bright geometry?

---

## 6. Common Pitfalls (Quick Reference)

| Mistake | Why It Happens | Fix |
| :--- | :--- | :--- |
| **Black canvas** | No lights added with `MeshStandardMaterial` | Always add at least `AmbientLight` + `DirectionalLight` |
| **Camera inside geometry** | Camera at `(0,0,0)` and object at `(0,0,0)` | Move camera to `(0, 2, 8)` minimum |
| **No scroll movement** | Page body has no scrollable height | Ensure `.scroll-section` blocks have `min-height: 100vh` |
| **Overlay text unclickable** | Canvas z-index above overlay | Canvas: `pointer-events: none; z-index: 0;` |
| **Round roof on square body** | Using `LatheGeometry` on `BoxGeometry` body | Use `ConeGeometry(r, h, 4)` rotated 45° for square bodies |
| **Colors washed out** | Missing tone mapping | Add `renderer.toneMapping = THREE.ACESFilmicToneMapping` |
| **Mobile clipping** | Fixed FOV on narrow viewports | Apply `camera.fov = baseFov / aspect * 0.78` when `aspect < 1.0` |
| **Micro-stutters** | Allocating objects inside `animate()` | Pre-allocate all vectors/colors outside the render loop |
| **CDN fails silently** | No error handling on `import()` | Wrap CDN import in `try/catch` with static fallback message |

---

## 7. Three.js Version Management

The skill pins Three.js at **r170** (`0.170.0`). All CDN URLs in scaffolds and examples use this version.

**To update the version:**
1. Change the CDN URL in `scaffold-and-overlay.md` (search for `three@0.170.0`)
2. Update all examples in `examples/` directory
3. Test that `ACESFilmicToneMapping`, `SRGBColorSpace`, `PCFSoftShadowMap`, and `ShadowMaterial` still exist in the new version's API

**Three.js breaking change history (relevant):**
- `r152+`: `outputEncoding` renamed to `outputColorSpace`, `sRGBEncoding` → `SRGBColorSpace`
- `r155+`: `CanvasTexture` auto-sets `needsUpdate = true`
- `r160+`: Import maps deprecated for direct ESM CDN imports
