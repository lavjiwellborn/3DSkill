# Golden Path: End-to-End Build Walkthrough

This document walks through building one complete procedural 3D scroll site from a user prompt to finished output, step by step. Every subsystem is connected in the right order. Follow this once to understand how the entire skill fits together.

---

## The Prompt

> *"Build a cinematic dark-mode landing page with a floating sci-fi spaceship that the user scrolls around. Add glass card text panels."*

---

## Phase 1: Route the Prompt (30 seconds)

Open [`decision-router.md`](./decision-router.md) and classify:

- **Mode**: A (Pure Procedural 3D) — no image frames mentioned
- **Geometry**: Archetype B (Sci-Fi Vessel) from `procedural-geometry.md`
- **Palette**: §2 "Cyberpunk Obsidian" from `lighting-and-color-palettes.md`
- **Scene Template**: §3 "Night Cityscape" from `scene-templates.md` (closest mood)
- **Camera**: Orbital Arc (user said "scrolls around")
- **Materials**: Brushed metal hull + emissive thruster glow

---

## Phase 2: Copy the Scaffold (2 minutes)

Open [`scaffold-and-overlay.md`](./scaffold-and-overlay.md) §1 and copy the complete standalone HTML5 scaffold. This gives you a working file with:

```
✅ WebGLRenderer with ACES tone mapping + sRGB
✅ 3-point cross-lighting rig (warm key + cool rim + bounce)
✅ Shadow catcher plane
✅ Scroll progress driver with damping
✅ Cursor parallax tracking
✅ Mobile FOV compensation
✅ Loading screen with spinner
✅ Glass card CSS with backdrop-filter
✅ IntersectionObserver for card reveal animations
✅ prefers-reduced-motion detection
```

**At this point the page loads, shows a loading screen, then reveals an empty lit scene. Scrolling works but moves nothing. This is correct — you haven't added geometry or choreography yet.**

---

## Phase 3: Build the Hero Object (10 minutes)

Open [`procedural-geometry.md`](./procedural-geometry.md) §2.B (Sci-Fi Exploration Vessel) and copy the `buildSciFiVessel()` function into your `<script>`.

```js
// Add to scene setup, after lighting
const vessel = buildSciFiVessel();
vessel.castShadow = true;
vessel.traverse(child => {
  if (child.isMesh) {
    child.castShadow = true;
    child.receiveShadow = true;
  }
});
scene.add(vessel);

// Store reference for animation
const heroGroup = vessel;
```

**At this point the vessel appears in the scene, lit by the 3-point rig, casting shadows on the ground plane. It doesn't move yet.**

---

## Phase 4: Apply Procedural Textures (5 minutes)

Open [`procedural-materials-and-textures.md`](./procedural-materials-and-textures.md) and apply brushed metal to the hull:

```js
// Generate brushed metal texture (zero external assets)
const brushedMetalMap = createBrushedMetalTexture(512, 512);

// Apply to hull materials
const hullMat = new THREE.MeshStandardMaterial({
  color: 0x0f172a,
  roughness: 0.35,
  metalness: 0.85,
  roughnessMap: brushedMetalMap,
});
```

For the thruster glow, use an emissive material:
```js
const thrusterMat = new THREE.MeshStandardMaterial({
  color: 0x000000,
  emissive: 0x00f0ff,
  emissiveIntensity: 2.5,
});
```

**At this point the vessel has surface detail and glowing thrusters. It still doesn't move.**

---

## Phase 5: Apply the Lighting Palette (3 minutes)

Open [`lighting-and-color-palettes.md`](./lighting-and-color-palettes.md) §2 "Cyberpunk Obsidian" and replace the scaffold's default lighting values:

```js
renderer.setClearColor(0x05070f);
scene.fog = new THREE.FogExp2(0x05070f, 0.04);

// Key: bright cyan moonlight
keyLight.color.setHex(0x38bdf8);
keyLight.intensity = 1.2;
keyLight.position.set(-4, 9, 5);

// Rim: hot magenta back rim
rimLight.color.setHex(0xf43f5e);
rimLight.intensity = 0.8;
rimLight.position.set(5, 2, -6);

// Ambient: low navy blue shadow floor
ambient.color.setHex(0x0f172a);
ambient.intensity = 0.4;
```

**At this point the scene has the cyberpunk obsidian mood — dark void, cyan/magenta cross-lighting, fog depth.**

---

## Phase 6: Wire Scroll Choreography (5 minutes)

Open [`scroll-choreography.md`](./scroll-choreography.md) and use the orbital arc pattern. The scaffold already has `currentProgress` and `mouseCurrentX/Y` being damped in the render loop. Add the choreography:

```js
function applyScrollChoreography(progress) {
  // Non-linear easing for organic feel
  const t = smoothstep(progress);

  // Orbital arc: camera sweeps 270° around the vessel
  const theta = t * Math.PI * 1.5 + Math.PI * 0.25; // Start at 45°, end at 315°
  const radius = 8 - t * 2; // Tighten orbit as we scroll deeper
  const height = 3 + Math.sin(t * Math.PI) * 3; // Rise then descend

  camera.position.x = Math.sin(theta) * radius;
  camera.position.z = Math.cos(theta) * radius;
  camera.position.y = height;

  // Add cursor parallax offset
  camera.position.x += mouseCurrentX * 0.4;
  camera.position.y += mouseCurrentY * 0.25;

  // Always look at the vessel center (with slight upward bias)
  camera.lookAt(0, 1, 0);

  // Idle breathing when scroll stops
  const time = performance.now() * 0.001;
  heroGroup.rotation.y += Math.sin(time * 0.5) * 0.0003;
  heroGroup.position.y += Math.sin(time * 0.8) * 0.0002;
}
```

Call `applyScrollChoreography(currentProgress)` inside your `animate()` function.

**At this point scrolling orbits the camera around the vessel with smooth damping, cursor moves tilt the view, and the vessel breathes when idle. The core 3D experience is working.**

---

## Phase 7: Add HTML Overlay Content (5 minutes)

The scaffold already has `.scroll-section` and `.glass-card` CSS. Add your content sections:

```html
<main class="scroll-container">
  <section class="scroll-section" style="justify-content: center; text-align: center; align-items: flex-end; padding-bottom: 12vh;">
    <div class="glass-card" style="background: transparent; border: none; box-shadow: none;">
      <span class="badge">Deep Space</span>
      <h1>Prometheus VII</h1>
      <p>Scroll to explore the next-generation exploration vessel.</p>
    </div>
  </section>

  <section class="scroll-section">
    <div class="glass-card">
      <h2>Modular Hull</h2>
      <p>Brushed titanium fuselage with integrated sensor arrays and zero-emission plasma thrusters.</p>
    </div>
  </section>

  <section class="scroll-section">
    <div class="glass-card">
      <h2>Autonomous Navigation</h2>
      <p>AI-guided flight computer processes stellar parallax for real-time course correction at 0.3c.</p>
    </div>
  </section>

  <section class="scroll-section">
    <div class="glass-card">
      <h2>Mission Ready</h2>
      <p>Cleared for deep interstellar reconnaissance. Launch window confirmed.</p>
    </div>
  </section>
</main>
```

**At this point the full page experience is working: scroll orbits the camera, glass cards fade in with IntersectionObserver, text is readable over the 3D scene.**

---

## Phase 8: Final Polish Pass (3 minutes)

Open [`polish-and-performance.md`](./polish-and-performance.md) and verify:

- [x] ACES tone mapping active (§1) — already in scaffold
- [x] 3-point lighting with warm/cool contrast (§2) — applied in Phase 5
- [x] Contact shadow catcher (§3) — already in scaffold
- [x] Mobile FOV compensation (§4) — already in scaffold
- [ ] Procedural environment map for metallic reflections (§5) — **add now**
- [ ] Optional bloom for emissive thrusters (§6) — **add if desired**
- [x] Zero-allocation render loop (§7) — verify no `new` in `animate()`

```js
// Add procedural environment map (makes metal surfaces reflect virtual lights)
setupProceduralEnvironment(renderer, scene);
```

---

## Phase 9: Run the 12-Point Self-Check Gate

Open [`../SKILL.md`](../SKILL.md) §5 and verify every item:

| # | Check | Status |
| :--- | :--- | :--- |
| 1 | Squint silhouette — reads as spaceship? | ✅ |
| 2 | Plan-shape coherence — hull components aligned? | ✅ |
| 3 | Deep overhang — wings/fins extend past body? | ✅ |
| 4 | Color/material contrast — hull vs thrusters vs cockpit? | ✅ |
| 5 | ACES tone mapping + sRGB active? | ✅ |
| 6 | Grounding shadow on shadow catcher? | ✅ |
| 7 | Non-linear easing on scroll? | ✅ (smoothstep) |
| 8 | Full 0→1 scroll coverage? | ✅ (270° orbit) |
| 9 | Idle breathing when scroll stops? | ✅ |
| 10 | Mobile portrait FOV compensation? | ✅ |
| 11 | `prefers-reduced-motion` respected? | ✅ (scaffold handles) |
| 12 | Text contrast over 3D background? | ✅ (glass card with backdrop-filter) |

**All 12 checks pass → output is finished.**

---

## Phase 10: If Something Breaks

Open [`troubleshooting-and-faq.md`](./troubleshooting-and-faq.md) and match the symptom. Fix, then re-run the self-check gate.

---

## Total Build Time: ~35 minutes

By following this golden path, you produce a polished, cinematic, production-quality output every time — not a demo, not a prototype, a finished website experience.
