# Polish, Performance & Production Readiness

Procedural geometry built from primitives transitions from an amateur "Three.js demo" to a luxury commercial website through **color science, lighting contrast, contact shadows, mobile responsiveness, and zero-allocation performance**.

---

## 1. Color Science & Tone Mapping (Mandatory Step 1)

Never render Three.js in raw un-tone-mapped linear space. Without tone mapping, highlights blow out to harsh 255/255/255 white and darks lose depth.

```js
// Apply immediately after creating WebGLRenderer
renderer.toneMapping = THREE.ACESFilmicToneMapping; // Industry standard filmic rolloff
renderer.toneMappingExposure = 1.0;                  // 0.8 = dark/moody, 1.2 = bright/clean
renderer.outputColorSpace = THREE.SRGBColorSpace;    // Correct gamma reproduction
```

---

## 2. Cinematic Cross-Lighting Rig

A three-point lighting rig establishes volumetric shape through warm/cool color temperature contrast.

```js
function setupCinematicLighting(scene) {
  // 1. Ambient Fill (cool tinted shadow floor)
  const ambient = new THREE.AmbientLight(0x384252, 0.5);
  scene.add(ambient);

  // 2. Key Light (warm main sun with crisp soft shadows)
  const keyLight = new THREE.DirectionalLight(0xfff0dd, 1.3);
  keyLight.position.set(6, 10, 5);
  keyLight.castShadow = true;
  
  // Shadow camera optimization
  keyLight.shadow.mapSize.set(1024, 1024); // 2048 for desktop hero shots
  keyLight.shadow.camera.near = 0.5;
  keyLight.shadow.camera.far = 30;
  keyLight.shadow.camera.left = -7;
  keyLight.shadow.camera.right = 7;
  keyLight.shadow.camera.top = 7;
  keyLight.shadow.camera.bottom = -7;
  keyLight.shadow.bias = -0.0001;          // Eliminates shadow acne
  keyLight.shadow.normalBias = 0.02;       // Eliminates surface self-shadow artifacts
  scene.add(keyLight);

  // 3. Rim / Back Light (cool cyan/blue kicker defining silhouette edge)
  const rimLight = new THREE.DirectionalLight(0x7dd3fc, 0.6);
  rimLight.position.set(-5, 4, -6);
  scene.add(rimLight);

  // 4. Subtle Ground Bounce (HemisphereLight)
  const bounceLight = new THREE.HemisphereLight(0xfff0dd, 0x0f172a, 0.25);
  scene.add(bounceLight);

  return { keyLight, rimLight, ambient };
}
```

---

## 3. Contact Shadows (Invisible Shadow Catcher)

Grounding the object with an invisible plane catching real-time soft shadows transforms floating geometries into tangible physical objects.

```js
function createShadowCatcher(size = 25, opacity = 0.35) {
  const planeGeo = new THREE.PlaneGeometry(size, size);
  const shadowMat = new THREE.ShadowMaterial({
    opacity: opacity,
    transparent: true,
    depthWrite: false,
  });
  const plane = new THREE.Mesh(planeGeo, shadowMat);
  plane.rotation.x = -Math.PI / 2;
  plane.position.y = -0.01; // Sits exactly under base plinth
  plane.receiveShadow = true;
  return plane;
}
```

---

## 4. Mobile Responsiveness & Portrait FOV Compensation

On mobile phones held vertically (portrait mode, aspect < 1.0), standard Three.js perspective cameras clip the left and right sides of horizontal models.

```js
function handleResponsiveResize(camera, renderer) {
  const width = window.innerWidth;
  const height = window.innerHeight;
  const aspect = width / height;

  camera.aspect = aspect;

  // Base FOV tuned for widescreen desktop (aspect ~ 1.7)
  const baseFov = 50;
  if (aspect < 1.0) {
    // Mobile portrait: scale FOV inversely with aspect so entire width remains visible
    camera.fov = Math.min(85, baseFov / aspect * 0.78);
  } else {
    camera.fov = baseFov;
  }

  camera.updateProjectionMatrix();
  renderer.setSize(width, height);
  renderer.setPixelRatio(Math.min(window.devicePixelRatio, width < 768 ? 1.5 : 2.0));
}

window.addEventListener('resize', () => handleResponsiveResize(camera, renderer));
```

---

## 5. Procedural Environment Map (PMREMGenerator)

Metallic and glossy materials need an environment map to reflect. Generate one procedurally from virtual light points without downloading HDR files.

```js
function setupProceduralEnvironment(renderer, scene) {
  const pmremGenerator = new THREE.PMREMGenerator(renderer);
  pmremGenerator.compileEquirectangularShader();

  const envScene = new THREE.Scene();
  envScene.background = new THREE.Color(0x0a0e1a);
  
  // Virtual light sources to reflect in metallic materials
  const light1 = new THREE.DirectionalLight(0xfff0dd, 2.0);
  light1.position.set(5, 5, 5);
  envScene.add(light1);

  const light2 = new THREE.DirectionalLight(0x38bdf8, 1.5);
  light2.position.set(-5, 3, -5);
  envScene.add(light2);

  const envTexture = pmremGenerator.fromScene(envScene, 0.04).texture;
  scene.environment = envTexture;
  pmremGenerator.dispose();
}
```

---

## 6. Selective Bloom & Vignette Post-Processing

Using Three.js postprocessing (`EffectComposer` + `UnrealBloomPass`) elevates emissive elements (windows, thrusters, neon accents).

```js
import { EffectComposer } from 'https://cdn.jsdelivr.net/npm/three@0.170.0/examples/jsm/postprocessing/EffectComposer.js';
import { RenderPass } from 'https://cdn.jsdelivr.net/npm/three@0.170.0/examples/jsm/postprocessing/RenderPass.js';
import { UnrealBloomPass } from 'https://cdn.jsdelivr.net/npm/three@0.170.0/examples/jsm/postprocessing/UnrealBloomPass.js';

function setupPostProcessing(renderer, scene, camera) {
  const composer = new EffectComposer(renderer);
  composer.addPass(new RenderPass(scene, camera));

  const bloomPass = new UnrealBloomPass(
    new THREE.Vector2(window.innerWidth, window.innerHeight),
    0.45,  // Strength (0.2–0.6 = subtle elegance, 1.0+ = cyberpunk neon)
    0.4,   // Radius
    0.85   // Threshold (only colors with luminance > 0.85 bloom)
  );
  composer.addPass(bloomPass);

  return composer;
}
```

---

## 7. Zero-Allocation Render Loop (60 FPS Performance Rule)

Creating new objects (`new THREE.Vector3()`, `new THREE.Color()`) inside the `animate()` loop triggers JavaScript Garbage Collection pauses (micro-stutters).

**Correct (Pre-allocated variables outside loop):**
```js
// Reusable scratchpad vectors outside animate()
const _v1 = new THREE.Vector3();
const _v2 = new THREE.Vector3();

function animate() {
  requestAnimationFrame(animate);
  // Modify existing vectors without allocating new memory
  _v1.set(0, currentProgress * 2, 0);
  heroGroup.position.copy(_v1);
  renderer.render(scene, camera);
}
```

---

## 8. Complete Resource Disposal Matrix (React / SPA Cleanup)

Failing to dispose GPU buffers causes WebGL context crashes and memory leaks upon route navigation or hot reloading.

```js
function disposeSceneHierarchy(scene, renderer, composer) {
  if (composer) composer.dispose();

  scene.traverse((object) => {
    if (object.geometry) {
      object.geometry.dispose();
    }
    if (object.material) {
      const materials = Array.isArray(object.material) ? object.material : [object.material];
      materials.forEach((mat) => {
        // Dispose all active texture maps
        ['map', 'normalMap', 'roughnessMap', 'metalnessMap', 'emissiveMap', 'envMap'].forEach((mapName) => {
          if (mat[mapName]) mat[mapName].dispose();
        });
        mat.dispose();
      });
    }
  });

  renderer.dispose();
  renderer.forceContextLoss();
}
```
