# Procedural Materials & Textures (Zero External Assets)

A common mistake in code-generated 3D sites is using flat, uniform colors across every polygon. While solid colors can work for minimalist papercraft, true cinematic depth comes from **surface variation**: subtle roughness changes, procedural micro-bump, glowing circuit grids, wood grain, or brushed metal sheen.

Because this skill prohibits external downloaded textures/images (to keep the site 100% code-driven and self-contained), all textures below are generated **live in memory via HTML5 Canvas 2D** or **mathematical procedural shaders**.

---

## 1. The In-Memory Canvas Texture Pattern

Generate a texture once at startup into an offscreen `<canvas>`, convert it to a `THREE.CanvasTexture`, and apply it to standard materials. Zero network requests, zero bundle weight, infinitely customizable.

```js
function createProceduralTexture(width, height, drawFn) {
  const canvas = document.createElement('canvas');
  canvas.width = width;
  canvas.height = height;
  const ctx = canvas.getContext('2d');
  drawFn(ctx, width, height);
  const texture = new THREE.CanvasTexture(canvas);
  texture.wrapS = THREE.RepeatWrapping;
  texture.wrapT = THREE.RepeatWrapping;
  texture.colorSpace = THREE.SRGBColorSpace;
  return texture;
}
```

---

## 2. Essential Procedural Texture Recipes

### A. Brushed Metal / Anisotropic Noise (Luxury Tech, Hardware, Spire Finials)
Creates fine micro-scratches that catch directional lighting.

```js
function createBrushedMetalTexture(width = 512, height = 512) {
  return createProceduralTexture(width, height, (ctx, w, h) => {
    ctx.fillStyle = '#808080';
    ctx.fillRect(0, 0, w, h);
    // Draw horizontal micro-streaks
    for (let i = 0; i < 4000; i++) {
      const y = Math.random() * h;
      const x = Math.random() * w;
      const len = 20 + Math.random() * 80;
      const alpha = 0.04 + Math.random() * 0.08;
      ctx.strokeStyle = Math.random() > 0.5 ? `rgba(255,255,255,${alpha})` : `rgba(0,0,0,${alpha})`;
      ctx.lineWidth = 1;
      ctx.beginPath();
      ctx.moveTo(x, y);
      ctx.lineTo(x + len, y);
      ctx.stroke();
    }
  });
}

// Usage: Apply as roughnessMap or bumpMap
const brushedMetalMat = new THREE.MeshStandardMaterial({
  color: 0xd8d8d8,
  metalness: 0.9,
  roughness: 0.25,
  roughnessMap: createBrushedMetalTexture(),
});
```

---

### B. Japanese Wood Grain / Planks (Shrines, Pagoda Beams, Furniture)
Creates organic wood grain with natural ring/stripe variations.

```js
function createWoodTexture(width = 512, height = 512, baseColor = '#8a3c20', grainColor = '#501e0c') {
  return createProceduralTexture(width, height, (ctx, w, h) => {
    ctx.fillStyle = baseColor;
    ctx.fillRect(0, 0, w, h);

    // Sine-wave grain lines with subtle jitter
    ctx.strokeStyle = grainColor;
    for (let x = 0; x < w; x += 3) {
      const alpha = 0.15 + Math.sin(x * 0.05) * 0.1 + Math.random() * 0.05;
      ctx.strokeStyle = `rgba(60, 20, 10, ${Math.max(0, alpha)})`;
      ctx.lineWidth = 1.5;
      ctx.beginPath();
      ctx.moveTo(x, 0);
      for (let y = 0; y < h; y += 10) {
        const offset = Math.sin(y * 0.02 + x * 0.01) * 8 + (Math.random() - 0.5) * 2;
        ctx.lineTo(x + offset, y);
      }
      ctx.stroke();
    }
  });
}

// Usage:
const woodTexture = createWoodTexture();
woodTexture.repeat.set(2, 4);
const timberMat = new THREE.MeshStandardMaterial({
  map: woodTexture,
  roughness: 0.75,
  metalness: 0.05,
});
```

---

### C. Cyberpunk Grid / Sci-Fi Floor (Tech Landscapes, Ground Planes)
Creates an emissive neon grid with dark reflective panels.

```js
function createGridTexture(size = 512, divisions = 8, lineColor = '#00ffff', fillColor = '#05050f') {
  return createProceduralTexture(size, size, (ctx, w, h) => {
    ctx.fillStyle = fillColor;
    ctx.fillRect(0, 0, w, h);

    const step = w / divisions;
    ctx.strokeStyle = lineColor;
    ctx.lineWidth = 3;
    ctx.shadowColor = lineColor;
    ctx.shadowBlur = 8;

    for (let i = 0; i <= w; i += step) {
      // Vertical
      ctx.beginPath();
      ctx.moveTo(i, 0); ctx.lineTo(i, h);
      ctx.stroke();
      // Horizontal
      ctx.beginPath();
      ctx.moveTo(0, i); ctx.lineTo(w, i);
      ctx.stroke();
    }
  });
}

// Usage:
const gridTex = createGridTexture(512, 8, '#38bdf8', '#030712');
gridTex.repeat.set(10, 10);
const sciFiFloorMat = new THREE.MeshStandardMaterial({
  map: gridTex,
  emissive: 0x0284c7,
  emissiveMap: gridTex,
  emissiveIntensity: 0.6,
  roughness: 0.2,
  metalness: 0.8,
});
```

---

### D. Architectural Window Grid (Skyscrapers, Modern Buildings)
Generates high-rise illuminated windows with random lit/unlit states.

```js
function createBuildingFacadeTexture(width = 512, height = 1024, rows = 32, cols = 16) {
  return createProceduralTexture(width, height, (ctx, w, h) => {
    ctx.fillStyle = '#0f172a'; // dark wall
    ctx.fillRect(0, 0, w, h);

    const padX = 4, padY = 6;
    const winW = (w / cols) - padX;
    const winH = (h / rows) - padY;

    for (let r = 0; r < rows; r++) {
      for (let c = 0; c < cols; c++) {
        const x = c * (winW + padX) + padX / 2;
        const y = r * (winH + padY) + padY / 2;
        
        // Randomly lit window (warm yellow, cool white, or unlit)
        const rand = Math.random();
        if (rand > 0.6) {
          ctx.fillStyle = rand > 0.85 ? '#fef08a' : '#bae6fd'; // warm golden or cool office light
        } else {
          ctx.fillStyle = '#1e293b'; // dark unlit room
        }
        ctx.fillRect(x, y, winW, winH);
      }
    }
  });
}
```

---

### E. Procedural Normal Map (Surface Bumpiness & Tile Seams)
Generates normal maps (RGB encoded tangent-space vectors) in 2D canvas without needing external normal map generators.

```js
function createTileNormalMap(size = 256, tileSize = 64) {
  return createProceduralTexture(size, size, (ctx, w, h) => {
    // Neutral normal is rgb(128, 128, 255) -> pointing straight up (0, 0, 1)
    ctx.fillStyle = 'rgb(128, 128, 255)';
    ctx.fillRect(0, 0, w, h);

    // Beveled tile edges
    for (let x = 0; x < w; x += tileSize) {
      for (let y = 0; y < h; y += tileSize) {
        // Top bevel (slanted up: positive Y -> green)
        ctx.fillStyle = 'rgb(128, 180, 255)';
        ctx.fillRect(x, y, tileSize, 3);
        // Bottom bevel (slanted down: negative Y -> red/dark green)
        ctx.fillStyle = 'rgb(128, 76, 255)';
        ctx.fillRect(x, y + tileSize - 3, tileSize, 3);
        // Left bevel (slanted left: negative X -> low red)
        ctx.fillStyle = 'rgb(76, 128, 255)';
        ctx.fillRect(x, y, 3, tileSize);
        // Right bevel (slanted right: positive X -> high red)
        ctx.fillStyle = 'rgb(180, 128, 255)';
        ctx.fillRect(x + tileSize - 3, y, 3, tileSize);
      }
    }
  });
}
```

---

## 3. High-End Custom Procedural Shaders

When standard materials aren't enough, use custom GLSL shaders via `THREE.ShaderMaterial` or modify standard materials with `onBeforeCompile`.

### A. Animated Hologram / Scanline Shield
Gives any object a futuristic sci-fi hologram aura with scrolling scanlines and fresnel glow.

```js
const HologramShader = {
  uniforms: {
    uTime: { value: 0 },
    uColor: { value: new THREE.Color(0x00f0ff) },
  },
  vertexShader: `
    varying vec3 vNormal;
    varying vec3 vPosition;
    varying vec2 vUv;
    void main() {
      vNormal = normalize(normalMatrix * normal);
      vPosition = position;
      vUv = uv;
      gl_Position = projectionMatrix * modelViewMatrix * vec4(position, 1.0);
    }
  `,
  fragmentShader: `
    uniform float uTime;
    uniform vec3 uColor;
    varying vec3 vNormal;
    varying vec3 vPosition;
    varying vec2 vUv;

    void main() {
      // Fresnel effect (rim glowing edge)
      vec3 viewDir = vec3(0.0, 0.0, 1.0);
      float fresnel = pow(1.0 - abs(dot(vNormal, viewDir)), 2.5);

      // Horizontal scanlines moving upward
      float scanline = sin(vPosition.y * 30.0 - uTime * 4.0) * 0.5 + 0.5;
      scanline = pow(scanline, 3.0);

      // Edge glow + scanline composite
      float alpha = fresnel * 0.8 + scanline * 0.25;
      vec3 finalColor = uColor + vec3(scanline * 0.4);

      gl_FragColor = vec4(finalColor, alpha);
    }
  `,
  transparent: true,
  depthWrite: false,
  blending: THREE.AdditiveBlending,
  side: THREE.DoubleSide,
};

// In render loop:
// hologramMaterial.uniforms.uTime.value = clock.getElapsedTime();
```

---

### B. Procedural Water / Ocean Waves with Vertex Displacement
Generates live undulating water surface with specular sparkle.

```js
function createWaterMesh(size = 30, segments = 64) {
  const geo = new THREE.PlaneGeometry(size, size, segments, segments);
  geo.rotateX(-Math.PI / 2);

  const mat = new THREE.ShaderMaterial({
    uniforms: {
      uTime: { value: 0 },
      uDeepColor: { value: new THREE.Color(0x0a192f) },
      uShallowColor: { value: new THREE.Color(0x00b4d8) },
    },
    vertexShader: `
      uniform float uTime;
      varying float vElevation;
      varying vec2 vUv;
      void main() {
        vUv = uv;
        vec3 pos = position;
        // Two intersecting sine waves with different frequencies
        float wave1 = sin(pos.x * 0.5 + uTime * 1.5) * cos(pos.z * 0.5 + uTime * 1.2) * 0.25;
        float wave2 = sin(pos.x * 1.2 - uTime * 2.0) * sin(pos.z * 1.0 + uTime * 1.8) * 0.12;
        pos.y += wave1 + wave2;
        vElevation = wave1 + wave2;
        gl_Position = projectionMatrix * modelViewMatrix * vec4(pos, 1.0);
      }
    `,
    fragmentShader: `
      uniform vec3 uDeepColor;
      uniform vec3 uShallowColor;
      varying float vElevation;
      varying vec2 vUv;
      void main() {
        // Blend color based on wave height
        float mixFactor = (vElevation + 0.35) * 1.5;
        mixFactor = clamp(mixFactor, 0.0, 1.0);
        vec3 color = mix(uDeepColor, uShallowColor, mixFactor);
        gl_FragColor = vec4(color, 0.9);
      }
    `,
    transparent: true,
  });

  return new THREE.Mesh(geo, mat);
}
```

---

## 4. Performance Rules for Procedural Textures

1. **Keep dimensions powers of two**: Use 256x256, 512x512, or 1024x1024. Never use arbitrary sizes like 300x500.
2. **Generate once at setup**: Never generate canvas textures inside `requestAnimationFrame` or `scroll` listeners. Create them during initialization.
3. **Reuse textures across materials**: If 10 pillars all use the red timber material, create one `woodTexture` and share it across all 10 material instances.
4. **Dispose when finished**: When unmounting or cleaning up, call `texture.dispose()` on every generated canvas texture alongside geometry and materials.
