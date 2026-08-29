# Color & Lighting Composition

This reference covers color composition decisions and lighting setup. These are design decisions made *before* writing any Three.js code — choosing wrong here cannot be fixed with better geometry or smoother easing.

---

## The Quality Bar: Art-Directed, Not Photorealistic

Pure procedural primitive geometry rendered live in a browser — no modeled assets, no photographic textures — **cannot achieve photorealism**. Photorealism requires high-poly scanned assets and offline path-tracing, both incompatible with a live scroll site. Do not chase that bar.

The achievable, honest target is: **art-directed composition** — deliberate color relationships, correct lighting response, real tone mapping, and smooth motion. A scene that reads as *designed* rather than *generated*. This distinction matters because chasing "ultra-realistic" with primitives produces disappointment every time.

---

## 1. The 60/30/10 Rule

Every scene divides its color budget into three tiers. This is not a style choice — it is a structural constraint that prevents the "random colorful demo" problem.

| Tier | Proportion | Assigned To |
| :--- | :--- | :--- |
| **Dominant** | ~60% | Background, sky, fog, void — the color the eye rests in |
| **Structure** | ~30% | The hero object's main material surfaces |
| **Accent** | ~10% | Emissive details, CTA elements, highlights, one edge light |

**The accent tier is singular.** One deliberate hue used sparingly — not a rainbow of glowing elements competing for attention. When you see cyan windows, magenta rim lights, orange engine glow, and green particles all in the same frame, the composition has no accent — it has chaos.

### What to Assign to Each Tier

- **Dominant (60%)**: `renderer.setClearColor()`, `scene.fog`, any large background planes. Pick a color with real value depth — not pure `#000000`, not pure `#ffffff`. A near-black with a hue (`#10171A`, `#1B1330`) reads as considered; pure black reads as unfinished.
- **Structure (30%)**: The primary `MeshStandardMaterial` color on the hero object's largest surfaces. Should have clear value contrast with the dominant tier.
- **Accent (10%)**: One `emissive` color, one rim light hue, or one UI highlight color. Not all three simultaneously.

---

## 2. Anti-Cliché Reference List

These combinations are the reliable signals of AI-generated WebGL output. Avoid them as defaults:

- **Black void + cyan glow**: `#000000` background with `emissive: 0x00ffff` objects, nothing else in the composition. There is no 60/30 — just black and one saturated hue.
- **Neon text on black with blur/glow filter**: CSS `text-shadow` or `filter: blur()` on bright-colored text over a dark background is a UI cliché, not a design choice.
- **Multi-accent emissive**: Cyan thrusters AND magenta rim AND orange engine core AND green particles. Pick one accent and commit.
- **Bokeh particles with no color relationship**: Floating colored dots whose hue has nothing to do with the object or environment color. If particles are present, their color should belong to the palette's accent tier.
- **Unvaried flat-black metallic objects**: `roughness: 0, metalness: 1` on a dark object with no environment map produces a mirror-black artifact — not a premium material. Metallic materials need an environment map to read as metallic. See §5 of `polish-and-performance.md`.

---

## 3. Named Starter Palettes

These are starting points, not mandates. Adjust values based on the specific scene — but keep the 60/30/10 ratios intact.

### Palette A: Ethereal Dusk (shrine, temple, cultural monument)

| Tier | Value | Notes |
| :--- | :--- | :--- |
| **Dominant (60%)** | `#1B1330` → `#3B2350` | Deep indigo-plum, CSS gradient on body; fog matches at `#1B1330` |
| **Structure (30%)** | `#8B4A3E` | Muted terracotta for walls; `#D4AF6A` for structural trim |
| **Accent (10%)** | `#D9A55C` | Warm gold — finials, one emissive, glass card border |

Three.js setup:
```js
renderer.setClearColor(0x1b1330);
scene.fog = new THREE.FogExp2(0x1b1330, 0.04);

// Key: warm golden-hour sun
const key = new THREE.DirectionalLight(0xffd194, 1.4);
key.position.set(5, 8, 4);

// Rim: single cool complement — one accent hue
const rim = new THREE.DirectionalLight(0x7bb8d4, 0.5);
rim.position.set(-4, 3, -6);

// Ambient: low fill, inherits dominant hue
const ambient = new THREE.AmbientLight(0x2a1e28, 0.5);
```

Materials in this palette keep `roughness` between 0.5–0.9 on non-metallic surfaces. The only metallic element is the gold finial (`roughness: 0.3, metalness: 0.75`).

---

### Palette B: Studio Minimal (product showcase, hardware, SaaS)

Pick **one** background tone — light or dark — and do not mix them. Light and dark studio in the same frame produces no composition.

| Tier | Light Variant | Dark Variant |
| :--- | :--- | :--- |
| **Dominant (60%)** | `#EDEBE6` | `#1C1C1E` |
| **Structure (30%)** | `#C9CACB` (brushed neutral) | `#2C2C2E` (anodized dark) |
| **Accent (10%)** | Single brand hue only on CTA/UI — e.g. `#0071E3` | Same rule |

Three.js setup (dark variant):
```js
renderer.setClearColor(0x1c1c1e);
scene.fog = new THREE.FogExp2(0x1c1c1e, 0.012); // very light fog

// Key: neutral-warm studio softbox overhead
const key = new THREE.DirectionalLight(0xffffff, 1.1);
key.position.set(4, 9, 5);

// Fill: cool side — keeps the structure tier readable
const fill = new THREE.DirectionalLight(0xe2e8f0, 0.4);
fill.position.set(-5, 3, -3);

// No rim light in studio — rim would add a second accent hue
const ambient = new THREE.AmbientLight(0xffffff, 0.5);
```

**In this palette, the accent hue (`#0071E3` or your brand color) belongs only on UI elements** — a hover state, a CTA button, a badge. Never use it as an emissive on the 3D object or as an ambient glow filling the scene.

---

### Palette C: Nocturne Cityscape (dark tech, architecture, modern)

| Tier | Value | Notes |
| :--- | :--- | :--- |
| **Dominant (60%)** | `#10171A` → `#1B2A2E` | Deep teal-charcoal |
| **Structure (30%)** | `#6B6259` | Warm stone/concrete — deliberate warm/cool contrast with dominant |
| **Accent (10%)** | `#F2A94E` | Warm amber window-light only — not cyan, not magenta |

Three.js setup:
```js
renderer.setClearColor(0x10171a);
scene.fog = new THREE.FogExp2(0x10171a, 0.035);

// Key: moonlight from above — slightly warm white
const key = new THREE.DirectionalLight(0xd4e8f0, 1.2);
key.position.set(-3, 10, 5);

// Rim: absent or very subtle — the accent is amber windows, not a rim hue
const ambient = new THREE.AmbientLight(0x0d1a1e, 0.4);

// Window emissives: this is where accent color lives
// windowMat.emissive = new THREE.Color(0xf2a94e);
// windowMat.emissiveIntensity = 1.8;
```

The accent `#F2A94E` appears **only** on window emissives and nowhere else — not on the rim light, not on particles, not on CSS badges.

---

## 4. ACES Filmic Tone Mapping

The single highest-leverage, cheapest technique for "cinematic" vs. "flat default Three.js" look. Without it, highlights clip to harsh 255/255/255 white and shadows lose depth. Apply immediately after creating the renderer:

```js
renderer.toneMapping = THREE.ACESFilmicToneMapping;
renderer.toneMappingExposure = 1.1; // Tune 0.8–1.3 by eye: lower = darker/moodier, higher = brighter/cleaner
renderer.outputColorSpace = THREE.SRGBColorSpace;
```

**Version caveat**: Three.js before r152 used a different API surface:
```js
// r128 and earlier (Claude.ai artifact sandbox pinned version):
renderer.outputEncoding = THREE.sRGBEncoding;
// NOT renderer.outputColorSpace — that property does not exist before r152
```

Check which version you are targeting before assuming. `THREE.ACESFilmicToneMapping` itself has been stable since r118 — only the color-space output API changed.

Also apply to any `CanvasTexture` generated in-memory:
```js
const tex = new THREE.CanvasTexture(offscreenCanvas);
tex.colorSpace = THREE.SRGBColorSpace; // r152+; omit for r128
```

---

## 5. Warm/Cool Contrast in Lighting

The "cinematic cross-lighting" look comes from a deliberate warm/cool split between the key light and at least one other light source. The eye reads this as natural — outdoor sunlight (warm) against sky (cool), or interior lamp (warm) against moonlight (cool). Monochromatic lighting (all lights the same color temperature) reads as artificial and flat.

**Template: warm key + cool rim**
```js
const key = new THREE.DirectionalLight(0xffd194, 1.3); // warm golden
key.position.set(5, 8, 4);

const rim = new THREE.DirectionalLight(0x7dd3fc, 0.5); // cool sky blue
rim.position.set(-4, 3, -6);
```

**Template: cool key + warm fill** (moonlight scene, nocturne)
```js
const key = new THREE.DirectionalLight(0xd4e8f0, 1.2); // cool moonlight
key.position.set(-3, 10, 5);

const fill = new THREE.DirectionalLight(0xf59e0b, 0.3); // warm ambient bounce
fill.position.set(3, 0, 3);
```

Intensity ratio: key light is typically 2–3x the intensity of rim or fill. If they are equal, neither reads as the primary source.

---

## 6. When to Add Fog

`FogExp2` density guide for common scene scales (hero object 3–8 Three.js units tall):

| Mood | Density | Effect |
| :--- | :--- | :--- |
| Airy / minimal | `0.008–0.015` | Near-invisible, just hides far geometry |
| Moderate depth | `0.025–0.04` | Clear mid-distance, dark atmosphere at edges |
| Dense / oppressive | `0.06–0.10` | Object emerges from void, strong depth |

Always match fog color to `renderer.setClearColor()`. If they differ, geometry fades to a hue different from the background — a common source of ugly edges.
