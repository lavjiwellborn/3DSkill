# Procedural Materials & Textures Without External Assets

Procedural 3D web experiences cannot rely on external PNG/JPG downloads or HDR files. All surface variation and reflections must be generated in memory via the HTML5 2D Canvas API and Three.js shader / PMREM utilities.

---

## 1. The In-Memory `CanvasTexture` Pattern

Every texture recipe follows the same lifecycle:
1. Create an offscreen HTML `<canvas>` element (typically 256×256 or 512×512).
2. Draw procedural patterns using 2D Canvas methods (`fillRect`, `lineTo`, `arc`, linear/radial gradients).
3. Wrap in `new THREE.CanvasTexture(canvas)`.
4. Configure wrapping and color space:
   ```js
   texture.wrapS = THREE.RepeatWrapping;
   texture.wrapT = THREE.RepeatWrapping;
   texture.colorSpace = THREE.SRGBColorSpace; // r152+; omit for r128
   ```
5. Assign to material maps: `map` (diffuse), `roughnessMap`, `bumpMap`, or `emissiveMap`.

---

## 2. Tested Procedural Texture Recipes

### Recipe A: Japanese Wood Grain (Lacquer, Beams, Furniture)
Draws horizontal directional grain streaks with subtle opacity variance.

```js
function makeWoodGrainTexture(baseColor = '#7a3b2e', grainColor = '#5c2c22', size = 256) {
  const canvas = document.createElement('canvas');
  canvas.width = size;
  canvas.height = size;
  const ctx = canvas.getContext('2d');

  // Base stain
  ctx.fillStyle = baseColor;
  ctx.fillRect(0, 0, size, size);

  // Organic fiber lines
  for (let i = 0; i < 45; i++) {
    ctx.strokeStyle = grainColor;
    ctx.globalAlpha = 0.12 + Math.random() * 0.18;
    ctx.lineWidth = 1 + Math.random() * 2;
    ctx.beginPath();
    const y = Math.random() * size;
    ctx.moveTo(0, y);
    for (let x = 0; x <= size; x += 16) {
      ctx.lineTo(x, y + (Math.random() - 0.5) * 8);
    }
    ctx.stroke();
  }

  const texture = new THREE.CanvasTexture(canvas);
  texture.wrapS = THREE.RepeatWrapping;
  texture.wrapT = THREE.RepeatWrapping;
  texture.colorSpace = THREE.SRGBColorSpace;
  return texture;
}

// Usage on Pagoda Beams / Plinths:
// const woodMat = new THREE.MeshStandardMaterial({
//   map: makeWoodGrainTexture('#8B4A3E', '#5C2C22'),
//   roughness: 0.7,
//   metalness: 0.05
// });
```

---

### Recipe B: Brushed Metal (Anisotropic Aluminum, Tech Chassis)
Generates high-frequency micro-scratches along one axis, simulating extruded or milled aluminum.

```js
function makeBrushedMetalTexture(baseColor = '#27272a', scratchColor = '#3f3f46', size = 512) {
  const canvas = document.createElement('canvas');
  canvas.width = size;
  canvas.height = size;
  const ctx = canvas.getContext('2d');

  ctx.fillStyle = baseColor;
  ctx.fillRect(0, 0, size, size);

  // Dense horizontal hairline striations
  for (let i = 0; i < 600; i++) {
    ctx.strokeStyle = scratchColor;
    ctx.globalAlpha = 0.04 + Math.random() * 0.08;
    ctx.lineWidth = 0.75 + Math.random() * 1.25;
    const y = Math.random() * size;
    const length = size * (0.3 + Math.random() * 0.7);
    const startX = Math.random() * size;

    ctx.beginPath();
    ctx.moveTo(startX, y);
    ctx.lineTo(startX + length, y);
    ctx.stroke();
  }

  const texture = new THREE.CanvasTexture(canvas);
  texture.wrapS = THREE.RepeatWrapping;
  texture.wrapT = THREE.RepeatWrapping;
  texture.repeat.set(2, 4);
  texture.colorSpace = THREE.SRGBColorSpace;
  return texture;
}

// Usage for hardware unibody:
// const chassisMat = new THREE.MeshStandardMaterial({
//   color: 0x1c1c1e,
//   roughness: 0.35,
//   metalness: 0.85,
//   roughnessMap: makeBrushedMetalTexture('#ffffff', '#888888')
// });
```

---

### Recipe C: Architectural Window Facade (Modern Towers, Nocturne Cityscape)
Draws a structured grid of windows with randomized illuminated and dark office cells.

```js
function makeWindowFacadeTexture(width = 512, height = 512, rows = 32, cols = 16, litRatio = 0.4) {
  const canvas = document.createElement('canvas');
  canvas.width = width;
  canvas.height = height;
  const ctx = canvas.getContext('2d');

  // Concrete / mullion backdrop (Structure Tier)
  ctx.fillStyle = '#10171a';
  ctx.fillRect(0, 0, width, height);

  const padX = width / cols;
  const padY = height / rows;
  const winW = padX * 0.65;
  const winH = padY * 0.55;

  for (let r = 0; r < rows; r++) {
    for (let c = 0; c < cols; c++) {
      const isLit = Math.random() < litRatio;
      if (isLit) {
        // Accent Tier hue: warm amber window light (#F2A94E)
        const amberGlow = Math.random() > 0.8 ? '#fcd34d' : '#f2a94e';
        ctx.fillStyle = amberGlow;
        ctx.shadowColor = amberGlow;
        ctx.shadowBlur = 4;
        ctx.fillRect(c * padX + (padX - winW) / 2, r * padY + (padY - winH) / 2, winW, winH);
      } else {
        // Dark unlit office
        ctx.fillStyle = '#1b2a2e';
        ctx.shadowBlur = 0;
        ctx.fillRect(c * padX + (padX - winW) / 2, r * padY + (padY - winH) / 2, winW, winH);
      }
    }
  }

  const texture = new THREE.CanvasTexture(canvas);
  texture.wrapS = THREE.RepeatWrapping;
  texture.wrapT = THREE.RepeatWrapping;
  texture.repeat.set(1, 2);
  texture.colorSpace = THREE.SRGBColorSpace;
  return texture;
}
```

---

### Recipe D: Ceramic Roof Tile Ridging (Temples & Shrines)
Draws vertical ribbed shading representing interlocking roof tile columns (kawara).

```js
function makeRoofTileTexture(tileColor = '#14171c', ridgeColor = '#242a33', size = 256) {
  const canvas = document.createElement('canvas');
  canvas.width = size;
  canvas.height = size;
  const ctx = canvas.getContext('2d');

  ctx.fillStyle = tileColor;
  ctx.fillRect(0, 0, size, size);

  const ridges = 16;
  const colWidth = size / ridges;

  for (let i = 0; i < ridges; i++) {
    const x = i * colWidth;
    const grad = ctx.createLinearGradient(x, 0, x + colWidth, 0);
    grad.addColorStop(0.0, 'rgba(0, 0, 0, 0.45)');
    grad.addColorStop(0.3, ridgeColor);
    grad.addColorStop(0.7, 'rgba(255, 255, 255, 0.12)');
    grad.addColorStop(1.0, 'rgba(0, 0, 0, 0.55)');

    ctx.fillStyle = grad;
    ctx.fillRect(x, 0, colWidth, size);
  }

  const texture = new THREE.CanvasTexture(canvas);
  texture.wrapS = THREE.RepeatWrapping;
  texture.wrapT = THREE.RepeatWrapping;
  texture.repeat.set(4, 4);
  texture.colorSpace = THREE.SRGBColorSpace;
  return texture;
}
```

---

## 3. Procedural Environment Map (PMREMGenerator)

Metallic materials (`metalness > 0.4`) require an environment map to reflect. Without an environment map, metal surfaces reflect only directional light highlights and otherwise appear flat or black.

Instead of downloading heavy `.hdr` files, generate an in-memory radiance map from a lightweight virtual Three.js scene:

```js
function setupProceduralEnvironment(renderer, scene) {
  const pmremGenerator = new THREE.PMREMGenerator(renderer);
  pmremGenerator.compileEquirectangularShader();

  // Create a lightweight virtual studio room
  const envScene = new THREE.Scene();
  envScene.background = new THREE.Color(0x10171a); // matches Dominant tier

  // Virtual warm key light reflection
  const light1 = new THREE.DirectionalLight(0xffd194, 2.5);
  light1.position.set(5, 7, 5);
  envScene.add(light1);

  // Virtual cool fill light reflection
  const light2 = new THREE.DirectionalLight(0x7bb8d4, 1.5);
  light2.position.set(-5, 3, -5);
  envScene.add(light2);

  // Virtual soft ground bounce
  const light3 = new THREE.DirectionalLight(0x382350, 0.8);
  light3.position.set(0, -5, 0);
  envScene.add(light3);

  // Generate radiance map
  const renderTarget = pmremGenerator.fromScene(envScene, 0.04);
  scene.environment = renderTarget.texture;

  // Clean up generator memory
  pmremGenerator.dispose();
}
```

---

## 4. Roughness & Metalness Calibration Table

Use these tested physical values when creating materials:

| Material Type | Color | `roughness` | `metalness` | Notes |
| :--- | :--- | :--- | :--- | :--- |
| **Plaster / Wall** | `#ede6d6` | `0.85–0.95` | `0.0` | High diffuse scatter, zero specular |
| **Painted Lacquer** | `#8b4a3e` | `0.45–0.60` | `0.05` | Satin sheen, tight soft highlight |
| **Weathered Stone** | `#6b6259` | `0.80–0.90` | `0.0` | Rough, matte, cast shadows |
| **Aged Gold Finial** | `#d9a55c` | `0.25–0.35` | `0.75–0.85` | Requires procedural env map |
| **Brushed Titanium** | `#2c2c2e` | `0.30–0.40` | `0.85–0.95` | Use brushed metal `roughnessMap` |
| **OLED Glass Bezel** | `#050505` | `0.04–0.08` | `0.10` | Deep mirror reflection |
