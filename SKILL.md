---
name: procedural-3d-scroll-sites
description: How to build cinematic, scroll-driven 3D websites where the entire 3D scene (buildings, temples, product objects, environments) is generated from code and rendered live, pixel-by-pixel, by the GPU — with the object's rotation/position/camera tied directly to scroll — instead of generating a video (Kling, Seedance, Runway, Sora, etc.), extracting it into frames, and playing the frames back as a sprite/frame-sequence animation. ALWAYS use this skill when the user asks for a "3D animated" or "3D animative" website, a scroll-driven 3D scene, an object that rotates/rises/moves as the page scrolls, a "cinematic", "immersive", or "premium feel" landing page with a 3D hero, or explicitly says they want real/procedural/code-generated 3D instead of converting AI video into frames — even if they never say "Three.js" or "WebGL" by name. Also trigger this any time the user describes the frame-extraction-from-video workflow and wants an alternative to it.
---

# Procedural 3D Scroll Sites

## 1. What This Skill Is and Why It Exists

This skill teaches an AI to build cinematic, scroll-driven 3D websites where the entire 3D scene (a building, a temple, a product, an environment) is generated and rendered **live from code** via Three.js/WebGL, with scroll position driving real transforms on real geometry.

It specifically exists to replace a common workaround: generating a clip from an AI video model (Kling, Seedance, Runway, Sora, etc.), slicing it into frames, and playing those frames back as a scroll-triggered image sequence. That frame-playback approach is fixed-resolution, fixed-camera-path, has visible compression artifacts, and cannot be reshaped or lit dynamically.

This skill's premise is: **no video, no frames, anywhere in the pipeline** — just geometry, lighting, materials, and math, recomputed live by the GPU every frame.

---

## 2. The Quality Bar: Art-Directed, Not Photorealistic

Pure procedural primitive geometry rendered live in a browser — with zero downloaded 3D model files and zero external raster textures — **cannot achieve literal photorealism**. Photorealism requires high-poly scanned assets and offline path-tracing, both incompatible with live web performance.

Do not write code or set expectations promising "ultra-photorealistic" results. The honest, achievable target is:
- **Considered composition**: 60/30/10 color palette discipline
- **Physical lighting response**: Warm key vs. cool rim cross-lighting + ground shadow catcher
- **ACES filmic tone mapping**: Controlled highlight roll-off and dark value depth
- **Deliberate silhouette recognition**: Instant squint-test readability from primitive combinations
- **Organic motion**: Damped smoothstep/smootherstep easing + subtle idle breathing

A scene that looks *art-directed and designed by a human team*, rather than an uncalibrated default demo.

---

## 3. Core Architecture: The Fixed-Canvas Pattern

The foundational layout for all procedural 3D scroll websites:

```
┌──────────────────────────────────────────────────────────┐
│ Viewport (Fixed Screen Space)                            │
│                                                          │
│  [ Canvas: position: fixed; inset: 0; z-index: 0 ]       │
│  • Three.js WebGLRenderer rendering live GPU scene       │
│  • pointer-events: none; (allows scroll & interaction)   │
│                                                          │
│  [ DOM Overlay: position: relative; z-index: 10 ]        │
│  • .scroll-container with tall scroll height (min 400vh) │
│  • .scroll-section blocks (min-height: 100vh)            │
│  • .glass-card text panels (pointer-events: auto;)       │
│                                                          │
└──────────────────────────────────────────────────────────┘
```

Scroll events update a normalized `targetProgress` `[0, 1]`, which is smoothly damped via `requestAnimationFrame` and mapped through non-linear easing curves to camera/object transforms.

---

## 4. Master Reference Guides

Read these dedicated reference files to implement each subsystem:

| Reference Guide | Core Topic & Contents |
| :--- | :--- |
| **[`references/color-and-lighting.md`](file:///e:/SKILL/references/color-and-lighting.md)** | **Color & Lighting Discipline**: The 60/30/10 rule, anti-cliché ban list, 3 named starter palettes (*Ethereal Dusk*, *Studio Minimal*, *Nocturne Cityscape*), ACES tone mapping setup with r128 vs r152+ version caveat, and warm/cool cross-lighting. |
| **[`references/procedural-geometry.md`](file:///e:/SKILL/references/procedural-geometry.md)** | **Geometric Blueprints**: The Lathe trick for curved profiles, plan-shape coherence rule (square roof on square body), 4 worked blueprints (Japanese Pagoda, Modern Minimalist Tower, Low-Poly Floating Island, Sacred Torii Gate). |
| **[`references/procedural-materials.md`](file:///e:/SKILL/references/procedural-materials.md)** | **Zero-Asset Surface Materials**: In-memory HTML5 Canvas 2D texture generators (wood grain, brushed metal, window facade grid, roof tiles), PMREMGenerator procedural environment reflections, and physical roughness/metalness table. |
| **[`references/scroll-choreography.md`](file:///e:/SKILL/references/scroll-choreography.md)** | **Motion & Easing Mechanics**: Normalized scroll progress driver, first-order IIR damping loop, smoothstep & smootherstep formulas, keyframe timeline interpolation, non-accumulative mouse parallax, and idle breathing micro-motion. |
| **[`references/polish-and-performance.md`](file:///e:/SKILL/references/polish-and-performance.md)** | **Production Polish & Performance**: 3-point cross-lighting rig, invisible contact shadow catcher plane, mobile portrait FOV compensation, zero-allocation loop rules (no `new` in `animate()`), and GPU memory disposal matrix. |
| **[`references/scaffold-and-overlay.md`](file:///e:/SKILL/references/scaffold-and-overlay.md)** | **Complete Scaffolds**: Copy-paste standalone HTML5 and React/Next.js scaffolds with ACES tone mapping, shadow catcher, glassmorphic card CSS, `<noscript>` fallback, and loading safety timeout. |
| **[`references/troubleshooting-and-faq.md`](file:///e:/SKILL/references/troubleshooting-and-faq.md)** | **Diagnostic Matrix**: Fast fixes for blank canvas, camera inside geometry, missing scroll height, unclickable text cards, washed-out colors, mobile portrait clipping, and shadow acne. |

---

## 5. 10-Phase Production Build Order

When building a procedural 3D scroll experience, execute these phases in sequence:

1. **Scaffold & Canvas Setup**: Create full-bleed fixed canvas, attach passive scroll & pointer listeners, configure resize handler.
2. **Color Science & Tone Mapping**: Set `renderer.toneMapping = THREE.ACESFilmicToneMapping`, `renderer.toneMappingExposure`, and `renderer.outputColorSpace = THREE.SRGBColorSpace` (or `outputEncoding = THREE.sRGBEncoding` for r128).
3. **Lighting Rig & Grounding**: Add warm directional key light with shadow map, cool rim light, ambient fill, and invisible `ShadowMaterial` plane beneath base.
4. **Dominant Environment**: Set `renderer.setClearColor()` and `scene.fog = new THREE.FogExp2(color, density)` matching the 60% dominant palette hue.
5. **Geometry Construction**: Build the subject inside a single root `THREE.Group`, strictly obeying plan-shape matching (square roofs on square bodies).
6. **In-Memory Texturing & Reflections**: Generate procedural `CanvasTexture` maps for surfaces and set up `PMREMGenerator` reflections for metallic parts.
7. **Damped Scroll Engine**: Wire `targetProgress` calculation, damping lerp in `animate()`, and map `smootherstep(currentProgress)` across the keyframe timeline.
8. **Cursor Parallax & Idle Breathing**: Layer subtle mouse position offsets and `prefers-reduced-motion`-gated sinusoidal idle breathing.
9. **HTML Overlay & Contrast**: Lay out `.scroll-section` blocks with glassmorphic cards using `backdrop-filter: blur(18px)` and WCAG AA compliant text.
10. **Self-Check Gate Verification**: Run the 9-point audit gate below.

---

## 6. Mandatory 9-Point Self-Check Gate

> **Every item is a checkable YES/NO against the rendered result. All 9 must pass before output is considered finished.**

1. **Squint Silhouette Test**: Does the object read instantly as the intended subject at a glance without reading any text?
2. **Plan-Shape Coherence**: Do roofs, caps, and tiers match the footprint shape beneath them (square on square, round on round — no circular Lathe roofs on square boxes)?
3. **Deep Overhang Ratio**: Do eaves, ledges, or bevels extend significantly beyond the body beneath them (overhang factor 1.4–1.8×)?
4. **Color & Material Contrast**: Does the scene obey the 60/30/10 rule (60% background/fog, 30% structure, 10% singular accent) with high value contrast between trim and core panels?
5. **Continuous Scroll Coverage**: Is there meaningful visual transformation (rotation, elevation, detail reveal) across the entire 0→1 scroll range without static dead zones?
6. **Tone-Mapping Calibration**: Is `ACESFilmicToneMapping` and correct color space (`SRGBColorSpace` / `sRGBEncoding`) actively configured on the renderer?
7. **Text/UI Contrast**: Do text panels over the 3D scene maintain WCAG AA (4.5:1) contrast against their busiest background moment via background tint and backdrop blur?
8. **Mobile Portrait Framing**: Does the camera FOV adjust for narrow aspect ratios (`aspect < 1.0`) so the subject is never clipped on mobile screens?
9. **Idle Motion Present**: Does the scene maintain subtle harmonic breathing when scrolling pauses, while strictly disabling or dampening when `prefers-reduced-motion: reduce` is detected?

---

## 7. Common Failure Modes & Quick Fixes

| Symptom | Root Cause | Fix |
| :--- | :--- | :--- |
| **Black canvas** | `MeshStandardMaterial` used with no lights | Add `AmbientLight` + `DirectionalLight` |
| **Camera inside geometry** | Camera at `(0,0,0)` matching object position | Position camera at `(0, 2, 8)` and call `camera.lookAt(0, 0, 0)` |
| **No scroll animation** | Body has no scroll height (`maxScroll = 0`) | Ensure `.scroll-section` elements have `min-height: 100vh` |
| **Buttons unclickable** | Canvas z-index above DOM or missing pointer-events | Set canvas `pointer-events: none; z-index: 0;` and cards `pointer-events: auto;` |
| **Disc on a box** | Circular `LatheGeometry` roof on square body | Use `ConeGeometry(radius, height, 4)` rotated 45° for square bodies |
| **Washed out highlights** | Missing ACES tone mapping | Set `renderer.toneMapping = THREE.ACESFilmicToneMapping` |
| **Mobile cutoff** | Fixed vertical FOV on narrow screens | Apply `camera.fov = baseFov / aspect * 0.78` when `aspect < 1.0` |
| **Micro-stutters** | Allocating `new THREE.Vector3()` in `animate()` | Pre-allocate reusable scratchpad vectors outside the render loop |
| **Drifting camera** | Mouse parallax using `+=` instead of `=` | Assign `camera.position.x = mouseCurrentX * offset` directly |

---

## 8. Three.js Version Notes

- **Modern ES Module (r152+)**: Use `renderer.outputColorSpace = THREE.SRGBColorSpace;` and `texture.colorSpace = THREE.SRGBColorSpace;`.
- **Claude.ai Artifact Sandbox (r128)**: Use `renderer.outputEncoding = THREE.sRGBEncoding;`. Do not use `outputColorSpace`.
