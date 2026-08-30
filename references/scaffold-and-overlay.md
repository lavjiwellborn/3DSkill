# Complete Scaffold & Overlay Architecture

This file provides **production-ready, copy-paste scaffolds** for procedural 3D scroll websites. Every core subsystem (tone mapping, three-point cross lighting, contact shadow catcher, non-linear damping, interactive cursor parallax, mobile FOV compensation, loading transitions, glassmorphic text overlays, accessibility, and CDN error handling) is pre-wired.

> **IMPORTANT**: Always start from this scaffold. Do NOT piece together fragments from other reference files. This scaffold already includes all essential subsystems.

---

## 1. Standalone HTML5 Single-File Scaffold (Most Common)

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Procedural 3D Experience</title>
  <style>
    /* ===== RESET & BASE ===== */
    *, *::before, *::after { margin: 0; padding: 0; box-sizing: border-box; }
    html { scroll-behavior: auto; }
    body {
      font-family: 'Inter', system-ui, -apple-system, sans-serif;
      color: #f8fafc;
      background: #090d16;
      overflow-x: hidden;
      -webkit-font-smoothing: antialiased;
    }

    /* ===== WEBGL FALLBACK & LOADING ===== */
    .loading-screen {
      position: fixed; inset: 0; z-index: 1000;
      background: #090d16;
      display: flex; flex-direction: column; align-items: center; justify-content: center;
      transition: opacity 0.8s ease-out, visibility 0.8s ease-out;
    }
    .loading-screen.hidden { opacity: 0; visibility: hidden; pointer-events: none; }
    .spinner {
      width: 44px; height: 44px;
      border: 3px solid rgba(255,255,255,0.1);
      border-top-color: #38bdf8;
      border-radius: 50%;
      animation: spin 0.8s linear infinite;
    }
    @keyframes spin { to { transform: rotate(360deg); } }

    /* ===== 3D CANVAS VIEWPORT ===== */
    #webgl-canvas {
      position: fixed;
      inset: 0;
      width: 100%;
      height: 100%;
      z-index: 0;
      display: block;
      pointer-events: none;
    }

    /* ===== SCROLL CONTENT OVERLAY ===== */
    .scroll-container {
      position: relative;
      z-index: 10;
      pointer-events: none; /* Let scroll events pass through to window */
    }

    .scroll-section {
      min-height: 100vh;
      display: flex;
      align-items: center;
      padding: 6rem 8%;
    }

    /* Alternating section alignment */
    .scroll-section:nth-child(odd) { justify-content: flex-start; }
    .scroll-section:nth-child(even) { justify-content: flex-end; }
    .scroll-section:first-child {
      justify-content: center;
      text-align: center;
      align-items: flex-end;
      padding-bottom: 15vh;
    }

    /* ===== GLASSMORPHIC TEXT CARDS ===== */
    .glass-card {
      max-width: 540px;
      padding: 2.5rem;
      background: rgba(15, 23, 42, 0.65);
      backdrop-filter: blur(16px);
      -webkit-backdrop-filter: blur(16px);
      border: 1px solid rgba(255, 255, 255, 0.1);
      border-radius: 20px;
      box-shadow: 0 20px 50px rgba(0, 0, 0, 0.4);
      pointer-events: auto; /* Re-enable click / text selection */
      opacity: 0;
      transform: translateY(30px);
      transition: opacity 0.8s cubic-bezier(0.16, 1, 0.3, 1), transform 0.8s cubic-bezier(0.16, 1, 0.3, 1);
    }

    .glass-card.is-visible {
      opacity: 1;
      transform: translateY(0);
    }

    .glass-card h1, .glass-card h2 {
      font-size: clamp(2rem, 4vw, 3rem);
      font-weight: 700;
      letter-spacing: -0.02em;
      line-height: 1.15;
      margin-bottom: 1rem;
      color: #ffffff;
      text-shadow: 0 2px 10px rgba(0,0,0,0.5);
    }

    .glass-card p {
      font-size: clamp(1rem, 1.8vw, 1.15rem);
      line-height: 1.7;
      color: #94a3b8;
    }

    .badge {
      display: inline-block;
      padding: 0.35rem 0.85rem;
      font-size: 0.75rem;
      font-weight: 600;
      text-transform: uppercase;
      letter-spacing: 0.08em;
      color: #38bdf8;
      background: rgba(56, 189, 248, 0.12);
      border: 1px solid rgba(56, 189, 248, 0.3);
      border-radius: 999px;
      margin-bottom: 1rem;
    }

    @media (max-width: 768px) {
      .scroll-section { padding: 4rem 1.5rem; justify-content: center !important; }
      .glass-card { padding: 1.75rem; }
    }
  </style>
</head>
<body>

  <!-- Accessibility: noscript fallback for no-JS environments -->
  <noscript>
    <div style="position:fixed;inset:0;display:flex;align-items:center;justify-content:center;background:#090d16;color:#f8fafc;font-family:system-ui;text-align:center;padding:2rem;">
      <p>This experience requires JavaScript and WebGL. Please enable JavaScript in your browser.</p>
    </div>
  </noscript>

  <!-- Loading Screen (auto-dismissed after 8s timeout as safety net) -->
  <div class="loading-screen" id="loading-screen" role="status" aria-live="polite">
    <div class="spinner" aria-hidden="true"></div>
    <p style="margin-top: 1.2rem; font-size: 0.9rem; color: #64748b; letter-spacing: 0.05em;" id="loading-text">INITIALIZING 3D ENGINE</p>
  </div>

  <!-- Three.js Canvas -->
  <canvas id="webgl-canvas" role="img" aria-label="Interactive 3D procedural scene"></canvas>

  <!-- Overlay Content -->
  <main class="scroll-container">
    <section class="scroll-section">
      <div class="glass-card" style="background: transparent; border: none; box-shadow: none; text-align: center;">
        <span class="badge">Procedural WebGL</span>
        <h1>Cinematic 3D Scroll</h1>
        <p>Scroll down to explore real-time procedural geometry.</p>
      </div>
    </section>

    <section class="scroll-section">
      <div class="glass-card">
        <span class="badge">Architecture</span>
        <h2>Precision Geometry</h2>
        <p>Built from pure mathematical primitives and rendered live on your GPU. Zero video assets, zero pre-rendered sprite frames.</p>
      </div>
    </section>

    <section class="scroll-section">
      <div class="glass-card">
        <span class="badge">Lighting & Mood</span>
        <h2>ACES Tone Mapping</h2>
        <p>Filmic color compression, dynamic three-point cross lighting, and soft real-time contact shadows.</p>
      </div>
    </section>

    <section class="scroll-section">
      <div class="glass-card">
        <span class="badge">Interactive</span>
        <h2>Harmonic Damping</h2>
        <p>Non-linear easing combined with subtle cursor parallax makes the viewport feel reactive and tactile.</p>
      </div>
    </section>
  </main>

  <script type="module">
    // CDN Import with version pinning
    // To update Three.js version, change this single URL:
    const THREE_CDN = 'https://cdn.jsdelivr.net/npm/three@0.170.0/build/three.module.js';
    let THREE;
    try {
      THREE = await import(THREE_CDN);
    } catch (err) {
      // Graceful CDN failure: show static fallback instead of blank page
      console.error('Three.js CDN failed to load:', err);
      document.getElementById('loading-text').textContent = 'Failed to load 3D engine. Showing static view.';
      document.getElementById('loading-screen').classList.add('hidden');
      document.body.style.background = '#090d16';
      // Optionally render a static CSS-only hero instead of crashing
      throw err; // Stop execution cleanly
    }

    // ===== 1. CORE SETUP =====
    const canvas = document.getElementById('webgl-canvas');
    const prefersReducedMotion = window.matchMedia('(prefers-reduced-motion: reduce)').matches;

    const renderer = new THREE.WebGLRenderer({
      canvas,
      antialias: true,
      powerPreference: 'high-performance',
    });
    renderer.setPixelRatio(Math.min(window.devicePixelRatio, window.innerWidth < 768 ? 1.5 : 2.0));
    renderer.setSize(window.innerWidth, window.innerHeight);
    renderer.toneMapping = THREE.ACESFilmicToneMapping;
    renderer.toneMappingExposure = 1.0;
    renderer.outputColorSpace = THREE.SRGBColorSpace;
    renderer.shadowMap.enabled = true;
    renderer.shadowMap.type = THREE.PCFSoftShadowMap;

    const scene = new THREE.Scene();
    scene.fog = new THREE.FogExp2(0x090d16, 0.04);
    renderer.setClearColor(0x090d16);

    const camera = new THREE.PerspectiveCamera(50, window.innerWidth / window.innerHeight, 0.1, 100);
    camera.position.set(0, 1.5, 8);

    // ===== 2. LIGHTING RIG =====
    const ambient = new THREE.AmbientLight(0x384252, 0.6);
    scene.add(ambient);

    const keyLight = new THREE.DirectionalLight(0xfff0dd, 1.3);
    keyLight.position.set(5, 8, 4);
    keyLight.castShadow = true;
    keyLight.shadow.mapSize.set(1024, 1024);
    keyLight.shadow.camera.near = 0.5;
    keyLight.shadow.camera.far = 25;
    keyLight.shadow.camera.left = -6;
    keyLight.shadow.camera.right = 6;
    keyLight.shadow.camera.top = 6;
    keyLight.shadow.camera.bottom = -6;
    keyLight.shadow.bias = -0.0001;
    keyLight.shadow.normalBias = 0.02;
    scene.add(keyLight);

    const rimLight = new THREE.DirectionalLight(0x38bdf8, 0.6);
    rimLight.position.set(-4, 3, -6);
    scene.add(rimLight);

    // ===== 3. GROUND SHADOW CATCHER =====
    const shadowPlane = new THREE.Mesh(
      new THREE.PlaneGeometry(25, 25),
      new THREE.ShadowMaterial({ opacity: 0.35 })
    );
    shadowPlane.rotation.x = -Math.PI / 2;
    shadowPlane.position.y = -0.01;
    shadowPlane.receiveShadow = true;
    scene.add(shadowPlane);

    // ===== 4. PROCEDURAL HERO OBJECT (REPLACE WITH YOUR OBJECT) =====
    const heroGroup = new THREE.Group();

    // Example Hero Geometry (Faceted Diamond Pillar)
    const geo = new THREE.CylinderGeometry(0.8, 1.2, 2.8, 6);
    const mat = new THREE.MeshStandardMaterial({
      color: 0x38bdf8,
      roughness: 0.2,
      metalness: 0.85,
      flatShading: true,
    });
    const mesh = new THREE.Mesh(geo, mat);
    mesh.position.y = 1.4;
    mesh.castShadow = true;
    heroGroup.add(mesh);

    scene.add(heroGroup);

    // ===== 5. ATMOSPHERIC PARTICLES =====
    const pCount = window.innerWidth < 768 ? 75 : 150;
    const pPos = new Float32Array(pCount * 3);
    for (let i = 0; i < pCount; i++) {
      pPos[i * 3]     = (Math.random() - 0.5) * 14;
      pPos[i * 3 + 1] = Math.random() * 8;
      pPos[i * 3 + 2] = (Math.random() - 0.5) * 14;
    }
    const pGeo = new THREE.BufferGeometry();
    pGeo.setAttribute('position', new THREE.BufferAttribute(pPos, 3));
    const pMat = new THREE.PointsMaterial({
      color: 0x38bdf8,
      size: 0.035,
      transparent: true,
      opacity: 0.5,
      depthWrite: false,
    });
    const particles = new THREE.Points(pGeo, pMat);
    scene.add(particles);

    // ===== 6. SCROLL & CURSOR PARALLAX ENGINE =====
    let targetProgress = 0, currentProgress = 0;
    let mouseX = 0, mouseY = 0, curMouseX = 0, curMouseY = 0;
    const damp = prefersReducedMotion ? 0.25 : 0.08;

    function getProgress() {
      const max = document.documentElement.scrollHeight - window.innerHeight;
      return max > 0 ? Math.max(0, Math.min(1, window.scrollY / max)) : 0;
    }

    window.addEventListener('scroll', () => { targetProgress = getProgress(); }, { passive: true });
    window.addEventListener('pointermove', (e) => {
      mouseX = (e.clientX / window.innerWidth) * 2 - 1;
      mouseY = -(e.clientY / window.innerHeight) * 2 + 1;
    }, { passive: true });

    // Timeline Easing
    function lerp(a, b, t) { return a + (b - a) * t; }
    function smoothstep(t) { return t * t * (3 - 2 * t); }

    const timeline = [
      { t: 0.0,  rotY: 0,             posY: 0,   fov: 50 },
      { t: 0.35, rotY: Math.PI * 0.7, posY: 0.5, fov: 46 },
      { t: 0.70, rotY: Math.PI * 1.4, posY: 1.0, fov: 42 },
      { t: 1.0,  rotY: Math.PI * 2.0, posY: 0.2, fov: 38 },
    ];

    function applyTimeline(p) {
      let i = 0;
      while (i < timeline.length - 2 && p > timeline[i + 1].t) i++;
      const a = timeline[i], b = timeline[i + 1];
      const localT = smoothstep((p - a.t) / (b.t - a.t || 1));

      heroGroup.rotation.y = lerp(a.rotY, b.rotY, localT);
      heroGroup.position.y = lerp(a.posY, b.posY, localT);
      camera.fov = lerp(a.fov, b.fov, localT);
      camera.updateProjectionMatrix();
    }

    // ===== 7. RENDER LOOP =====
    const clock = new THREE.Clock();

    function animate() {
      requestAnimationFrame(animate);
      const elapsed = clock.getElapsedTime();

      // Scroll progress damping
      currentProgress += (targetProgress - currentProgress) * damp;
      applyTimeline(currentProgress);

      // Cursor parallax
      curMouseX += (mouseX - curMouseX) * 0.05;
      curMouseY += (mouseY - curMouseY) * 0.05;
      camera.position.x = curMouseX * 0.6;
      camera.position.y = 1.5 + curMouseY * 0.3;

      // Idle breathing
      if (!prefersReducedMotion) {
        heroGroup.position.y += Math.sin(elapsed * 0.8) * 0.03;
        heroGroup.rotation.z = Math.sin(elapsed * 0.4) * 0.02;
      }

      particles.rotation.y += 0.0003;
      camera.lookAt(0, heroGroup.position.y + 0.5, 0);

      renderer.render(scene, camera);
    }

    // ===== 8. RESPONSIVE RESIZE (Mobile FOV Compensation) =====
    function onResize() {
      const w = window.innerWidth, h = window.innerHeight;
      const aspect = w / h;
      camera.aspect = aspect;
      if (aspect < 1.0) {
        camera.fov = Math.min(80, 50 / aspect * 0.78);
      } else {
        camera.fov = 50;
      }
      camera.updateProjectionMatrix();
      renderer.setSize(w, h);
      renderer.setPixelRatio(Math.min(window.devicePixelRatio, w < 768 ? 1.5 : 2.0));
    }
    window.addEventListener('resize', onResize);

    // ===== 9. TEXT REVEAL OBSERVER =====
    const cards = document.querySelectorAll('.glass-card');
    const observer = new IntersectionObserver((entries) => {
      entries.forEach((e) => {
        if (e.isIntersecting) e.target.classList.add('is-visible');
      });
    }, { threshold: 0.2 });
    cards.forEach((c) => observer.observe(c));

    // Remove loading screen on first rendered frame
    animate();
    requestAnimationFrame(() => {
      document.getElementById('loading-screen').classList.add('hidden');
    });

    // Safety net: always dismiss loading screen after 8 seconds
    // (prevents infinite spinner if something silently fails)
    setTimeout(() => {
      const ls = document.getElementById('loading-screen');
      if (ls && !ls.classList.contains('hidden')) {
        ls.classList.add('hidden');
        console.warn('Loading screen auto-dismissed after timeout.');
      }
    }, 8000);
  </script>
</body>
</html>
```

---

## 2. React / Next.js / Vite Component Scaffold

```tsx
import React, { useEffect, useRef } from 'react';
import * as THREE from 'three';

export function ProceduralScene() {
  const mountRef = useRef<HTMLDivElement>(null);

  useEffect(() => {
    const container = mountRef.current;
    if (!container) return;

    const prefersReducedMotion = window.matchMedia('(prefers-reduced-motion: reduce)').matches;

    // Renderer
    const renderer = new THREE.WebGLRenderer({ antialias: true, alpha: false, powerPreference: 'high-performance' });
    renderer.setPixelRatio(Math.min(window.devicePixelRatio, window.innerWidth < 768 ? 1.5 : 2.0));
    renderer.setSize(window.innerWidth, window.innerHeight);
    renderer.toneMapping = THREE.ACESFilmicToneMapping;
    renderer.toneMappingExposure = 1.0;
    renderer.outputColorSpace = THREE.SRGBColorSpace;
    renderer.shadowMap.enabled = true;
    renderer.shadowMap.type = THREE.PCFSoftShadowMap;
    renderer.domElement.style.cssText = 'position:fixed;inset:0;width:100%;height:100%;z-index:0;pointer-events:none;';
    container.appendChild(renderer.domElement);

    // Scene & Camera
    const scene = new THREE.Scene();
    scene.fog = new THREE.FogExp2(0x090d16, 0.04);
    renderer.setClearColor(0x090d16);

    const camera = new THREE.PerspectiveCamera(50, window.innerWidth / window.innerHeight, 0.1, 100);
    camera.position.set(0, 1.5, 8);

    // Lights
    scene.add(new THREE.AmbientLight(0x384252, 0.6));
    const keyLight = new THREE.DirectionalLight(0xfff0dd, 1.3);
    keyLight.position.set(5, 8, 4);
    keyLight.castShadow = true;
    keyLight.shadow.mapSize.set(1024, 1024);
    keyLight.shadow.bias = -0.0001;
    scene.add(keyLight);
    scene.add(new THREE.DirectionalLight(0x38bdf8, 0.6).translateX(-4).translateY(3).translateZ(-6));

    // Shadow catcher
    const shadowPlane = new THREE.Mesh(
      new THREE.PlaneGeometry(25, 25),
      new THREE.ShadowMaterial({ opacity: 0.35 })
    );
    shadowPlane.rotation.x = -Math.PI / 2;
    shadowPlane.position.y = -0.01;
    shadowPlane.receiveShadow = true;
    scene.add(shadowPlane);

    // Hero Object Group
    const heroGroup = new THREE.Group();
    // (Attach your procedural geometry to heroGroup here)
    scene.add(heroGroup);

    // Scroll & Mouse Tracking
    let targetProgress = 0, currentProgress = 0;
    let mouseX = 0, mouseY = 0, curMouseX = 0, curMouseY = 0;
    const damp = prefersReducedMotion ? 0.25 : 0.08;

    const handleScroll = () => {
      const max = document.documentElement.scrollHeight - window.innerHeight;
      targetProgress = max > 0 ? Math.max(0, Math.min(1, window.scrollY / max)) : 0;
    };
    const handlePointer = (e: PointerEvent) => {
      mouseX = (e.clientX / window.innerWidth) * 2 - 1;
      mouseY = -(e.clientY / window.innerHeight) * 2 + 1;
    };

    window.addEventListener('scroll', handleScroll, { passive: true });
    window.addEventListener('pointermove', handlePointer, { passive: true });

    // Resize handler
    const handleResize = () => {
      const w = window.innerWidth, h = window.innerHeight;
      const aspect = w / h;
      camera.aspect = aspect;
      camera.fov = aspect < 1.0 ? Math.min(80, 50 / aspect * 0.78) : 50;
      camera.updateProjectionMatrix();
      renderer.setSize(w, h);
    };
    window.addEventListener('resize', handleResize);

    // Render loop
    const clock = new THREE.Clock();
    let animId: number;

    const animate = () => {
      animId = requestAnimationFrame(animate);
      const elapsed = clock.getElapsedTime();

      currentProgress += (targetProgress - currentProgress) * damp;
      heroGroup.rotation.y = currentProgress * Math.PI * 2;
      
      curMouseX += (mouseX - curMouseX) * 0.05;
      curMouseY += (mouseY - curMouseY) * 0.05;
      camera.position.x = curMouseX * 0.6;
      camera.position.y = 1.5 + curMouseY * 0.3;

      if (!prefersReducedMotion) {
        heroGroup.position.y = Math.sin(elapsed * 0.8) * 0.03;
      }

      camera.lookAt(0, heroGroup.position.y + 0.5, 0);
      renderer.render(scene, camera);
    };
    animate();

    // Cleanup on unmount
    return () => {
      cancelAnimationFrame(animId);
      window.removeEventListener('scroll', handleScroll);
      window.removeEventListener('pointermove', handlePointer);
      window.removeEventListener('resize', handleResize);

      scene.traverse((obj) => {
        if ((obj as THREE.Mesh).geometry) (obj as THREE.Mesh).geometry.dispose();
        if ((obj as THREE.Mesh).material) {
          const mats = Array.isArray((obj as THREE.Mesh).material) ? (obj as THREE.Mesh).material : [(obj as THREE.Mesh).material];
          (mats as THREE.Material[]).forEach((m) => m.dispose());
        }
      });
      renderer.dispose();
      if (container.contains(renderer.domElement)) {
        container.removeChild(renderer.domElement);
      }
    };
  }, []);

  return <div ref={mountRef} />;
}
```

---

## 3. Mode B Standalone HTML5 Frame-Sequence Scaffold (Canvas + Windowed Cache)

Use this copy-paste scaffold when building a **Mode B (Frame-Sequence Scroller)** experience with pre-existing video/image frame assets:

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Mode B Frame-Sequence Scroller</title>
  <style>
    *, *::before, *::after { margin: 0; padding: 0; box-sizing: border-box; }
    html { scroll-behavior: auto; }
    body {
      font-family: 'Inter', system-ui, -apple-system, sans-serif;
      color: #f8fafc;
      background: #090d16;
      overflow-x: hidden;
      -webkit-font-smoothing: antialiased;
    }
    .loading-screen {
      position: fixed; inset: 0; z-index: 1000;
      background: #090d16;
      display: flex; flex-direction: column; align-items: center; justify-content: center;
      transition: opacity 0.8s ease-out, visibility 0.8s ease-out;
    }
    .loading-screen.hidden { opacity: 0; visibility: hidden; pointer-events: none; }
    .spinner {
      width: 44px; height: 44px;
      border: 3px solid rgba(56, 189, 248, 0.15);
      border-top-color: #38bdf8;
      border-radius: 50%;
      animation: spin 0.8s linear infinite;
    }
    @keyframes spin { to { transform: rotate(360deg); } }

    /* Level 1: Back Typography */
    .headline-backdrop {
      position: fixed; inset: 0; z-index: 1;
      display: flex; align-items: center; justify-content: center;
      font-size: clamp(4rem, 15vw, 14rem); font-weight: 900;
      letter-spacing: -0.05em; color: rgba(255, 255, 255, 0.05);
      pointer-events: none; user-select: none;
    }

    /* Level 2: 2D Canvas Viewport */
    #frame-canvas {
      position: fixed; inset: 0; width: 100%; height: 100%; z-index: 2; display: block; pointer-events: none;
    }

    /* Level 3: Overlay Scroll Narrative */
    .scroll-container { position: relative; z-index: 10; pointer-events: none; }
    .scroll-section { min-height: 100vh; display: flex; align-items: center; padding: 6rem 8%; }
    .scroll-section:nth-child(odd) { justify-content: flex-start; }
    .scroll-section:nth-child(even) { justify-content: flex-end; }
    .scroll-section:first-child { justify-content: center; text-align: center; align-items: flex-end; padding-bottom: 15vh; }

    .glass-card {
      max-width: 520px; padding: 2.5rem;
      background: rgba(15, 23, 42, 0.8);
      backdrop-filter: blur(18px); -webkit-backdrop-filter: blur(18px);
      border: 1px solid rgba(56, 189, 248, 0.25); border-radius: 20px;
      box-shadow: 0 20px 50px rgba(0, 0, 0, 0.5);
      pointer-events: auto; opacity: 0; transform: translateY(30px);
      transition: opacity 0.8s cubic-bezier(0.16, 1, 0.3, 1), transform 0.8s cubic-bezier(0.16, 1, 0.3, 1);
    }
    .glass-card.is-visible { opacity: 1; transform: translateY(0); }
    .glass-card h1, .glass-card h2 { font-size: clamp(2rem, 4vw, 2.8rem); font-weight: 700; margin-bottom: 1rem; color: #fff; }
    .glass-card p { font-size: 1.1rem; line-height: 1.7; color: #cbd5e1; }
  </style>
</head>
<body>

  <div class="loading-screen" id="loading-screen" role="status" aria-live="polite">
    <div class="spinner" aria-hidden="true"></div>
    <p style="margin-top: 1.2rem; font-size: 0.85rem; color: #38bdf8; letter-spacing: 0.08em;">LOADING SEQUENCE</p>
  </div>

  <div class="headline-backdrop">CINEMATIC</div>
  <canvas id="frame-canvas" role="img" aria-label="Interactive frame sequence"></canvas>

  <main class="scroll-container">
    <section class="scroll-section">
      <div class="glass-card" style="background: transparent; border: none; box-shadow: none;">
        <h1>Mode B Scroller</h1>
        <p>Scroll down to scrub high-fidelity frame sequences smoothly.</p>
      </div>
    </section>
    <section class="scroll-section">
      <div class="glass-card">
        <h2>Sub-Frame Smoothing</h2>
        <p>Adjacent frames blend dynamically to prevent stepped motion.</p>
      </div>
    </section>
  </main>

  <script>
    // Configure your frame URLs (e.g. 720p WebP sequence)
    const frameUrls = Array.from({ length: 60 }, (_, i) => `frames/frame_${String(i).padStart(4, '0')}.webp`);

    class FrameCache {
      constructor(urls, w = 1280, h = 720, budget = 90 * 1024 * 1024) {
        this.frameUrls = urls;
        this.frameWidth = w;
        this.frameHeight = h;
        const maxResident = Math.max(5, Math.floor(budget / (w * h * 4)));
        this.windowRadius = Math.floor((maxResident - 1) / 2);
        this.cache = new Map();
        this.pending = new Map();
        this.supportsImageBitmap = typeof createImageBitmap === 'function';
      }
      async ensureWindow(center) {
        const lo = Math.max(0, center - this.windowRadius);
        const hi = Math.min(this.frameUrls.length - 1, center + this.windowRadius);
        for (let i = lo; i <= hi; i++) {
          if (!this.cache.has(i) && !this.pending.has(i)) this.pending.set(i, this._decode(i));
        }
        for (const i of [...this.cache.keys()]) {
          if (i < lo || i > hi) {
            const b = this.cache.get(i);
            if (b && typeof b.close === 'function') b.close();
            this.cache.delete(i);
          }
        }
      }
      async _decode(i) {
        try {
          if (this.supportsImageBitmap) {
            const resp = await fetch(this.frameUrls[i]);
            const blob = await resp.blob();
            const bmp = await createImageBitmap(blob);
            this.cache.set(i, bmp);
            this.pending.delete(i);
            return bmp;
          } else {
            return new Promise((resolve, reject) => {
              const img = new Image();
              img.onload = () => { this.cache.set(i, img); this.pending.delete(i); resolve(img); };
              img.onerror = reject;
              img.src = this.frameUrls[i];
            });
          }
        } catch (e) { this.pending.delete(i); return null; }
      }
      get(i) { return this.cache.get(i) || null; }
    }

    const canvas = document.getElementById('frame-canvas');
    const ctx = canvas.getContext('2d');
    const cache = new FrameCache(frameUrls);
    const prefersReducedMotion = window.matchMedia('(prefers-reduced-motion: reduce)').matches;

    function resize() {
      const dpr = Math.min(window.devicePixelRatio || 1, 2);
      canvas.width = window.innerWidth * dpr;
      canvas.height = window.innerHeight * dpr;
      ctx.scale(dpr, dpr);
    }
    window.addEventListener('resize', resize);
    resize();

    function drawCover(ctx, src, cw, ch) {
      const sw = src.width, sh = src.height;
      const cvA = cw / ch, srcA = sw / sh;
      let sx, sy, sW, sH;
      if (srcA > cvA) { sH = sh; sW = sH * cvA; sx = (sw - sW) / 2; sy = 0; }
      else { sW = sw; sH = sW / cvA; sx = 0; sy = (sh - sH) / 2; }
      ctx.drawImage(src, sx, sy, sW, sH, 0, 0, cw, ch);
    }

    function drawSubFrame(ctx, cache, floatIdx, cw, ch) {
      const max = cache.frameUrls.length - 1;
      const clamped = Math.max(0, Math.min(max, floatIdx));
      const lo = Math.floor(clamped), hi = Math.min(lo + 1, max), frac = clamped - lo;
      const bmpLo = cache.get(lo);
      if (!bmpLo) return;
      ctx.clearRect(0, 0, cw, ch);
      ctx.globalAlpha = 1.0;
      drawCover(ctx, bmpLo, cw, ch);
      const bmpHi = cache.get(hi);
      if (bmpHi && frac > 0.01) {
        ctx.globalAlpha = frac;
        drawCover(ctx, bmpHi, cw, ch);
        ctx.globalAlpha = 1.0;
      }
    }

    let target = 0, current = 0;
    const damp = prefersReducedMotion ? 0.25 : 0.08;
    window.addEventListener('scroll', () => {
      const max = document.documentElement.scrollHeight - window.innerHeight;
      target = max > 0 ? Math.max(0, Math.min(1, window.scrollY / max)) : 0;
    }, { passive: true });

    function animate() {
      requestAnimationFrame(animate);
      current += (target - current) * damp;
      const floatFrame = current * (frameUrls.length - 1);
      cache.ensureWindow(Math.round(floatFrame));
      drawSubFrame(ctx, cache, floatFrame, window.innerWidth, window.innerHeight);
    }

    const observer = new IntersectionObserver((entries) => {
      entries.forEach(e => { if (e.isIntersecting) e.target.classList.add('is-visible'); });
    }, { threshold: 0.2 });
    document.querySelectorAll('.glass-card').forEach(c => observer.observe(c));

    cache.ensureWindow(0).then(() => {
      document.getElementById('loading-screen').classList.add('hidden');
      animate();
    });
    setTimeout(() => document.getElementById('loading-screen').classList.add('hidden'), 6000);
  </script>
</body>
</html>
```

