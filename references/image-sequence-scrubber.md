# Image-Sequence Frame Scrubber (Mode B & Hybrid Mode C)

While **Mode A (Pure Procedural 3D)** generates geometry mathematically from code, **Mode B (Image-Sequence Frame Scrubber)** is designed for workflows where the user provides a series of pre-rendered image frames (e.g. `frame_0001.webp` to `frame_0150.webp` or an array of image URLs) and wants an **Apple-style, butter-smooth scroll scrubber** that renders live onto an HTML5 2D `<canvas>`.

**Hybrid Mode C** combines both: an image-sequence background layer seamlessly composited with live interactive 3D WebGL objects in the foreground.

---

## 1. Core Architecture of High-Performance Frame Scrubbing

```
┌─────────────────────────────────────────────────────────────┐
│ High-Performance Canvas Scrubber                            │
│                                                             │
│  1. Memory Buffer Preloader (loads all frames into RAM)     │
│  2. Float-based Progress Damping (lerp for sub-frame scrub) │
│  3. Responsive Aspect-Ratio Fit (`object-fit: cover`)       │
│  4. DPI / Retina Scaling (draws crisp on 4K displays)       │
│  5. Zero-flicker double-buffered frame drawing             │
└─────────────────────────────────────────────────────────────┘
```

---

## 2. Complete Standalone Image-Sequence Scrubber Scaffold

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Apple-Style Image Sequence Scrubber</title>
  <style>
    *, *::before, *::after { margin: 0; padding: 0; box-sizing: border-box; }
    html { scroll-behavior: auto; }
    body {
      font-family: 'Inter', system-ui, -apple-system, sans-serif;
      color: #f8fafc;
      background: #000000;
      overflow-x: hidden;
      -webkit-font-smoothing: antialiased;
    }

    /* Fixed Canvas sitting behind page */
    #sequence-canvas {
      position: fixed;
      inset: 0;
      width: 100%;
      height: 100%;
      z-index: 0;
      display: block;
      pointer-events: none;
    }

    /* Preloader Overlay */
    .preloader {
      position: fixed; inset: 0; z-index: 1000;
      background: #000000;
      display: flex; flex-direction: column; align-items: center; justify-content: center;
      transition: opacity 0.6s ease-out, visibility 0.6s ease-out;
    }
    .preloader.hidden { opacity: 0; visibility: hidden; pointer-events: none; }
    .progress-bar-container {
      width: 240px; height: 4px; background: #1e293b; border-radius: 4px; overflow: hidden; margin-top: 1rem;
    }
    .progress-bar {
      width: 0%; height: 100%; background: #38bdf8; transition: width 0.1s linear;
    }

    /* Scroll Runway & Text Overlays */
    .scroll-container { position: relative; z-index: 10; pointer-events: none; }
    .scroll-section {
      min-height: 100vh; display: flex; align-items: center; padding: 6rem 8%;
    }
    .scroll-section:nth-child(odd) { justify-content: flex-start; }
    .scroll-section:nth-child(even) { justify-content: flex-end; }
    .scroll-section:first-child {
      justify-content: center; text-align: center; align-items: flex-end; padding-bottom: 15vh;
    }

    .glass-card {
      max-width: 500px; padding: 2.5rem;
      background: rgba(15, 23, 42, 0.7);
      backdrop-filter: blur(20px);
      -webkit-backdrop-filter: blur(20px);
      border: 1px solid rgba(255, 255, 255, 0.12);
      border-radius: 24px;
      box-shadow: 0 20px 50px rgba(0, 0, 0, 0.7);
      pointer-events: auto;
      opacity: 0; transform: translateY(30px);
      transition: opacity 0.8s cubic-bezier(0.16, 1, 0.3, 1), transform 0.8s cubic-bezier(0.16, 1, 0.3, 1);
    }
    .glass-card.is-visible { opacity: 1; transform: translateY(0); }
    .glass-card h2 { font-size: clamp(2rem, 4vw, 3rem); font-weight: 700; margin-bottom: 0.8rem; color: #ffffff; }
    .glass-card p { font-size: 1.1rem; line-height: 1.7; color: #94a3b8; }
  </style>
</head>
<body>

  <div class="preloader" id="preloader">
    <p id="preloader-text" style="font-size: 0.85rem; letter-spacing: 0.1em; color: #94a3b8;">LOADING FRAMES 0%</p>
    <div class="progress-bar-container">
      <div class="progress-bar" id="progress-bar"></div>
    </div>
  </div>

  <canvas id="sequence-canvas"></canvas>

  <main class="scroll-container">
    <section class="scroll-section">
      <div class="glass-card" style="background: transparent; border: none; box-shadow: none;">
        <h2>Cinema Sequence</h2>
        <p>Scroll down to scrub through high-definition image frames.</p>
      </div>
    </section>

    <section class="scroll-section">
      <div class="glass-card">
        <h2>Frame-Accurate</h2>
        <p>Preloaded directly into RAM for zero-latency, stutter-free playback.</p>
      </div>
    </section>

    <section class="scroll-section">
      <div class="glass-card">
        <h2>Sub-Pixel Lerp</h2>
        <p>Physics-damped easing smooths out fast scrolls and trackpad flings.</p>
      </div>
    </section>
  </main>

  <script>
    const canvas = document.getElementById('sequence-canvas');
    const ctx = canvas.getContext('2d', { alpha: false });
    const prefersReducedMotion = window.matchMedia('(prefers-reduced-motion: reduce)').matches;

    // CONFIGURATION
    const frameCount = 120; // Total number of images
    const images = [];
    let imagesLoaded = 0;

    // URL Generator: Replace with your image frame path pattern
    // e.g. `https://your-domain.com/frames/frame_${String(index).padStart(4, '0')}.jpg`
    function getFrameUrl(index) {
      // In this example, we generate a procedural placeholder canvas frame if external URLs are not provided
      return null;
    }

    // 1. PRELOADER ENGINE
    function preloadFrames() {
      const progressBar = document.getElementById('progress-bar');
      const preloaderText = document.getElementById('preloader-text');

      for (let i = 0; i < frameCount; i++) {
        const img = new Image();
        const url = getFrameUrl(i);

        if (!url) {
          // Fallback: Generate demo frames dynamically if user did not provide image paths
          const demoCanvas = document.createElement('canvas');
          demoCanvas.width = 1920; demoCanvas.height = 1080;
          const dCtx = demoCanvas.getContext('2d');
          
          // Draw dynamic demonstration graphics
          dCtx.fillStyle = '#05070f';
          dCtx.fillRect(0, 0, 1920, 1080);
          dCtx.strokeStyle = '#38bdf8';
          dCtx.lineWidth = 6;
          
          const angle = (i / frameCount) * Math.PI * 2;
          dCtx.beginPath();
          dCtx.arc(960 + Math.cos(angle) * 300, 540 + Math.sin(angle) * 150, 180, 0, Math.PI * 2);
          dCtx.stroke();

          img.src = demoCanvas.toDataURL('image/jpeg', 0.8);
        } else {
          img.src = url;
        }

        img.onload = () => {
          imagesLoaded++;
          const percent = Math.round((imagesLoaded / frameCount) * 100);
          progressBar.style.width = percent + '%';
          preloaderText.innerText = `LOADING FRAMES ${percent}%`;

          if (imagesLoaded === frameCount) {
            document.getElementById('preloader').classList.add('hidden');
            resizeCanvas();
            renderFrame(0);
          }
        };

        images.push(img);
      }
    }

    // 2. ASPECT RATIO COVER DRAWING
    function drawImageCover(img) {
      if (!img || !img.complete) return;
      const cw = canvas.width;
      const ch = canvas.height;
      const iw = img.width;
      const ih = img.height;

      // object-fit: cover calculation
      const hRatio = cw / iw;
      const vRatio = ch / ih;
      const ratio = Math.max(hRatio, vRatio);

      const centerShiftX = (cw - iw * ratio) / 2;
      const centerShiftY = (ch - ih * ratio) / 2;

      ctx.drawImage(img, 0, 0, iw, ih, centerShiftX, centerShiftY, iw * ratio, ih * ratio);
    }

    // 3. RESPONSIVE RESIZE
    function resizeCanvas() {
      const dpr = Math.min(window.devicePixelRatio || 1, 2);
      canvas.width = window.innerWidth * dpr;
      canvas.height = window.innerHeight * dpr;
      ctx.scale(dpr, dpr);
      renderFrame(currentFrameFloat);
    }
    window.addEventListener('resize', resizeCanvas);

    // 4. SCROLL PROGRESS & DAMPING
    let targetProgress = 0;
    let currentProgress = 0;
    let currentFrameFloat = 0;
    const damp = prefersReducedMotion ? 0.3 : 0.08;

    function getScrollProgress() {
      const max = document.documentElement.scrollHeight - window.innerHeight;
      return max > 0 ? Math.max(0, Math.min(1, window.scrollY / max)) : 0;
    }

    window.addEventListener('scroll', () => { targetProgress = getScrollProgress(); }, { passive: true });

    function renderFrame(frameFloat) {
      const frameIndex = Math.min(frameCount - 1, Math.max(0, Math.floor(frameFloat)));
      if (images[frameIndex]) {
        drawImageCover(images[frameIndex]);
      }
    }

    // 5. ANIMATION LOOP
    function animate() {
      requestAnimationFrame(animate);
      currentProgress += (targetProgress - currentProgress) * damp;
      currentFrameFloat = currentProgress * (frameCount - 1);
      renderFrame(currentFrameFloat);
    }

    // 6. TEXT OVERLAY OBSERVER
    const cards = document.querySelectorAll('.glass-card');
    const observer = new IntersectionObserver((entries) => {
      entries.forEach((e) => { if (e.isIntersecting) e.target.classList.add('is-visible'); });
    }, { threshold: 0.2 });
    cards.forEach((c) => observer.observe(c));

    preloadFrames();
    animate();
  </script>
</body>
</html>
```

---

## 3. Hybrid Mode C: Image Sequence Background + 3D WebGL Foreground

To render interactive 3D objects (e.g. floating crystals, glowing particles, or product annotations) on top of an image sequence:

1. **Canvas Layer 1 (Back)**: The 2D `<canvas id="sequence-canvas">` scrubbing the image frames.
2. **Canvas Layer 2 (Front)**: The Three.js WebGL `<canvas id="webgl-canvas">` with `alpha: true` (`transparent` background) rendering 3D geometries that match the camera trajectory of the background images.
3. **Synchronization**: Both the 2D frame index and the 3D transforms read from the exact same `currentProgress` variable.
