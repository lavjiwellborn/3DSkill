# 🏛️ Procedural 3D Scroll Sites

[![Claude Skill](https://img.shields.io/badge/Claude-Skill-6366f1?style=for-the-badge&logo=anthropic)](https://claude.ai)
[![Three.js](https://img.shields.io/badge/Three.js-r170+-black?style=for-the-badge&logo=three.js)](https://threejs.org/)
[![WebGL](https://img.shields.io/badge/WebGL-Live%20Rendered-38bdf8?style=for-the-badge)](https://developer.mozilla.org/en-US/docs/Web/API/WebGL_API)
[![License: MIT](https://img.shields.io/badge/License-MIT-emerald?style=for-the-badge)](./LICENSE)

> **Build cinematic, scroll-driven 3D websites where the entire 3D scene (buildings, temples, product objects, environments) is generated from code and rendered live, pixel-by-pixel, by the GPU — with the object's rotation/position/camera tied directly to scroll — instead of extracting AI video clips into playback frames.**

---

## ⚡ Core Philosophy: Real 3D from Code, No Video Frames

This skill replaces the fragile AI video-to-frames workflow (Kling, Seedance, Runway, Sora frame extraction) with **pure procedural Three.js/WebGL geometry, in-memory canvas textures, physical lighting, and mathematical motion choreography**.

The quality bar is **art-directed composition**, not unachievable photorealism:
- **60/30/10 Color Discipline**: Controlled color budget without random multi-colored neon glows.
- **Physical Lighting Response**: Warm key vs. cool rim cross-lighting + ground shadow catcher.
- **ACES Filmic Tone Mapping**: Highlight roll-off and dark value depth.
- **Silhouette-First Geometry**: Recognizable primitives with strict plan-shape matching.
- **Damped Non-Linear Easing**: `smoothstep` / `smootherstep` curves + subtle idle breathing.

---

## 📚 Master Reference Guides

| Reference Guide | Core Topic |
| :--- | :--- |
| **[`SKILL.md`](./SKILL.md)** | **Master Instruction Manual**: Architecture, 10-phase production build order, 9-point self-check audit gate, and common failure modes. |
| **[`references/color-and-lighting.md`](./references/color-and-lighting.md)** | **Color & Lighting Discipline**: The 60/30/10 rule, anti-cliché ban list, 3 named starter palettes (*Ethereal Dusk*, *Studio Minimal*, *Nocturne Cityscape*), ACES tone mapping setup with version caveat, and warm/cool cross-lighting. |
| **[`references/procedural-geometry.md`](./references/procedural-geometry.md)** | **Geometric Blueprints**: Lathe trick for curved profiles, plan-shape coherence rule, and 4 worked blueprints (Japanese Pagoda, Modern Minimalist Tower, Low-Poly Floating Island, Sacred Torii Gate). |
| **[`references/procedural-materials.md`](./references/procedural-materials.md)** | **Zero-Asset Textures**: In-memory HTML5 Canvas 2D texture generators (wood grain, brushed metal, window facade grid, roof tiles), PMREMGenerator procedural environment reflections, and physical roughness/metalness table. |
| **[`references/scroll-choreography.md`](./references/scroll-choreography.md)** | **Motion & Easing Mechanics**: Normalized scroll progress driver, first-order IIR damping loop, smoothstep & smootherstep formulas, keyframe timeline interpolation, non-accumulative mouse parallax, and idle breathing micro-motion. |
| **[`references/polish-and-performance.md`](./references/polish-and-performance.md)** | **Polish & Performance**: 3-point cross-lighting rig, invisible contact shadow catcher plane, mobile portrait FOV compensation, zero-allocation loop rules, and GPU memory disposal matrix. |
| **[`references/scaffold-and-overlay.md`](./references/scaffold-and-overlay.md)** | **Complete Scaffolds**: Copy-paste standalone HTML5 and React/Next.js scaffolds with ACES tone mapping, shadow catcher, glassmorphic card CSS, `<noscript>` fallback, and loading safety timeout. |
| **[`references/troubleshooting-and-faq.md`](./references/troubleshooting-and-faq.md)** | **Diagnostic Matrix**: Fast fixes for blank canvas, camera inside geometry, missing scroll height, unclickable text cards, washed-out colors, mobile portrait clipping, and shadow acne. |

---

## 🎨 Turnkey Standalone Runnable Demos

Explore the complete standalone HTML examples in [`examples/`](./examples/):
* **[`examples/shrine-at-dusk.html`](./examples/shrine-at-dusk.html)**: 3-Tier Japanese Pagoda with Ethereal Dusk palette, in-memory wood grain & roof tile textures, PMREM reflections, and sunset embers.
* **[`examples/cyberpunk-megastructure.html`](./examples/cyberpunk-megastructure.html)**: Nocturne Modern Tower with Nocturne Cityscape palette, in-memory window facade textures, and corkscrew camera orbit.
* **[`examples/luxury-product-showcase.html`](./examples/luxury-product-showcase.html)**: Apex Pro One with Studio Minimal palette, in-memory brushed metal texture, PMREM reflections, and unibody chassis.
* **[`examples/floating-island-nature.html`](./examples/floating-island-nature.html)**: Aethelgard Island with low-poly faceted rock strata, articulated bonsai trunk, and foliage clusters.
* **[`examples/6dof-camera-flythrough.html`](./examples/6dof-camera-flythrough.html)**: Catmull-Rom spline camera flying through an architectural complex with lookahead tracking and banking Dutch roll.
* **[`examples/drone-aerial-descent.html`](./examples/drone-aerial-descent.html)**: Autonomous drone aerial flight through a cyberpunk neon canyon into a hillside villa with full HUD telemetry.

---

## 📐 Mandatory 9-Point Self-Check Gate

1. **Squint Silhouette Test**: Does the object read instantly as the intended subject at a glance without reading text?
2. **Plan-Shape Coherence**: Do roofs, caps, and tiers match the footprint shape beneath them (square on square, round on round)?
3. **Deep Overhang Ratio**: Do eaves, ledges, or bevels extend significantly beyond the body beneath them (overhang factor 1.4–1.8×)?
4. **Color & Material Contrast**: Does the scene obey the 60/30/10 rule with high value contrast between trim and core panels?
5. **Continuous Scroll Coverage**: Is there meaningful visual transformation across the entire 0→1 scroll range without static dead zones?
6. **Tone-Mapping Calibration**: Is `ACESFilmicToneMapping` and correct color space (`SRGBColorSpace` / `sRGBEncoding`) active?
7. **Text/UI Contrast**: Do text panels over the 3D scene maintain WCAG AA (4.5:1) contrast against their busiest background moment?
8. **Mobile Portrait Framing**: Does camera FOV adjust for narrow aspect ratios (`aspect < 1.0`) so the subject is never clipped on mobile screens?
9. **Idle Motion Present**: Does the scene maintain subtle harmonic breathing when scrolling pauses, while strictly respecting `prefers-reduced-motion`?

---

## 📦 Installation in Claude

1. Download [`procedural-3d-scroll-sites.skill`](./procedural-3d-scroll-sites.skill).
2. Install it in your Claude skills settings or place it in your assistant's skill directory.
3. Prompt your assistant:
   > *"Build a cinematic scroll-driven 3D landing page with a procedural Japanese shrine hero and dark mode aesthetic."*

---

## 📄 License

MIT License — Copyright (c) 2025-2026 Lavji Wellborn
