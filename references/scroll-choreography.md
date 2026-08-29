# Scroll Choreography & Motion Design

Transforming raw scroll position into smooth 3D motion requires physics damping, non-linear easing curves, and mouse parallax layering. This file covers the mechanics.

---

## 1. Scroll Progress Driver

Always clamp progress to `[0, 1]` — macOS trackpad rubber-banding and mobile Safari URL-bar shifts can produce values outside this range.

```js
function getScrollProgress() {
  const maxScroll = document.documentElement.scrollHeight - window.innerHeight;
  if (maxScroll <= 0) return 0;
  return Math.max(0, Math.min(1, window.scrollY / maxScroll));
}
```

---

## 2. Damping Loop

Never assign raw scroll progress directly to transforms. Use a damped variable in `requestAnimationFrame` that exponentially approaches the target — this is a simple first-order IIR filter, not "physics simulation," but it produces smooth organic lag:

```js
let targetProgress = 0;
let currentProgress = 0;
const dampingFactor = 0.08; // 0.05 = slow dreamy, 0.12 = snappy

// Separate variables for mouse parallax
let mouseTargetX = 0, mouseTargetY = 0;
let mouseCurrentX = 0, mouseCurrentY = 0;

window.addEventListener('scroll', () => {
  targetProgress = getScrollProgress();
}, { passive: true });

window.addEventListener('pointermove', (e) => {
  mouseTargetX = (e.clientX / window.innerWidth) * 2 - 1;   // -1 to +1
  mouseTargetY = -(e.clientY / window.innerHeight) * 2 + 1; // -1 to +1
}, { passive: true });
```

Inside `animate()`:
```js
currentProgress += (targetProgress - currentProgress) * dampingFactor;
mouseCurrentX += (mouseTargetX - mouseCurrentX) * 0.05;
mouseCurrentY += (mouseTargetY - mouseCurrentY) * 0.05;
```

---

## 3. Non-Linear Easing (Apply Before Driving Transforms)

Linear interpolation between keyframes produces mechanical, robotic movement. Apply a curve to `currentProgress` before using it to drive transforms:

```js
// Smoothstep: standard ease-in-out (Ken Perlin's first version)
function smoothstep(t) {
  t = Math.max(0, Math.min(1, t));
  return t * t * (3 - 2 * t);
}

// Smootherstep: Ken Perlin's quintic — gentler at both ends, better for slow cinematic sweeps
function smootherstep(t) {
  t = Math.max(0, Math.min(1, t));
  return t * t * t * (t * (t * 6 - 15) + 10);
}

// Ease-out cubic: decelerates into a stop (arrival, landing, reveal)
function easeOutCubic(t) {
  t = Math.max(0, Math.min(1, t));
  return 1 - Math.pow(1 - t, 3);
}

// Ease-in cubic: accelerates from rest (takeoff, launch)
function easeInCubic(t) {
  t = Math.max(0, Math.min(1, t));
  return t * t * t;
}
```

Usage: apply AFTER computing `currentProgress`, BEFORE using it to drive scene transforms:
```js
const eased = smootherstep(currentProgress);
applyProgressToScene(eased);
```

---

## 4. Keyframe Timeline System

Define named states at positions along the 0→1 timeline. Interpolate between adjacent keyframes using the easing functions above.

```js
function lerp(a, b, t) { return a + (b - a) * t; }

const timeline = [
  { t: 0.0,  rotY: 0,             posY: 0,   fov: 55 },
  { t: 0.35, rotY: Math.PI * 0.7, posY: 0.5, fov: 48 },
  { t: 0.70, rotY: Math.PI * 1.4, posY: 1.2, fov: 44 },
  { t: 1.0,  rotY: Math.PI * 2.0, posY: 0.0, fov: 40 },
];

function applyProgressToScene(p) {
  p = Math.max(0, Math.min(1, p));

  // Find active keyframe interval
  let i = 0;
  while (i < timeline.length - 2 && p > timeline[i + 1].t) i++;
  const a = timeline[i], b = timeline[i + 1];

  const span = b.t - a.t || 1;
  const localT = smoothstep((p - a.t) / span); // easing applied per-segment

  heroGroup.rotation.y = lerp(a.rotY, b.rotY, localT);
  heroGroup.position.y = lerp(a.posY, b.posY, localT);
  camera.fov = lerp(a.fov, b.fov, localT);
  camera.updateProjectionMatrix();
}
```

---

## 5. Cursor Parallax

Mouse position adds a secondary offset layered on top of scroll transforms. Keep offsets small — this is a subtle depth cue, not an interactive camera controller.

```js
function applyParallax() {
  // Offset camera position by a fixed small amount — not additive per-frame
  camera.position.x = mouseCurrentX * 0.6;
  camera.position.y = baseYFromScroll + mouseCurrentY * 0.3;

  // Subtle object tilt
  heroGroup.rotation.x = mouseCurrentY * 0.08;
  heroGroup.rotation.z = -mouseCurrentX * 0.06;
}
```

**Important**: `camera.position.x = mouseCurrentX * 0.6` (assignment) not `camera.position.x += ...` (accumulation). Accumulation causes unbounded drift every frame.

---

## 6. Idle Micro-Motion ("Breathing")

When the user stops scrolling, a static scene looks frozen. A slow sinusoidal offset on position or rotation reads as "alive" without feeling like unwanted motion. Amplitudes should be small enough that the effect is noticed only when the viewer is still, not when they are scrolling.

```js
function applyIdleMotion(elapsed, prefersReducedMotion) {
  if (prefersReducedMotion) return; // Hard gate — no motion if user has reduced motion on

  // Different frequencies per axis prevent a pendulum rhythm
  heroGroup.position.y += Math.sin(elapsed * 0.9) * 0.018;
  heroGroup.rotation.y += Math.sin(elapsed * 0.35) * 0.004;
  heroGroup.rotation.z += Math.cos(elapsed * 0.5) * 0.003;
}
```

Note: these offsets are applied on top of the scroll-driven values each frame. They do not replace or accumulate against scroll position.

`prefers-reduced-motion` check:
```js
const prefersReducedMotion = window.matchMedia('(prefers-reduced-motion: reduce)').matches;
```

---

## 7. Full Render Loop Assembly

```js
const clock = new THREE.Clock();
let baseYFromScroll = 0; // set this from applyProgressToScene before applyParallax

function animate() {
  requestAnimationFrame(animate);
  const elapsed = clock.getElapsedTime();

  // 1. Damp scroll and mouse toward targets
  currentProgress += (targetProgress - currentProgress) * dampingFactor;
  mouseCurrentX += (mouseTargetX - mouseCurrentX) * 0.05;
  mouseCurrentY += (mouseTargetY - mouseCurrentY) * 0.05;

  // 2. Apply eased scroll keyframes
  const eased = smootherstep(currentProgress);
  applyProgressToScene(eased);

  // 3. Layer cursor parallax offset (assignment, not accumulation)
  applyParallax();

  // 4. Layer idle breathing (gated behind prefers-reduced-motion)
  applyIdleMotion(elapsed, prefersReducedMotion);

  // 5. Render
  camera.lookAt(heroGroup.position);
  renderer.render(scene, camera);
}
```

---

## 8. Optional: Scroll Velocity Feedback

For high-energy scenes, warp the camera FOV slightly during fast scroll movement to give kinetic feedback:

```js
let lastProgress = 0;
let scrollVelocity = 0;

function computeVelocity() {
  const delta = Math.abs(targetProgress - lastProgress);
  lastProgress = targetProgress;
  scrollVelocity += (delta * 60 - scrollVelocity) * 0.1; // exponential smooth
}

// Inside animate() — apply a small warp on top of keyframe FOV:
// camera.fov += scrollVelocity * 4.0;
// camera.updateProjectionMatrix();
```

Use sparingly — FOV warping reads as disorienting if the effect is too strong or applied to calm scenes.
