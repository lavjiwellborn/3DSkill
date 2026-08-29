# Scene Templates

Three complete scene mood presets. Pick the one closest to the user's brief and adapt it — don't build a lighting/atmosphere rig from scratch every time. Each template specifies the key values to set; combine with the geometry from `procedural-geometry.md` and the scroll choreography from `scroll-choreography.md`.

---

## 1. "Shrine at Dusk" — warm, dramatic, reverent

**Best for:** temples, pagodas, shrines, cultural landmarks, anything with a spiritual/historical mood. The golden-hour warmth makes stone and wood materials look their best.

### Renderer
```js
renderer.toneMapping = THREE.ACESFilmicToneMapping;
renderer.toneMappingExposure = 0.9;                   // slightly darker for mood
renderer.outputColorSpace = THREE.SRGBColorSpace;
renderer.setClearColor(0x1a0e08);
renderer.shadowMap.enabled = true;
renderer.shadowMap.type = THREE.PCFSoftShadowMap;
```

### Lighting
```js
const ambient = new THREE.AmbientLight(0x2a1a10, 0.5);       // warm, dim fill
const key = new THREE.DirectionalLight(0xffcc88, 1.4);        // golden sunset key
key.position.set(4, 6, 3);
key.castShadow = true;
key.shadow.mapSize.set(1024, 1024);
const rim = new THREE.DirectionalLight(0x4466aa, 0.4);        // cool blue backlight
rim.position.set(-5, 4, -6);
const bounce = new THREE.HemisphereLight(0xffaa66, 0x223344, 0.3); // warm sky / cool ground bounce
scene.add(ambient, key, rim, bounce);
```

### Atmosphere
```js
scene.fog = new THREE.FogExp2(0x1a0e08, 0.04);               // warm dark fog
```

### Particles
Floating embers/fireflies — warm, sparse, slowly drifting upward:
```js
const particles = createParticleField(80, 12, 0xffaa44);      // warm orange, low count
// In render loop: slowly drift particles upward
// Update individual positions: positions[i*3+1] += 0.002; wrap if > spread
```

### Scroll mood
- **Camera:** low start, rising orbit to a high reveal angle
- **Object:** gentle upward rise + slow rotation (ease-out for the rise — it should "settle")
- **FOV:** start at 55, narrow to 42 by the end for a telephoto/compressed feel
- **Idle motion:** very subtle float (amplitude ~0.03), slow drift rotation

### Material guidance
- Warm stone/plaster walls: `{ color: 0xede6d6, roughness: 0.85 }`
- Dark roof tiles: `{ color: 0x14171c, roughness: 0.45, metalness: 0.1 }`
- Red structural frame: `{ color: 0x9c2b2b, roughness: 0.55 }`
- Gold accents: `{ color: 0xd4af6a, roughness: 0.3, metalness: 0.75 }`

### Post-processing (if available)
- Bloom: strength 0.3, radius 0.4, threshold 0.8 (subtle glow on gold and ember particles)
- Vignette: darkness 1.3 (strong edge darkening for cinematic framing)

---

## 2. "Floating Product" — clean, minimal, studio-lit

**Best for:** product showcases, tech objects, luxury goods, abstract sculptures, SaaS hero sections. The "Apple product page" look — clean white/gray void, perfect lighting, object is the entire focus.

### Renderer
```js
renderer.toneMapping = THREE.ACESFilmicToneMapping;
renderer.toneMappingExposure = 1.2;                   // bright and clean
renderer.outputColorSpace = THREE.SRGBColorSpace;
renderer.setClearColor(0xf0f0f0);                     // light gray, not pure white
renderer.shadowMap.enabled = true;
renderer.shadowMap.type = THREE.PCFSoftShadowMap;
```

### Lighting
```js
const ambient = new THREE.AmbientLight(0xffffff, 0.5);        // neutral white fill
const key = new THREE.DirectionalLight(0xffffff, 1.0);         // clean white key
key.position.set(3, 8, 5);
key.castShadow = true;
key.shadow.mapSize.set(2048, 2048);                            // sharper shadow for product
const fill = new THREE.DirectionalLight(0xe8e8ff, 0.4);        // slightly cool fill
fill.position.set(-4, 4, -2);
const bottom = new THREE.DirectionalLight(0xffeedd, 0.2);      // warm uplighting
bottom.position.set(0, -3, 2);
scene.add(ambient, key, fill, bottom);
```

### Atmosphere
```js
// Minimal or no fog — clean studio look
// If you want subtle depth, use very light fog:
scene.fog = new THREE.FogExp2(0xf0f0f0, 0.015);
```

### Ground
Shadow catcher only — no visible ground:
```js
const shadowPlane = new THREE.Mesh(
  new THREE.PlaneGeometry(20, 20),
  new THREE.ShadowMaterial({ opacity: 0.2 })           // very subtle shadow
);
shadowPlane.rotation.x = -Math.PI / 2;
shadowPlane.receiveShadow = true;
scene.add(shadowPlane);
```

### Particles
None, or extremely sparse dust motes for subtle depth:
```js
// Optional — only if the scene feels too sterile
const particles = createParticleField(40, 10, 0xcccccc);
```

### Scroll mood
- **Camera:** tight orbit, almost level with the object, slow deliberate rotation
- **Object:** minimal movement — the camera does the work. Maybe a slight Y-axis rise over the full scroll.
- **FOV:** start at 45 (telephoto-ish), hold steady or narrow slightly
- **Idle motion:** almost imperceptible rotation drift (amplitude ~0.005), no float — product should feel "placed" not "floating"

### Material guidance
- Clean surfaces: `{ color: 0xfafafa, roughness: 0.1, metalness: 0.0 }` (glossy white)
- Metal parts: `{ color: 0x888888, roughness: 0.15, metalness: 0.9 }`
- Accent color: one bold color for brand identity, applied to one or two small elements
- Use `envMap` (see procedural environment in `polish-and-performance.md`) — glossy products need reflections to look real

### Post-processing (if available)
- Bloom: strength 0.15, radius 0.3, threshold 0.9 (barely there — just a hint of highlight glow)
- No vignette (clean studio doesn't darken edges)

---

## 3. "Night Cityscape" — moody, electric, urban

**Best for:** cityscapes, cyberpunk/tech themes, nightscapes, tower/skyscraper showcases, gaming, anything with an "after dark" energy. Blue-orange contrast, emissive windows, dramatic fog.

### Renderer
```js
renderer.toneMapping = THREE.ACESFilmicToneMapping;
renderer.toneMappingExposure = 0.7;                   // dark and moody
renderer.outputColorSpace = THREE.SRGBColorSpace;
renderer.setClearColor(0x050510);                      // near-black with blue tint
renderer.shadowMap.enabled = true;
renderer.shadowMap.type = THREE.PCFSoftShadowMap;
```

### Lighting
```js
const ambient = new THREE.AmbientLight(0x0a0a20, 0.3);        // very dim blue fill
const key = new THREE.DirectionalLight(0x4488ff, 0.8);         // cool blue moonlight
key.position.set(-3, 10, 5);
key.castShadow = true;
const accent = new THREE.PointLight(0xff6622, 1.5, 15);       // warm orange street-level accent
accent.position.set(2, 1, 3);
const neon = new THREE.PointLight(0xff0066, 0.8, 10);          // magenta neon accent
neon.position.set(-3, 2, -1);
scene.add(ambient, key, accent, neon);
```

### Atmosphere
```js
scene.fog = new THREE.FogExp2(0x050510, 0.06);                // thick dark fog
```

### Sky
Gradient dome with dark blue to deep purple:
```js
// Use the sky dome recipe from polish-and-performance.md with these gradient stops:
// 0.0: '#020208' (near-black top)
// 0.3: '#0a0a30' (deep navy)
// 0.7: '#1a0a20' (dark purple)
// 1.0: '#100808' (warm dark horizon)
```

### Particles
Sparse, slow-drifting light motes (dust in streetlight, distant headlights):
```js
const particles = createParticleField(120, 20, 0x8888ff);     // cool blue-white
```

### Scroll mood
- **Camera:** fly-through or sweeping arc at medium height, weaving between buildings
- **Object:** buildings are static; camera does all the work via a spline path
- **FOV:** wider start (60), useful for establishing shots of a cityscape
- **Idle motion:** subtle camera sway (amplitude ~0.02), particle drift

### Material guidance
- Building walls: `{ color: 0x1a1a2a, roughness: 0.8 }` (dark matte)
- Windows: `{ color: 0x000000, emissive: 0xffcc66, emissiveIntensity: 2.0 }` (lit warm glow — works with bloom)
- Metal/glass: `{ color: 0x222233, roughness: 0.1, metalness: 0.9 }` (reflective dark)
- Neon signs: `{ color: 0x000000, emissive: 0xff0066, emissiveIntensity: 4.0 }` (hot pink/cyan for bloom)
- Ground: `{ color: 0x111115, roughness: 0.4, metalness: 0.3 }` (wet asphalt look)

### Post-processing (if available)
- Bloom: strength 0.6, radius 0.5, threshold 0.6 (strong — this is the signature look for neon/window glow)
- Vignette: darkness 1.5 (heavy edge darkening for focus)

---

## Adapting templates

These are starting points. To adapt:

1. **Copy the renderer + lighting + fog values** as-is for the initial setup.
2. **Swap in your geometry** — the template doesn't dictate what object you build, only how it's lit and atmosphered.
3. **Tune exposure** up/down to taste — this is the first thing to adjust if it feels too dark/bright.
4. **Adjust fog density** — lower for more visible depth, higher for more mystery/mood.
5. **Pick a scroll pattern** from `scroll-choreography.md` that matches the template's suggested mood.

**Mixing templates:** You can also blend two templates across scroll sections — e.g. start with "Shrine at Dusk" warm tones in the hero section, then transition lighting toward "Night Cityscape" cool tones as the user scrolls deeper. Animate light colors and fog density with the same keyframe/timeline system used for object transforms.
