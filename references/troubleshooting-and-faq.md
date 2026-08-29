# Troubleshooting & Diagnostic Guide

When building or testing a procedural 3D scroll website, issues can range from complete rendering failure to subtle visual defects. Use this diagnostic matrix to immediately identify and resolve any problem.

---

## 1. Complete Failure Modes (Nothing Renders or Page Breaks)

### Issue: "Canvas is completely black / blank"
* **Diagnosis 1: Camera is inside the geometry.**
  * *Fix:* Check `camera.position` vs object position. If object is at `(0, 0, 0)`, move camera to `(0, 2, 8)` or `(0, 0, 10)`. Call `camera.lookAt(0, 0, 0)`.
* **Diagnosis 2: Camera clipping planes are misconfigured.**
  * *Fix:* Ensure `camera.near` is small (e.g. `0.1`) and `camera.far` is large enough (e.g. `1000`). If `far` is `10` and camera is at `12`, the scene is clipped.
* **Diagnosis 3: No lights with `MeshStandardMaterial`.**
  * *Fix:* Standard materials require lighting. If no light is in the scene, materials render pitch black. Add at least one `AmbientLight` and one `DirectionalLight`.
* **Diagnosis 4: WebGL Context Creation Failed.**
  * *Fix:* Check browser console for `WebGL context lost` or disabled hardware acceleration. Ensure the fallback overlay is displayed gracefully.

---

### Issue: "Scrolling the page doesn't move or animate the 3D scene"
* **Diagnosis 1: Page has no scroll height.**
  * *Fix:* Verify `.scroll-section` has `min-height: 100vh`. If the DOM content has 0 height, `document.documentElement.scrollHeight - window.innerHeight` is `0`, producing `0` progress.
* **Diagnosis 2: Target progress is not updating in render loop.**
  * *Fix:* Ensure `animate()` reads `currentProgress += (targetProgress - currentProgress) * dampingFactor` and calls `applyProgressToScene(currentProgress)` every frame.
* **Diagnosis 3: Passive scroll listener blocked.**
  * *Fix:* Check for CSS `overflow: hidden` on `body` or `html`. Ensure `window.addEventListener('scroll', ..., { passive: true })` is attached to `window`, not a detached container.

---

### Issue: "Buttons / Links / Inputs on the page cannot be clicked"
* **Diagnosis: Canvas or overlay pointer-events conflict.**
  * *Fix:*
    - Canvas must have: `pointer-events: none;` OR sit at `z-index: 0` with content above it.
    - Container `.scroll-content` must have `pointer-events: none;`.
    - Interactive children (`.section-inner`, `button`, `a`, `.glass-card`) must have `pointer-events: auto;`.

---

## 2. Visual & Aesthetic Defects

### Issue: "Colors look washed out, chalky, or overexposed"
* **Diagnosis: Missing tone mapping and sRGB color space.**
  * *Fix:*
    ```js
    renderer.toneMapping = THREE.ACESFilmicToneMapping;
    renderer.toneMappingExposure = 1.0;
    renderer.outputColorSpace = THREE.SRGBColorSpace;
    ```
    Ensure every `CanvasTexture` also has `texture.colorSpace = THREE.SRGBColorSpace;`.

---

### Issue: "Object is cropped / cut off on Mobile devices (Portrait Mode)"
* **Diagnosis: Perspective camera FOV is fixed vertically, narrowing horizontal view on narrow screens.**
  * *Fix: Responsive Aspect Ratio Compensation in resize handler:*
    ```js
    function updateResponsiveCamera() {
      const aspect = window.innerWidth / window.innerHeight;
      camera.aspect = aspect;
      
      // Base FOV designed for desktop (aspect >= 1.6)
      const baseFov = 50;
      if (aspect < 1.0) {
        // Mobile portrait: increase FOV or push camera back so width fits
        camera.fov = baseFov / aspect * 0.75;
      } else {
        camera.fov = baseFov;
      }
      camera.updateProjectionMatrix();
    }
    ```

---

### Issue: "Shadows look jagged, pixelated, or disappear when scrolling"
* **Diagnosis 1: Shadow camera frustum does not cover the scene.**
  * *Fix:* Expand shadow camera bounds:
    ```js
    keyLight.shadow.camera.left = -10;
    keyLight.shadow.camera.right = 10;
    keyLight.shadow.camera.top = 10;
    keyLight.shadow.camera.bottom = -10;
    keyLight.shadow.camera.near = 0.5;
    keyLight.shadow.camera.far = 30;
    ```
* **Diagnosis 2: Shadow map resolution is too low.**
  * *Fix:* Set `keyLight.shadow.mapSize.set(1024, 1024)` or `2048, 2048`. Use `THREE.PCFSoftShadowMap` on renderer.
* **Diagnosis 3: Shadow Acne (stripes on geometry surface).**
  * *Fix:* Set `keyLight.shadow.bias = -0.0001;` and `keyLight.shadow.normalBias = 0.02;`.

---

### Issue: "Shading looks faceted / low-poly when it should be smooth"
* **Diagnosis: Vertex normals were not computed after vertex displacement.**
  * *Fix:* Call `geometry.computeVertexNormals()` immediately after modifying any vertex positions.

---

### Issue: "Roof looks like a hat sitting on a box"
* **Diagnosis: Plan-shape mismatch.**
  * *Fix:* If body is square (`BoxGeometry`), roof must be square (`ConeGeometry(radius, height, 4)` rotated by 45°). Never put a round `LatheGeometry` roof on a square box. See `procedural-geometry.md`.

---

## 3. Performance & Memory Leaks

### Issue: "Frame rate drops or micro-stutters occur during scroll"
* **Diagnosis 1: Allocating objects in `animate()` or render loop.**
  * *Fix:* Never do `new THREE.Vector3()`, `new THREE.Color()`, or `new Float32Array()` inside `requestAnimationFrame`. Pre-allocate reusable vectors outside the loop:
    ```js
    const _tempVec = new THREE.Vector3(); // reuse across frames
    ```
* **Diagnosis 2: Uncapped devicePixelRatio on Retina/4K displays.**
  * *Fix:* `renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));` (or `1.5` on mobile).
* **Diagnosis 3: Too many draw calls.**
  * *Fix:* Check `renderer.info.render.calls`. If > 100, replace individual repetitive meshes with `THREE.InstancedMesh` or merge static geometries with `BufferGeometryUtils.mergeGeometries()`.

---

### Issue: "Memory climbs continuously on React Hot Reload / Route Change"
* **Diagnosis: Incomplete cleanup in `useEffect` return.**
  * *Fix:* Traverse the scene, dispose all geometries, materials, and textures, cancel `requestAnimationFrame`, and remove all window event listeners. See cleanup recipe in `polish-and-performance.md` and `scaffold-and-overlay.md`.
