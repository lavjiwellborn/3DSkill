# Curated 3D Lighting & Color Palettes

A major cause of amateur WebGL output is selecting random, unharmonious hex colors (e.g. generic `#ff0000` or `#00ff00`). Cinematic 3D requires **color temperature contrast** (warm key vs. cool rim or vice-versa) and **calibrated tone ranges**.

Every palette below provides exact, tested hex values for **Renderer Clear/Fog Color**, **Ambient Fill**, **Key Light**, **Rim Light**, **Bounce Light**, **Primary Materials**, and **Overlay UI**.

---

## 1. "Ethereal Dusk" (Japanese Shrines, Cultural Monuments, Temples)
*Warm golden-hour sunlight against deep twilight indigo shadows.*

| Role | Hex Color | Intensity / Purpose |
| :--- | :--- | :--- |
| **Clear / Fog** | `0x160e18` | Warm twilight deep purple-black |
| **Ambient Fill** | `0x2a1e28` | Dim cool plum fill (`0.5`) |
| **Key Light** | `0xffd194` | Golden sunset directional light (`1.4`, pos: `5, 8, 4`) |
| **Rim Light** | `0x38bdf8` | Cool sky blue rim kicker (`0.6`, pos: `-4, 3, -6`) |
| **Hemisphere Bounce** | `0xffd194` / `0x0f172a` | Golden sky vs dark ground bounce (`0.3`) |
| **Plaster Walls** | `0xede6d6` | Warm cream plaster (`roughness: 0.85`) |
| **Red Lacquer** | `0x9c2b2b` | Deep vermillion frame (`roughness: 0.55`) |
| **Roof Tiles** | `0x14171c` | Dark slate charcoal (`roughness: 0.45, metalness: 0.1`) |
| **Gold Finials** | `0xd4af6a` | Metallic aged gold (`roughness: 0.3, metalness: 0.8`) |
| **Particles** | `0xffaa44` | Warm glowing embers / fireflies |
| **Glass Card UI** | `rgba(22, 14, 24, 0.65)` | Frosted dark plum background |

---

## 2. "Cyberpunk Obsidian" (Sci-Fi, High-Tech, Megastructures, Gaming)
*Deep obsidian shadows electrified by cyan laser lines and hot magenta accents.*

| Role | Hex Color | Intensity / Purpose |
| :--- | :--- | :--- |
| **Clear / Fog** | `0x05070f` | Near-black cyber void |
| **Ambient Fill** | `0x0f172a` | Low navy blue shadow floor (`0.4`) |
| **Key Light** | `0x38bdf8` | Bright cyan moonlight (`1.2`, pos: `-4, 9, 5`) |
| **Rim Light** | `0xf43f5e` | Hot pink/magenta back rim (`0.8`, pos: `5, 2, -6`) |
| **Accent Points** | `0x00f0ff` | Glowing thruster/window emissive (`intensity: 2.5–4.0`) |
| **Hull / Monolith** | `0x0f172a` | Dark carbon matte (`roughness: 0.35, metalness: 0.85`) |
| **Trim Metal** | `0x334155` | Polished titanium (`roughness: 0.2, metalness: 0.9`) |
| **Glass Canopy** | `0x0284c7` | Tinted transparent cyan glass (`opacity: 0.8`) |
| **Particles** | `0x38bdf8` | Cyan digital data motes |
| **Glass Card UI** | `rgba(5, 7, 15, 0.75)` | Dark obsidian frosted backdrop |

---

## 3. "Apple Minimalist Studio" (Luxury Tech Hardware, SaaS Hero, Products)
*Clean, neutral high-key studio lighting with soft diffused contact shadows.*

| Role | Hex Color | Intensity / Purpose |
| :--- | :--- | :--- |
| **Clear Color** | `0xf8fafc` | Ultra-clean soft off-white |
| **Fog** | `0xf8fafc` | Extremely subtle depth fade (`density: 0.015`) |
| **Ambient Fill** | `0xffffff` | Pure neutral white fill (`0.6`) |
| **Key Light** | `0xffffff` | Soft diffused overhead softbox (`1.1`, pos: `4, 9, 5`) |
| **Fill Light** | `0xe2e8f0` | Cool silver side fill (`0.4`, pos: `-5, 3, -3`) |
| **Uplight Bounce** | `0xfff7ed` | Warm ground bounce reflection (`0.25`, pos: `0, -3, 2`) |
| **Anodized Aluminum**| `0x18181b` | Space Gray / Matte Black (`roughness: 0.25, metalness: 0.95`) |
| **Ceramic / Gloss** | `0xfafafa` | Pure white gloss (`roughness: 0.1, metalness: 0.05`) |
| **OLED Display** | `0x050505` | Deep black mirror glass (`roughness: 0.05`) |
| **Brand Accent** | `0x6366f1` | Vibrant indigo UI element |
| **Glass Card UI** | `rgba(255, 255, 255, 0.7)`| Frosted milk glass with dark text (`#0f172a`) |

---

## 4. "Bioluminescent Deep Sea" (Organic, Nature, Abstract, Wellness)
*Aquamarine glow emerging from deep oceanic abyss.*

| Role | Hex Color | Intensity / Purpose |
| :--- | :--- | :--- |
| **Clear / Fog** | `0x020f18` | Midnight abyssal blue-green |
| **Ambient Fill** | `0x06283d` | Deep ocean ambient (`0.45`) |
| **Key Light** | `0x00f5d4` | Radiant aqua bioluminescence (`1.3`, pos: `3, 7, 4`) |
| **Rim Light** | `0x7b2cbf` | Deep ultraviolet rim kicker (`0.7`, pos: `-5, 3, -5`) |
| **Foliage / Coral** | `0x2ec4b6` | Vibrant emerald turquoise (`roughness: 0.4`) |
| **Abyssal Rock** | `0x0a192f` | Dark wet seafloor stone (`roughness: 0.85, metalness: 0.2`) |
| **Glow Elements** | `0x52b788` | Soft neon green emissive (`intensity: 2.2`) |
| **Particles** | `0x00f5d4` | Floating plankton spores |
| **Glass Card UI** | `rgba(2, 15, 24, 0.7)` | Deep ocean glass |

---

## 5. "Solar Flare Amber" (FinTech, Energy, Luxury Gold, Architecture)
*Rich bronze, copper, and fiery amber tones for high-value financial or luxury brands.*

| Role | Hex Color | Intensity / Purpose |
| :--- | :--- | :--- |
| **Clear / Fog** | `0x140a04` | Dark charred espresso |
| **Ambient Fill** | `0x241208` | Warm mahogany fill (`0.5`) |
| **Key Light** | `0xffb703` | Intense warm amber sun (`1.4`, pos: `5, 8, 4`) |
| **Rim Light** | `0xfb8500` | Fiery orange backlight (`0.7`, pos: `-4, 3, -6`) |
| **Polished Brass** | `0xd4af37` | Reflective brass/gold (`roughness: 0.2, metalness: 0.9`) |
| **Brushed Bronze** | `0x8c5e34` | Brushed dark bronze (`roughness: 0.4, metalness: 0.85`) |
| **Dark Marble** | `0x1c1917` | Black marble with gold highlights (`roughness: 0.1`) |
| **Particles** | `0xffd166` | Rising golden sparks |
| **Glass Card UI** | `rgba(20, 10, 4, 0.75)` | Smoked amber glass |

---

## Lighting Rig Integration Pattern

```js
function applyLightingPalette(scene, palette) {
  // Clear color & Fog
  renderer.setClearColor(palette.clear);
  scene.fog = new THREE.FogExp2(palette.clear, palette.fogDensity || 0.035);

  // Ambient
  const ambient = new THREE.AmbientLight(palette.ambient, palette.ambientIntensity || 0.5);
  scene.add(ambient);

  // Key Light with shadows
  const key = new THREE.DirectionalLight(palette.key, palette.keyIntensity || 1.3);
  key.position.set(5, 8, 4);
  key.castShadow = true;
  key.shadow.mapSize.set(1024, 1024);
  key.shadow.bias = -0.0001;
  key.shadow.normalBias = 0.02;
  scene.add(key);

  // Rim Light
  const rim = new THREE.DirectionalLight(palette.rim, palette.rimIntensity || 0.6);
  rim.position.set(-4, 3, -6);
  scene.add(rim);

  return { ambient, key, rim };
}
```
