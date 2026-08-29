# 6-DOF Scrollytelling Director's Cookbook

This guide provides exact mathematical formulas and Three.js code patterns for all **6 Degrees of Freedom (6-DOF: Position X/Y/Z + Rotation X/Y/Z)**, **Catmull-Rom Spline Fly-Throughs**, **Dynamic LookAt Target Tracking**, and **Camera Lens Warping**.

---

## 1. The 6-DOF Motion Matrix

| Cinematic Move | 3D Transform Mapping | Visual Effect |
| :--- | :--- | :--- |
| **Dolly In / Out** | `camera.position.z` or Distance along forward vector | Camera physically travels toward or away from subject |
| **Truck Left / Right** | `camera.position.x = lerp(startX, endX, t)` | Camera slides horizontally across the scene |
| **Pedestal Up / Down** | `camera.position.y = lerp(startY, endY, t)` | Camera elevates or descends vertically |
| **Pan (Yaw)** | `camera.rotation.y` or `lookAt(targetX, targetY, targetZ)` | Camera turns left/right on vertical axis |
| **Tilt (Pitch)** | `camera.rotation.x` or lookAt height offset | Camera angles up toward sky or down toward ground |
| **Roll (Dutch Angle)** | `camera.rotation.z = Math.sin(t * Math.PI) * angle` | Cinematic bank/tilt during turns or speed boosts |
| **Orbital Arc** | `pos.x = sin(θ) * R`, `pos.z = cos(θ) * R` | Camera sweeps around subject in a circle |
| **Spiral Ascent** | `pos.x = sin(θ) * R`, `pos.y = y0 + t*H`, `pos.z = cos(θ) * R` | Camera corkscrews upwards while filming subject |
| **Crash Zoom (Vertigo)**| Simultaneous `dolly.z` forward + `camera.fov` increase | Background perspective warps while subject size stays constant (Hitchcock Vertigo effect) |

---

## 2. Advanced Trajectory Formulas

### A. Spiral Ascent (Corkscrew Crane Shot)
Sweeps in a circular helix around the hero object while ascending.

```js
function applySpiralAscent(camera, heroPosition, progress, radius = 8, height = 6) {
  const theta = progress * Math.PI * 2.5; // 1.25 full rotations
  camera.position.x = heroPosition.x + Math.sin(theta) * radius * (1 - progress * 0.3);
  camera.position.y = heroPosition.y + progress * height + 1.5;
  camera.position.z = heroPosition.z + Math.cos(theta) * radius * (1 - progress * 0.3);
  
  // Look at rising center point
  camera.lookAt(heroPosition.x, heroPosition.y + progress * height * 0.6 + 1.0, heroPosition.z);
}
```

---

### B. Catmull-Rom Spline 3D Fly-Through (Architectural / Sci-Fi Tour)
Smooth flight path through waypoints with separate lookAt tracking path.

```js
// 1. Define Camera Path
const flightPath = new THREE.CatmullRomCurve3([
  new THREE.Vector3(0, 2, 12),    // 0%: Wide establishing view
  new THREE.Vector3(6, 4, 6),     // 30%: Sweeping high-right flank
  new THREE.Vector3(2, 6, -3),    // 60%: High overhead reveal
  new THREE.Vector3(-4, 3, -6),   // 80%: Rear architectural close-up
  new THREE.Vector3(0, 1.5, 7),   // 100%: Front hero landing
], false, 'catmullrom', 0.5);

// 2. Define LookAt Target Path
const targetPath = new THREE.CatmullRomCurve3([
  new THREE.Vector3(0, 1, 0),
  new THREE.Vector3(0, 2, 0),
  new THREE.Vector3(0, 3, 0),
  new THREE.Vector3(0, 2, 0),
  new THREE.Vector3(0, 1.5, 0),
]);

// 3. Evaluate in animation loop:
function applySplineChoreography(camera, progress) {
  const p = Math.max(0, Math.min(1, progress));
  
  // Get point on curve
  const camPos = flightPath.getPointAt(p);
  const targetPos = targetPath.getPointAt(p);
  
  camera.position.copy(camPos);
  camera.lookAt(targetPos);

  // Optional: Add Dutch Roll tilt based on turn curvature
  const tangent = flightPath.getTangentAt(p);
  camera.rotation.z += tangent.x * 0.15;
}
```

---

### C. Hitchcock "Vertigo" Dolly-Zoom Effect
Warps background perspective while keeping the hero object exactly the same apparent size.

```js
function applyVertigoDollyZoom(camera, heroMesh, progress) {
  // As camera dollies closer (z: 10 -> 4), FOV widens (35 -> 75)
  const startZ = 10, endZ = 4;
  const startFov = 35, endFov = 75;

  camera.position.z = THREE.MathUtils.lerp(startZ, endZ, progress);
  camera.fov = THREE.MathUtils.lerp(startFov, endFov, progress);
  camera.updateProjectionMatrix();
}
```

---

### D. Multi-Axis Pivot Offsets (Exploded Views & Folding Assemblies)
Rotate or explode child parts outward from a central assembly.

```js
function applyExplodedAssembly(group, progress) {
  // progress 0 = compact unit, progress 1 = exploded diagram
  group.children.forEach((child, index) => {
    if (!child.userData.originPos) {
      child.userData.originPos = child.position.clone();
      child.userData.explodeDir = new THREE.Vector3(
        (index % 2 === 0 ? 1 : -1) * (1 + index * 0.5),
        index * 0.6,
        (index % 3 === 0 ? 1 : -1) * 0.8
      );
    }

    const offset = child.userData.explodeDir.clone().multiplyScalar(progress * 1.5);
    child.position.copy(child.userData.originPos).add(offset);
  });
}
```

---

## 3. Dynamic Lookahead Tangent Tracking

When the camera flies forward along a curved tunnel or path, the camera should look **ahead** along the curve rather than at a static point:

```js
function applyLookaheadFlight(camera, curve, progress, lookaheadDelta = 0.04) {
  const p = Math.max(0, Math.min(1, progress));
  const currentPos = curve.getPointAt(p);
  
  // Look ahead slightly on the curve
  const lookaheadP = Math.min(1, p + lookaheadDelta);
  const lookaheadPos = curve.getPointAt(lookaheadP);

  camera.position.copy(currentPos);
  camera.lookAt(lookaheadPos);
}
```
