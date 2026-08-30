# Zero-Dependency Custom GLSL Shaders & FX

This reference provides production-ready, lightweight **custom GLSL shaders** using Three.js `ShaderMaterial` and `RawShaderMaterial`. All shaders use in-line mathematical formulas with zero external texture downloads or heavy post-processing libraries.

---

## 1. Atmospheric Volumetric Light Cone (God Rays)

Simulates theatrical sunlight or spotlight beams cutting through dust and fog with soft edge falloff.

```js
function createVolumetricLightBeam(colorHex = 0xfff0dd, radiusTop = 0.2, radiusBottom = 3.5, height = 12) {
  const geometry = new THREE.CylinderGeometry(radiusTop, radiusBottom, height, 32, 1, true);
  geometry.translate(0, -height / 2, 0); // Origin at light apex

  const material = new THREE.ShaderMaterial({
    uniforms: {
      uColor: { value: new THREE.Color(colorHex) },
      uIntensity: { value: 1.4 },
      uTime: { value: 0 }
    },
    vertexShader: `
      varying vec3 vNormal;
      varying vec3 vWorldPosition;
      varying vec2 vUv;
      void main() {
        vUv = uv;
        vNormal = normalize(normalMatrix * normal);
        vec4 worldPos = modelMatrix * vec4(position, 1.0);
        vWorldPosition = worldPos.xyz;
        gl_Position = projectionMatrix * viewMatrix * worldPos;
      }
    `,
    fragmentShader: `
      uniform vec3 uColor;
      uniform float uIntensity;
      uniform float uTime;
      varying vec3 vNormal;
      varying vec2 vUv;

      void main() {
        // Vertical gradient: intense near source, fading with distance
        float verticalFalloff = pow(1.0 - vUv.y, 1.8);

        // Rim/fresnel falloff: soft transparent edges
        vec3 viewDir = normalize(cameraPosition - vNormal);
        float edge = abs(dot(viewDir, vNormal));
        float fresnel = smoothstep(0.0, 0.8, edge);

        // Subtle atmospheric dust shimmer
        float shimmer = 0.9 + 0.1 * sin(uTime * 1.5 + vUv.y * 12.0);

        float alpha = verticalFalloff * fresnel * uIntensity * shimmer;
        gl_FragColor = vec4(uColor, alpha);
      }
    `,
    transparent: true,
    side: THREE.DoubleSide,
    blending: THREE.AdditiveBlending,
    depthWrite: false
  });

  return new THREE.Mesh(geometry, material);
}
```

---

## 2. Holographic Scanline & Grid Wireframe Shader

Creates glowing digital tech overlays, telemetry planes, or sci-fi energy barriers.

```js
function createHologramMaterial(colorHex = 0x38bdf8) {
  return new THREE.ShaderMaterial({
    uniforms: {
      uColor: { value: new THREE.Color(colorHex) },
      uTime: { value: 0 }
    },
    vertexShader: `
      varying vec2 vUv;
      varying vec3 vNormal;
      void main() {
        vUv = uv;
        vNormal = normalize(normalMatrix * normal);
        gl_Position = projectionMatrix * modelViewMatrix * vec4(position, 1.0);
      }
    `,
    fragmentShader: `
      uniform vec3 uColor;
      uniform float uTime;
      varying vec2 vUv;
      varying vec3 vNormal;

      void main() {
        // 1. Moving horizontal scanlines
        float scanline = sin(vUv.y * 120.0 - uTime * 4.0) * 0.5 + 0.5;
        scanline = pow(scanline, 3.0);

        // 2. Fine grid coordinates
        vec2 grid = abs(fract(vUv * 30.0 - 0.5) - 0.5) / fwidth(vUv * 30.0);
        float line = min(grid.x, grid.y);
        float gridPattern = 1.0 - min(line, 1.0);

        // 3. Fresnel edge glow
        float fresnel = pow(1.0 - abs(dot(vNormal, vec3(0.0, 0.0, 1.0))), 2.0);

        float alpha = (scanline * 0.35 + gridPattern * 0.6 + fresnel * 0.5);
        gl_FragColor = vec4(uColor * (1.2 + scanline * 0.5), clamp(alpha, 0.0, 0.85));
      }
    `,
    transparent: true,
    side: THREE.DoubleSide,
    blending: THREE.AdditiveBlending,
    depthWrite: false
  });
}
```

---

## 3. GPU Particle Field with Noise Drift

A lightweight, zero-texture interactive particle storm rendered in a single draw call.

```js
function createProceduralParticleStorm(count = 1500, colorHex = 0x38bdf8) {
  const geo = new THREE.BufferGeometry();
  const positions = new Float32Array(count * 3);
  const randomOffsets = new Float32Array(count * 3);

  for (let i = 0; i < count; i++) {
    positions[i * 3]     = (Math.random() - 0.5) * 40;
    positions[i * 3 + 1] = Math.random() * 30 - 5;
    positions[i * 3 + 2] = (Math.random() - 0.5) * 40;

    randomOffsets[i * 3]     = Math.random() * 10;
    randomOffsets[i * 3 + 1] = Math.random() * 10;
    randomOffsets[i * 3 + 2] = Math.random() * 10;
  }

  geo.setAttribute('position', new THREE.BufferAttribute(positions, 3));
  geo.setAttribute('aOffset', new THREE.BufferAttribute(randomOffsets, 3));

  const mat = new THREE.ShaderMaterial({
    uniforms: {
      uColor: { value: new THREE.Color(colorHex) },
      uTime: { value: 0 },
      uSize: { value: 3.5 }
    },
    vertexShader: `
      uniform float uTime;
      uniform float uSize;
      attribute vec3 aOffset;
      varying float vAlpha;

      void main() {
        vec3 pos = position;
        // Harmonic orbital drift
        pos.x += sin(uTime * 0.4 + aOffset.x) * 1.5;
        pos.y += cos(uTime * 0.5 + aOffset.y) * 1.2;
        pos.z += sin(uTime * 0.3 + aOffset.z) * 1.5;

        vec4 mvPos = modelViewMatrix * vec4(pos, 1.0);
        gl_PointSize = uSize * (20.0 / -mvPos.z);
        gl_Position = projectionMatrix * mvPos;

        vAlpha = smoothstep(45.0, 5.0, -mvPos.z);
      }
    `,
    fragmentShader: `
      uniform vec3 uColor;
      varying float vAlpha;

      void main() {
        // Circular soft particle disk
        vec2 coord = gl_PointCoord - vec2(0.5);
        float dist = length(coord);
        if (dist > 0.5) discard;
        float softEdge = smoothstep(0.5, 0.0, dist);

        gl_FragColor = vec4(uColor, softEdge * vAlpha * 0.75);
      }
    `,
    transparent: true,
    blending: THREE.AdditiveBlending,
    depthWrite: false
  });

  return new THREE.Points(geo, mat);
}
```

---

## 4. Render Loop Uniform Updates

Always update `uTime` inside `animate()` without allocating new objects:

```js
const clock = new THREE.Clock();

function animate() {
  requestAnimationFrame(animate);
  const elapsed = clock.getElapsedTime();

  // Update shader uniforms cleanly
  shaderMesh.material.uniforms.uTime.value = elapsed;

  renderer.render(scene, camera);
}
```
