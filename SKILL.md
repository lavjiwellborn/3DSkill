---
name: procedural-3d-scroll-sites
description: How to build cinematic, scroll-driven 3D and visual websites using a Dual-Engine Architecture. Mode A (Default): Pure procedural Three.js/WebGL scenes generated from code and rendered live by the GPU. Mode B: High-performance canvas-accelerated frame-sequence/video scrubber with windowed LRU caching and sub-frame alpha crossfading for pre-existing footage. Mode C: Hybrid compositing live WebGL shaders over pre-rendered frames. Includes zero-asset procedural Web Audio and custom GLSL shader toolkits. ALWAYS use this skill when building scroll-driven 3D scenes, cinematic landing pages, or high-performance video/frame scrubbing websites.
---

# Procedural 3D Scroll Sites (Dual-Engine Architecture)

## 1. What This Skill Is and Why It Exists

This skill teaches an AI to build cinematic, scroll-driven visual web experiences using two distinct, high-performance rendering engines and a hybrid compositing pipeline:

- **Mode A (Procedural 3D — Default)**: The entire 3D scene (buildings, temples, products, environments) is generated and rendered **live from code** via Three.js/WebGL, with scroll position driving real transforms on real geometry. Zero video files, zero downloaded textures.
- **Mode B (Enhanced Frame-Sequence Scrubber)**: High-fidelity scrubbing of pre-rendered image sequences or footage using hardware-accelerated 2D Canvas, a memory-bounded Windowed LRU Cache, and sub-frame alpha crossfade interpolation to eliminate discrete stepping.
- **Mode C (Hybrid Pipeline)**: Live procedural 3D objects, custom GLSL shaders, or particle storms composited directly over pre-rendered video/frame backgrounds.

---

## 2. Mode Selection Routing Rules

To ensure architectural integrity and prevent anti-patterns, every project strictly adheres to these routing rules:

> **Mode A (procedural 3D)** is the default. Use it whenever there's no pre-existing visual asset, or the user wants something built/stylized from scratch.
>
> **Mode B (frame-sequence scrubber)** is used *only* when the user already has, or explicitly references having, a real pre-rendered image sequence or video file they want scrubbed — actual photography, licensed footage, or a render they already produced elsewhere. **Mode B must never be reached by generating a video and slicing it into frames as a strategy for fulfilling a from-scratch request** — that's precisely the workflow Mode A exists to replace. If a request has no existing asset, it's a Mode A request, full stop, regardless of how "photorealistic" the ask sounds.
>
> **Mode C (hybrid)** is used when the user wants a live 3D object/UI composited over real pre-rendered background footage (e.g., a procedural product render in front of photographed environment footage).

---

## 3. The Quality Bar: Art-Directed Composition & Optical Continuity

For **Mode A**, pure procedural primitive geometry rendered live in a browser cannot achieve literal photorealism. The achievable, honest target is **art-directed composition**:
- **Considered composition**: 60/30/10 color palette discipline
- **Physical lighting response**: Warm key vs. cool rim cross-lighting + ground shadow catcher
- **ACES filmic tone mapping**: Controlled highlight roll-off and dark value depth
- **Deliberate silhouette recognition**: Instant squint-test readability from primitive combinations
- **Organic motion**: Damped smoothstep/smootherstep easing + subtle idle breathing

For **Mode B & C**, the quality bar is **optical continuity and memory stability**:
- **Memory-bounded caching**: Windowed LRU cache derived from budget formula, keeping RAM under 100MB
- **Sub-frame crossfade**: Continuous fractional alpha blending eliminating discrete frame jumps
- **Typographic depth**: Layered multi-tier z-index hierarchy (text behind and in front of subject)
- **Zero-asset sound design**: Procedural Web Audio modulated dynamically by scroll velocity

---

## 4. Core Architecture: Multi-Tier Compositing Pattern

The foundational layout for modern cinematic scrollytelling:

```
┌──────────────────────────────────────────────────────────┐
│ Viewport (Fixed Screen Space)                            │
│                                                          │
│  [ Level 1: Backdrop Text: z-index: 1; pointer-events: none ]
│  • Giant H1 hero typography sitting behind visual subject│
│                                                          │
│  [ Level 2: Frame Canvas: z-index: 2; pointer-events: none ]
│  • Mode B/C: 2D Canvas rendering windowed ImageBitmaps   │
│                                                          │
│  [ Level 3: WebGL Canvas: z-index: 3; pointer-events: none ]
│  • Mode A: Three.js WebGLRenderer live GPU scene         │
│  • Mode C: Live GPU particle storm & GLSL shader overlay │
│                                                          │
│  [ Level 4: DOM Overlay: position: relative; z-index: 10 ]
│  • .scroll-container with tall scroll height (min 400vh) │
│  • .scroll-section blocks (min-height: 100vh)            │
│  • .glass-card text panels (pointer-events: auto;)       │
│  • Floating HUD telemetry & Web Audio controls           │
│                                                          │
└──────────────────────────────────────────────────────────┘
```

---

## 5. Master Reference Guides

Read these dedicated reference files to implement each subsystem:

| Reference Guide | Core Topic & Contents |
| :--- | :--- |
| **[`references/color-and-lighting.md`](file:///e:/SKILL/references/color-and-lighting.md)** | **Color & Lighting Discipline**: The 60/30/10 rule, anti-cliché ban list, 3 named starter palettes (*Ethereal Dusk*, *Studio Minimal*, *Nocturne Cityscape*), ACES tone mapping setup with r128 vs r152+ version caveat, and warm/cool cross-lighting. |
| **[`references/procedural-geometry.md`](file:///e:/SKILL/references/procedural-geometry.md)** | **Geometric Blueprints**: The Lathe trick for curved profiles, plan-shape coherence rule (square roof on square body), 4 worked blueprints (Japanese Pagoda, Modern Minimalist Tower, Low-Poly Floating Island, Sacred Torii Gate). |
| **[`references/procedural-materials.md`](file:///e:/SKILL/references/procedural-materials.md)** | **Zero-Asset Surface Materials**: In-memory HTML5 Canvas 2D texture generators (wood grain, brushed metal, window facade grid, roof tiles), PMREMGenerator procedural environment reflections, and physical roughness/metalness table. |
| **[`references/scroll-choreography.md`](file:///e:/SKILL/references/scroll-choreography.md)** | **Motion & Easing Mechanics**: Normalized scroll progress driver, first-order IIR damping loop, smoothstep & smootherstep formulas, keyframe timeline interpolation, non-accumulative mouse parallax, and idle breathing micro-motion. |
| **[`references/frame-sequence-scrubber.md`](file:///e:/SKILL/references/frame-sequence-scrubber.md)** | **Mode B & C Frame Scrubber**: Windowed LRU ImageBitmap cache, memory budget formula, manual cover-fit crop math, DPR scaling, sub-frame alpha crossfade blending, video fallback, and typographic depth occlusion. |
| **[`references/procedural-audio.md`](file:///e:/SKILL/references/procedural-audio.md)** | **Zero-Asset Procedural Audio**: Mathematical Web Audio API synthesis (ambient sub-bass drones, scroll-velocity wind modulation, kinetic click resonance, and user gesture unlock). |
| **[`references/custom-shaders-and-fx.md`](file:///e:/SKILL/references/custom-shaders-and-fx.md)** | **Custom GLSL Shaders & FX**: Zero-dependency vertex/fragment shaders for volumetric God Rays, holographic scanlines, procedural caustics, and GPU particle storm drift. |
| **[`references/polish-and-performance.md`](file:///e:/SKILL/references/polish-and-performance.md)** | **Production Polish & Performance**: 3-point cross-lighting rig, invisible contact shadow catcher plane, mobile portrait FOV compensation, zero-allocation loop rules (no `new` in `animate()`), and GPU memory disposal matrix. |
| **[`references/scaffold-and-overlay.md`](file:///e:/SKILL/references/scaffold-and-overlay.md)** | **Complete Scaffolds**: Copy-paste standalone HTML5 and React/Next.js scaffolds for Mode A (Three.js WebGL) and Mode B (Canvas Frame Scrubber) with glassmorphic cards and safety timeouts. |
| **[`references/troubleshooting-and-faq.md`](file:///e:/SKILL/references/troubleshooting-and-faq.md)** | **Diagnostic Matrix**: Fast fixes for blank canvas, camera clipping, missing scroll height, unclickable text cards, washed-out colors, mobile portrait clipping, out-of-memory frame crashes, and CORS canvas tainting. |

---

## 6. Mandatory 16-Point Self-Check Gate

> **Every item is a checkable YES/NO against the rendered result. All applicable items must pass before output is considered finished.**

### Mode A (Procedural 3D) Gate Items:
1. **Squint Silhouette Test**: Does the object read instantly as the intended subject at a glance without reading any text?
2. **Plan-Shape Coherence**: Do roofs, caps, and tiers match the footprint shape beneath them (square on square, round on round — no circular Lathe roofs on square boxes)?
3. **Deep Overhang Ratio**: Do eaves, ledges, or bevels extend significantly beyond the body beneath them (overhang factor 1.4–1.8×)?
4. **Color & Material Contrast**: Does the scene obey the 60/30/10 rule (60% background/fog, 30% structure, 10% singular accent) with high value contrast between trim and core panels?
5. **Continuous Scroll Coverage**: Is there meaningful visual transformation (rotation, elevation, detail reveal) across the entire 0→1 scroll range without static dead zones?
6. **Tone-Mapping Calibration**: Is `ACESFilmicToneMapping` and correct color space (`SRGBColorSpace` / `sRGBEncoding`) actively configured on the renderer?
7. **Text/UI Contrast**: Do text panels over the 3D scene maintain WCAG AA (4.5:1) contrast against their busiest background moment via background tint and backdrop blur?
8. **Mobile Portrait Framing**: Does the camera FOV adjust for narrow aspect ratios (`aspect < 1.0`) so the subject is never clipped on mobile screens?
9. **Idle Motion Present**: Does the scene maintain subtle harmonic breathing when scrolling pauses, while strictly disabling or dampening when `prefers-reduced-motion: reduce` is detected?

### Mode B & C (Frame Scrubber & Hybrid) Gate Items:
10. **Memory-Bounded Playback**: Is frame memory bounded by a windowed/LRU cache derived from the actual budget/resolution formula, independent of total frame count — not a full-sequence preload?
11. **Sub-Frame Continuity**: At any scroll speed, does scrubbing crossfade between adjacent frames with no discrete jump?
12. **Cover-Fit Correctness**: Does the frame fill the canvas without stretching at every tested viewport aspect ratio?
13. **Decode-Fallback Safety**: Does playback degrade gracefully (not break) in browsers lacking `createImageBitmap`/`OffscreenCanvas`?
14. **Mode-Routing Correctness**: Was Mode B/C chosen only because real pre-existing frames/footage were supplied or referenced — never as a workaround for a from-scratch request that Mode A should have served?

### Audio & Shader Gate Items:
15. **Audio Safety**: Does procedural Web Audio initialize only after user gesture and ramp gains smoothly to prevent speaker clicks?
16. **Shader Uniform Cleanliness**: Are all custom shader uniforms (e.g. `uTime`) updated by reference in `animate()` without allocating new objects per frame?

---

## 7. Common Failure Modes & Quick Fixes

| Symptom | Root Cause | Fix |
| :--- | :--- | :--- |
| **Black canvas** | `MeshStandardMaterial` used with no lights | Add `AmbientLight` + `DirectionalLight` |
| **Camera inside geometry** | Camera at `(0,0,0)` matching object position | Position camera at `(0, 2, 8)` and call `camera.lookAt(0, 0, 0)` |
| **No scroll animation** | Body has no scroll height (`maxScroll = 0`) | Ensure `.scroll-section` elements have `min-height: 100vh` |
| **Buttons unclickable** | Canvas z-index above DOM or missing pointer-events | Set canvas `pointer-events: none; z-index: 2/3;` and cards `pointer-events: auto;` |
| **Disc on a box** | Circular `LatheGeometry` roof on square body | Use `ConeGeometry(radius, height, 4)` rotated 45° for square bodies |
| **Washed out highlights** | Missing ACES tone mapping | Set `renderer.toneMapping = THREE.ACESFilmicToneMapping` |
| **Mobile cutoff** | Fixed vertical FOV on narrow screens | Apply `camera.fov = baseFov / aspect * 0.78` when `aspect < 1.0` |
| **Mobile crash during scrub** | Preloaded all 1080p frames into memory | Use windowed LRU cache (`FrameCache`) derived from budget formula |
| **Stepped frame scrub** | Integer frame indexing | Implement sub-frame alpha crossfading via `drawSubFrame()` |
| **CORS tainted canvas** | Remote CDN missing access headers | Configure `Access-Control-Allow-Origin: *` on asset host |
| **Audio popping** | Setting gain to 0 instantly | Use `gain.setTargetAtTime(0, audioCtx.currentTime, 0.15)` |

---

## 8. Version Compatibility Notes

- **Modern ES Module (r152+)**: Use `renderer.outputColorSpace = THREE.SRGBColorSpace;` and `texture.colorSpace = THREE.SRGBColorSpace;`.
- **Claude.ai Artifact Sandbox (r128)**: Use `renderer.outputEncoding = THREE.sRGBEncoding;`. Do not use `outputColorSpace`.
- **Canvas 2D Frame Scrubber**: Use `createImageBitmap(blob)` for off-thread decode; fall back to `new Image()` where unsupported.
