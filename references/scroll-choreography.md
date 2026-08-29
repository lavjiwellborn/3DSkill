# Scroll Choreography & Motion Design

Transforming raw scroll position into cinematic 3D motion requires strict physics damping, non-linear easing curves, mouse parallax layering, and velocity responsiveness.

---

## 1. Bulletproof Scroll Progress Driver

Always clamp progress strictly to `[0, 1]` to guard against macOS trackpad rubber-banding and mobile Safari URL bar layout shifts.

```js
function getScrollProgress() {
  const scrollTop = window.scrollY || document.documentElement.scrollTop;
  const maxScroll = document.documentElement.scrollHeight - window.innerHeight;
  if (maxScroll <= 0) return 0;
  return Math.max(0, Math.min(1, scrollTop / maxScroll));
}
```

---

## 2. Dual-Layer Damping Engine

Never assign raw scroll straight to 3D transforms. Use a damping loop in `requestAnimationFrame` that smooths both **scroll progress** and **interactive mouse tilt**.

```js
let targetProgress = 0;
let currentProgress = 0;
const dampingFactor = 0.08; // 0.06 = dreamy/slow, 0.12 = snappy/tight

// Interactive mouse parallax variables (-1 to +1)
let mouseTargetX = 0, mouseTargetY = 0;
let mouseCurrentX = 0, mouseCurrentY = 0;

window.addEventListener('scroll', () => {
  targetProgress = getScrollProgress();
}, { passive: true });

window.addEventListener('pointermove', (e) => {
  mouseTargetX = (e.clientX / window.innerWidth) * 2 - 1;
  mouseTargetY = -(e.clientY / window.innerHeight) * 2 + 1;
}, { passive: true });
```

---

## 3. Non-Linear Easing Math (Natural Organic Motion)

Linear `lerp` between keyframes produces stiff, mechanical robotic movement. Use these mathematical curves to shape time:

```js
// Smoothstep: Default ease-in-out curve
function smoothstep(t) {
  t = Math.max(0, Math.min(1, t));
  return t * t * (3 - 2 * t);
}

// Smootherstep: Ultra-smooth quintic S-curve for slow cinematic sweeps
function smootherstep(t) {
  t = Math.max(0, Math.min(1, t));
  return t * t * t * (t * (t * 6 - 15) + 10);
}

// Cubic Ease-Out: Decelerates into rest (arrival / docking / revealing)
function easeOutCubic(t) {
  t = Math.max(0, Math.min(1, t));
  return 1 - Math.pow(1 - t, 3);
}

// Cubic Ease-In: Accelerates from rest (takeoff / zooming in)
function easeInCubic(t) {
  t = Math.max(0, Math.min(1, t));
  return t * t * t;
}

// Custom Cubic Bezier helper
function cubicBezier(p1x, p1y, p2x, p2y, t) {
  const cx = 3 * p1x, bx = 3 * (p2x - p1x) - cx, ax = 1 - cx - bx;
  const cy = 3 * p1y, by = 3 * (p2y - p1y) - cy, ay = 1 - cy - by;
  function sampleCurveX(t) { return ((ax * t + bx) * t + cx) * t; }
  function sampleCurveY(t) { return ((ay * t + by) * t + cy) * t; }
  return sampleCurveY(t);
}
```

---

## 4. Multi-Track Keyframe Timeline System

Define keyframe states along the normalized 0→1 timeline. Interpolate between adjacent points using non-linear easing.

```js
const timeline = [
  { t: 0.0,  rotY: 0,             posY: -0.5, camDist: 8,  fov: 55 },
  { t: 0.35, rotY: Math.PI * 0.7, posY: 0.5,  camDist: 6.5, fov: 48 },
  { t: 0.70, rotY: Math.PI * 1.4, posY: 1.2,  camDist: 7.0, fov: 44 },
  { t: 1.0,  rotY: Math.PI * 2.0, posY: 0.0,  camDist: 8.5, fov: 40 },
];

function lerp(a, b, t) { return a + (b - a) * t; }

function applyProgressToScene(p) {
  p = Math.max(0, Math.min(1, p));

  // Find active keyframe interval
  let i = 0;
  while (i < timeline.length - 2 && p > timeline[i + 1].t) i++;
  const a = timeline[i], b = timeline[i + 1];
  
  const span = b.t - a.t || 1;
  const rawT = (p - a.t) / span;
  const localT = smoothstep(rawT); // Apply smoothstep easing

  // Apply interpolated transforms to hero group
  heroGroup.rotation.y = lerp(a.rotY, b.rotY, localT);
  heroGroup.position.y = lerp(a.posY, b.posY, localT);
  
  camera.fov = lerp(a.fov, b.fov, localT);
  camera.updateProjectionMatrix();
}
```

---

## 5. Layering Cursor Parallax on Top of Scroll

The secret to award-winning tactile websites is that the scene reacts subtly to the cursor *independently* of scroll.

```js
function updateParallax(elapsed) {
  // Smooth mouse coordinates toward targets
  mouseCurrentX += (mouseTargetX - mouseCurrentX) * 0.05;
  mouseCurrentY += (mouseTargetY - mouseCurrentY) * 0.05;

  // Add subtle camera tilt based on mouse position
  camera.position.x = mouseCurrentX * 0.8;
  camera.position.y += mouseCurrentY * 0.4;
  
  // Add subtle object rotation offset
  heroGroup.rotation.x = mouseCurrentY * 0.15;
  heroGroup.rotation.z = -mouseCurrentX * 0.1;
}
```

---

## 6. Idle Micro-Motion Engine ("Breathing" Scene)

When the user stops scrolling, the scene must continue to breathe gently to avoid feeling frozen.

```js
function applyIdleMotion(elapsed, prefersReduced) {
  if (prefersReduced) return;

  // Gentle harmonic oscillations (different frequencies per axis prevent pendulum feel)
  heroGroup.position.y += Math.sin(elapsed * 0.9) * 0.035;
  heroGroup.rotation.y += Math.sin(elapsed * 0.4) * 0.008;
  heroGroup.rotation.z += Math.cos(elapsed * 0.5) * 0.005;
}
```

---

## 7. Dynamic Scroll Velocity Warp (Optional High-Impact Effect)

For high-energy or futuristic sites, warping the camera FOV or stretching the object slightly during fast scrolls creates dramatic kinetic feedback.

```js
let lastScrollProgress = 0;
let scrollVelocity = 0;

function computeScrollVelocity() {
  const delta = targetProgress - lastScrollProgress;
  lastScrollProgress = targetProgress;
  // Damped velocity
  scrollVelocity += (Math.abs(delta) * 60 - scrollVelocity) * 0.1;
}

// In applyProgressToScene:
// camera.fov += scrollVelocity * 5.0; // slight speed-warp zoom effect
```

---

## 8. Master Animation Loop Assembly

```js
const clock = new THREE.Clock();

function animate() {
  requestAnimationFrame(animate);
  const elapsed = clock.getElapsedTime();

  // 1. Smooth scroll progress
  currentProgress += (targetProgress - currentProgress) * dampingFactor;

  // 2. Apply scroll keyframes
  applyProgressToScene(currentProgress);

  // 3. Layer mouse parallax
  updateParallax(elapsed);

  // 4. Layer idle breathing motion
  applyIdleMotion(elapsed, prefersReducedMotion);

  // 5. Look at target and render
  camera.lookAt(heroGroup.position);
  renderer.render(scene, camera);
}
```
