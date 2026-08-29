# Decision Router — Prompt → Build Plan in 60 Seconds

**READ THIS FILE FIRST before opening any other reference.** It maps any user prompt to the exact files you need, in the exact order you should read them, so you never waste time reading irrelevant material or miss a critical subsystem.

---

## Step 0: Quick Prompt Classification

Read the user's prompt and classify it into exactly one rendering mode:

```
USER PROMPT
    │
    ├─ Mentions "image frames", "frame sequence", "scrub images",
    │  "I have frames", "Apple-style scrub", or provides image URLs
    │  ──────────────────────────────────────────────────────────────► MODE B (Image Scrubber)
    │
    ├─ Mentions image frames AND wants interactive 3D overlays
    │  (particles, annotations, floating objects on top of frames)
    │  ──────────────────────────────────────────────────────────────► MODE C (Hybrid)
    │
    └─ Everything else: "3D animated", "scroll-driven 3D", "procedural",
       "cinematic landing page", "Three.js", "WebGL", "3D hero section",
       product showcase, temple, city, sci-fi, nature, abstract
       ──────────────────────────────────────────────────────────────► MODE A (Procedural 3D)
```

---

## Mode A: Pure Procedural 3D — Full Build Route

Follow these steps **in order**. Each step references exactly one file.

### Step 1: Copy the Production Scaffold
📄 **Read**: [`scaffold-and-overlay.md`](./scaffold-and-overlay.md) → Section 1 (Standalone HTML5 Scaffold)
- Copy the entire HTML template as your starting point
- This gives you: renderer with ACES tone mapping, 3-point lighting, shadow catcher, scroll damping, cursor parallax, mobile FOV compensation, loading screen, and glass card CSS

### Step 2: Select a Geometry Blueprint
📄 **Read one of**:

| User Wants | Blueprint | File |
| :--- | :--- | :--- |
| Temple, shrine, pagoda, cultural monument | Archetype A: Japanese Pagoda | [`procedural-geometry.md`](./procedural-geometry.md) §2.A |
| Spaceship, sci-fi vessel, mothership | Archetype B: Sci-Fi Vessel | [`procedural-geometry.md`](./procedural-geometry.md) §2.B |
| Skyscraper, tower, cyberpunk megastructure | Archetype C: Cyberpunk Spire | [`procedural-geometry.md`](./procedural-geometry.md) §2.C |
| Phone, laptop, hardware, tech product | Archetype D: Smart Device | [`procedural-geometry.md`](./procedural-geometry.md) §2.D |
| Floating island, low-poly landscape | Archetype E: Floating Island | [`procedural-geometry.md`](./procedural-geometry.md) §2.E |
| DNA, molecules, biotech, genetics | Blueprint F: DNA Helix | [`advanced-procedural-blueprints.md`](./advanced-procedural-blueprints.md) §1 |
| Car, vehicle, automotive, concept car | Blueprint G: Concept Supercar | [`advanced-procedural-blueprints.md`](./advanced-procedural-blueprints.md) §2 |
| Globe, data visualization, SaaS, fintech | Blueprint H: Holo Data Globe | [`advanced-procedural-blueprints.md`](./advanced-procedural-blueprints.md) §3 |
| Greek temple, columns, classical architecture | Blueprint I: Parthenon | [`advanced-procedural-blueprints.md`](./advanced-procedural-blueprints.md) §4 |
| Reactor, tesseract, AI/web3, abstract tech | Blueprint J: Quantum Reactor | [`advanced-procedural-blueprints.md`](./advanced-procedural-blueprints.md) §5 |
| Mountains, terrain, nature, outdoor | Blueprint K: Mountain Range | [`advanced-procedural-blueprints.md`](./advanced-procedural-blueprints.md) §6 |
| **Custom / not listed** | Build from primitives using core principles | [`procedural-geometry.md`](./procedural-geometry.md) §1 (Lathe trick + plan-shape matching) |

### Step 3: Select a Lighting & Color Palette
📄 **Read**: [`lighting-and-color-palettes.md`](./lighting-and-color-palettes.md)

| User Mood / Theme | Palette |
| :--- | :--- |
| Warm, spiritual, cultural, golden-hour, sunset | §1 "Ethereal Dusk" |
| Dark, sci-fi, cyberpunk, neon, gaming | §2 "Cyberpunk Obsidian" |
| Clean, minimal, product showcase, Apple-style, studio | §3 "Apple Minimalist Studio" |
| Ocean, nature, organic, wellness, abstract | §4 "Bioluminescent Deep Sea" |
| Luxury, gold, finance, energy, architecture | §5 "Solar Flare Amber" |

Also read the matching **Scene Template** from [`scene-templates.md`](./scene-templates.md) for renderer settings, fog density, particle style, and scroll mood guidance.

### Step 4: Apply Procedural Surface Materials
📄 **Read**: [`procedural-materials-and-textures.md`](./procedural-materials-and-textures.md)
- Pick textures that match your palette and geometry (brushed metal for tech, wood grain for shrines, cyber grid for sci-fi)
- Apply as `map`, `roughnessMap`, or `normalMap` — never leave all surfaces as flat solid colors

### Step 5: Wire Scroll Choreography & Camera Motion
📄 **Read**: [`scroll-choreography.md`](./scroll-choreography.md) for damping, easing math, cursor parallax, idle breathing
📄 **Read**: [`6dof-scrollytelling-director.md`](./6dof-scrollytelling-director.md) for camera trajectory

| Desired Camera Feel | Trajectory Pattern |
| :--- | :--- |
| Orbit around subject | Orbital Arc (§2 in scroll-choreography) |
| Rising reveal shot | Spiral Ascent (§2.A in 6dof director) |
| Fly-through / architectural tour | Catmull-Rom Spline (§2.B in 6dof director) |
| Dramatic zoom with perspective warp | Vertigo Dolly-Zoom (§2.C in 6dof director) |
| Exploded diagram / assembly | Multi-Axis Pivot (§2.D in 6dof director) |

### Step 6: Polish & Performance Pass
📄 **Read**: [`polish-and-performance.md`](./polish-and-performance.md)
- Verify ACES tone mapping is active (§1)
- Verify 3-point lighting is warm/cool contrasted (§2)
- Verify contact shadow catcher is in place (§3)
- Apply mobile FOV compensation (§4)
- Add procedural environment map for metallic surfaces (§5)
- Optional: add bloom post-processing for emissive elements (§6)
- Pre-allocate render loop variables (§7)

### Step 7: Run the 12-Point Self-Check Gate
📄 **Read**: [`../SKILL.md`](../SKILL.md) §5 — every item must pass before output is considered finished.

### Step 8: If Something Breaks
📄 **Read**: [`troubleshooting-and-faq.md`](./troubleshooting-and-faq.md)
- Match the visual symptom to the diagnostic matrix
- Apply the fix, then re-run the self-check gate

---

## Mode B: Image-Sequence Frame Scrubber — Build Route

### Step 1: Copy the Frame Scrubber Scaffold
📄 **Read**: [`image-sequence-scrubber.md`](./image-sequence-scrubber.md) → Section 2 (Complete Standalone Scaffold)
- This scaffold includes: preloader with progress bar, aspect-ratio cover drawing, DPR retina scaling, scroll progress damping, and glass card overlays

### Step 2: Configure Frame Source
- Replace the `getFrameUrl(index)` function with the user's actual image URL pattern
- Set `frameCount` to match the number of frames
- If user provides an array of URLs, map directly to the `images[]` array

### Step 3: Add HTML Overlay Content
📄 **Read**: [`scaffold-and-overlay.md`](./scaffold-and-overlay.md) → Section 3 (Glass Card CSS Architecture)
- Layer `.scroll-section` blocks with glass cards over the fixed canvas

### Step 4: Run Self-Check Gate (Items 7–10 Apply)
- Items 1–6 (geometry/materials) don't apply to Mode B
- Items 7–10 (scroll coverage, idle motion, mobile framing, easing) still apply

---

## Mode C: Hybrid (3D + Image Sequence) — Build Route

### Step 1: Set Up Both Canvases
📄 **Read**: [`image-sequence-scrubber.md`](./image-sequence-scrubber.md) → Section 3 (Hybrid Mode C)
- Canvas Layer 1 (Back): 2D frame scrubber
- Canvas Layer 2 (Front): Three.js WebGL with `alpha: true`

### Step 2: Build 3D Foreground Objects
Follow Mode A Steps 2–6, but:
- Set `renderer = new THREE.WebGLRenderer({ canvas, alpha: true })`
- Set `renderer.setClearColor(0x000000, 0)` for transparency
- Only add foreground interactive elements (particles, annotations, floating objects)

### Step 3: Synchronize Both Layers
- Both canvases read from the same `currentProgress` damped variable
- Apply 3D transforms and frame index from the same source

### Step 4: Run Full 12-Point Self-Check Gate

---

## Quick Reference: File Purpose Map

| File | Read When... |
| :--- | :--- |
| `decision-router.md` | **Always first** — routes you to everything else |
| `scaffold-and-overlay.md` | Starting any new build (provides the HTML/CSS/JS skeleton) |
| `procedural-geometry.md` | Building the hero 3D object (5 core blueprints) |
| `advanced-procedural-blueprints.md` | Need specialty objects (DNA, car, globe, temple, reactor, mountains) |
| `lighting-and-color-palettes.md` | Setting up lights and colors (5 tested palettes) |
| `scene-templates.md` | Need a complete mood preset (renderer + lighting + fog + scroll mood) |
| `procedural-materials-and-textures.md` | Surfaces look flat or need texture variation |
| `scroll-choreography.md` | Wiring scroll progress to 3D transforms |
| `6dof-scrollytelling-director.md` | Camera needs cinematic movement (orbit, fly-through, vertigo) |
| `polish-and-performance.md` | Final quality pass (tone mapping, shadows, mobile, perf) |
| `image-sequence-scrubber.md` | Mode B or Mode C builds |
| `troubleshooting-and-faq.md` | Something is broken or looks wrong |
| `golden-path-walkthrough.md` | Want a step-by-step tutorial of a complete build |
