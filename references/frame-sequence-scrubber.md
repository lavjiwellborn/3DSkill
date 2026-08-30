# High-Performance Frame-Sequence & Video Scrollytelling (Mode B & C)

This reference covers the architecture, memory caching, sub-frame interpolation, and depth layering required to build ultra-smooth scroll-driven image sequence and video scrubbing experiences.

---

## 1. Engine Routing Rules (Crucial Architectural Constraint)

To maintain clarity and prevent anti-patterns, every project strictly routes to one of three modes:

> **Mode A (Procedural 3D — Default)**: Used whenever there is no pre-existing visual asset, or the user wants an object/environment built and stylized from scratch. Scene is rendered live from code via WebGL/Three.js with zero external video or image frame downloads.
>
> **Mode B (Frame-Sequence Scrubber)**: Used **only** when the user already has, or explicitly references having, a real pre-rendered image sequence or video file they want scrubbed — actual photography, licensed footage, or a render produced in offline 3D suites (Blender/Cinema4D). **Mode B must never be reached by generating an AI video and slicing it into frames as a strategy for fulfilling a from-scratch request.** If a request has no existing asset, it is a Mode A request.
>
> **Mode C (Hybrid Pipeline)**: Used when the user wants a live 3D object, shader effect, or UI element composited over real pre-rendered background footage (e.g. procedural WebGL particles or a real-time 3D product floating over photographed landscape video frames).

---

## 2. The Problem with Naive Scrubbing

Traditional frame-scrubbing implementations fail because:
1. **Discrete Frame Stepping**: Scrubbing across 30fps–60fps sequences at slow scroll speeds produces jarring step jumps.
2. **DOM Paint & Seek Bottlenecks**: Modifying `img.src` or seeking standard `<video>.currentTime` inside scroll listeners causes decoder stalls, asynchronous seek lag, and dropped frames.
3. **Memory Overflow from Full-Preloading**: Uncompressed decoded bitmaps cost `width × height × 4 bytes` regardless of source compression (WebP/AVIF). Preloading 120+ frames at 1080p consumes ~1GB of GPU RAM, crashing mobile browsers.

Mode B solves these issues using **Canvas-accelerated 2D rendering**, a **budget-derived Windowed LRU Cache**, **sub-frame alpha crossfading**, and **typographic depth occlusion**.

---

## 3. Memory Budgeting & Windowed LRU Cache

### Uncompressed Bitmap Memory Math
```
Bytes per decoded frame = width × height × 4 bytes (RGBA)
1080p (1920×1080) ≈ 7.9 MB per frame
720p  (1280×720)  ≈ 3.5 MB per frame
```

### Derived Window Radius Formula
Do not preload entire sequences. Keep a sliding window of frames resident around the active scroll position and dispose out-of-window frames using `ImageBitmap.close()`:

```
maxResidentFrames = Math.floor(memoryBudgetBytes / (frameWidth * frameHeight * 4))
windowRadius      = Math.floor((maxResidentFrames - 1) / 2)
```

*Example at 720p with a 120MB budget:*
`maxResidentFrames = floor(120,000,000 / 3,686,400) = 32 frames` → `windowRadius = 15` (31 resident frames ≈ 114MB, safely below ceiling).

### Production Windowed FrameCache Class
```js
class FrameCache {
  constructor(frameUrls, frameWidth = 1280, frameHeight = 720, memoryBudgetBytes = 100 * 1024 * 1024) {
    this.frameUrls = frameUrls;
    this.frameWidth = frameWidth;
    this.frameHeight = frameHeight;

    const bytesPerFrame = frameWidth * frameHeight * 4;
    const maxResident = Math.max(5, Math.floor(memoryBudgetBytes / bytesPerFrame));
    this.windowRadius = Math.floor((maxResident - 1) / 2);

    this.cache = new Map();   // frameIndex -> ImageBitmap | HTMLImageElement
    this.pending = new Map(); // frameIndex -> Promise
    this.supportsImageBitmap = typeof createImageBitmap === 'function';
  }

  async ensureWindow(centerIndex) {
    const lo = Math.max(0, centerIndex - this.windowRadius);
    const hi = Math.min(this.frameUrls.length - 1, centerIndex + this.windowRadius);

    // 1. Fetch & decode frames entering the window
    for (let i = lo; i <= hi; i++) {
      if (!this.cache.has(i) && !this.pending.has(i)) {
        this.pending.set(i, this._decode(i));
      }
    }

    // 2. Release frames falling outside the active window
    for (const i of [...this.cache.keys()]) {
      if (i < lo || i > hi) {
        const item = this.cache.get(i);
        if (item && typeof item.close === 'function') {
          item.close(); // Frees underlying GPU texture memory immediately
        }
        this.cache.delete(i);
      }
    }
  }

  async _decode(i) {
    try {
      if (this.supportsImageBitmap) {
        const resp = await fetch(this.frameUrls[i], { mode: 'cors' });
        const blob = await resp.blob();
        const bitmap = await createImageBitmap(blob);
        this.cache.set(i, bitmap);
        this.pending.delete(i);
        return bitmap;
      } else {
        // Universal fallback for older environments
        return new Promise((resolve, reject) => {
          const img = new Image();
          img.crossOrigin = 'anonymous';
          img.onload = () => {
            this.cache.set(i, img);
            this.pending.delete(i);
            resolve(img);
          };
          img.onerror = reject;
          img.src = this.frameUrls[i];
        });
      }
    } catch (err) {
      this.pending.delete(i);
      console.warn(`Frame ${i} decode error:`, err);
      return null;
    }
  }

  get(i) {
    return this.cache.get(i) || null;
  }
}
```

---

## 4. Canvas Rendering Engine

### A. Device Pixel Ratio (DPR) Scaling
```js
function setupCanvasDPR(canvas, ctx) {
  const dpr = Math.min(window.devicePixelRatio || 1, 2);
  const w = window.innerWidth;
  const h = window.innerHeight;
  canvas.width = w * dpr;
  canvas.height = h * dpr;
  canvas.style.width = `${w}px`;
  canvas.style.height = `${h}px`;
  ctx.scale(dpr, dpr);
}
```

### B. Manual Cover-Fit Crop (Canvas `object-fit: cover`)
```js
function drawCover(ctx, source, canvasW, canvasH) {
  const srcW = source.width || source.videoWidth;
  const srcH = source.height || source.videoHeight;
  const srcAspect = srcW / srcH;
  const cvAspect = canvasW / canvasH;

  let sx, sy, sw, sh;
  if (srcAspect > cvAspect) {
    // Source is wider than viewport -> crop sides
    sh = srcH;
    sw = sh * cvAspect;
    sx = (srcW - sw) / 2;
    sy = 0;
  } else {
    // Source is taller than viewport -> crop top/bottom
    sw = srcW;
    sh = sw / cvAspect;
    sx = 0;
    sy = (srcH - sh) / 2;
  }

  ctx.drawImage(source, sx, sy, sw, sh, 0, 0, canvasW, canvasH);
}
```

### C. Sub-Frame Fractional Alpha Crossfading
Interpolating between adjacent frames eliminates discrete stepping:
```js
function drawSubFrame(ctx, cache, floatIndex, canvasW, canvasH) {
  const maxIdx = cache.frameUrls.length - 1;
  const clamped = Math.max(0, Math.min(maxIdx, floatIndex));
  const lo = Math.floor(clamped);
  const hi = Math.min(lo + 1, maxIdx);
  const frac = clamped - lo;

  const bmpLo = cache.get(lo);
  if (!bmpLo) return; // Hold last painted frame if current frame is loading

  ctx.globalAlpha = 1.0;
  drawCover(ctx, bmpLo, canvasW, canvasH);

  const bmpHi = cache.get(hi);
  if (bmpHi && frac > 0.01) {
    ctx.globalAlpha = frac;
    drawCover(ctx, bmpHi, canvasW, canvasH);
    ctx.globalAlpha = 1.0;
  }
}
```

---

## 5. Direct Video Element Scrubbing (Hardware Fallback)

For continuous 4K footage where image extraction is impractical, use hardware-throttled `<video>` seeking:

```js
class VideoScrubber {
  constructor(videoElement) {
    this.video = videoElement;
    this.isSeeking = false;
    this.targetTime = 0;
    this.hasRVFC = 'requestVideoFrameCallback' in HTMLVideoElement.prototype;

    this.video.addEventListener('seeked', () => {
      this.isSeeking = false;
      if (Math.abs(this.video.currentTime - this.targetTime) > 0.03) {
        this._seek();
      }
    });
  }

  seekTo(normalizedProgress) {
    if (!this.video.duration) return;
    this.targetTime = normalizedProgress * this.video.duration;
    if (!this.isSeeking) {
      this._seek();
    }
  }

  _seek() {
    this.isSeeking = true;
    this.video.currentTime = this.targetTime;
  }
}
```

---

## 6. Typographic Depth Occlusion

Layering text and interactive elements creates immersive visual depth:

```
┌────────────────────────────────────────────────────────┐
│ DOM Layer Architecture                                 │
├────────────────────────────────────────────────────────┤
│ Level 0: Deep Background (CSS Gradient / Fog)          │
│ Level 1: Back Typography (z-index: 1, oversized H1)    │
│ Level 2: Interactive Frame Canvas (z-index: 2)         │
│ Level 3: Foreground Glass Cards (z-index: 10)         │
└────────────────────────────────────────────────────────┘
```

### CSS Depth Hierarchy
```css
/* Deep Headline behind canvas subject */
.headline-backdrop {
  position: fixed;
  inset: 0;
  z-index: 1;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: clamp(4rem, 15vw, 14rem);
  font-weight: 900;
  letter-spacing: -0.05em;
  color: rgba(255, 255, 255, 0.08);
  pointer-events: none;
}

/* Canvas viewport */
#frame-canvas {
  position: fixed;
  inset: 0;
  z-index: 2;
  pointer-events: none;
}

/* Interactive Glass Cards */
.scroll-container {
  position: relative;
  z-index: 10;
  pointer-events: none;
}
.glass-card {
  pointer-events: auto;
}
```

---

## 7. Asset Preparation Guide (FFmpeg)

To convert high-bitrate video to an optimized 720p WebP frame sequence:

```bash
# Extract 720p WebP sequence at 30fps (quality 80)
ffmpeg -i source_video.mp4 -vf "fps=30,scale=1280:-1" -vcodec libwebp -lossless 0 -q:v 80 frame_%04d.webp

# For transparent subject sequences (PNG to WebP alpha)
ffmpeg -i source_alpha.mov -vf "fps=30,scale=1280:-1" -vcodec libwebp -lossless 0 -q:v 85 frame_%04d.webp
```

---

## 8. Mode B Self-Check Checklist

- [ ] **Mode Routing**: Verified that real pre-existing footage/renders exist (never generated video as a workaround).
- [ ] **Memory Bounded**: Cache derived via `memoryBudgetBytes` formula; out-of-window bitmaps explicitly closed via `bitmap.close()`.
- [ ] **Sub-Frame Crossfade**: Continuous fractional interpolation eliminates step jumps during slow crawls.
- [ ] **Aspect Ratio Cover**: `drawCover()` math active without distortion across mobile and ultrawide screens.
- [ ] **DPR Scaling**: Canvas dimensions and 2D context scaled by `Math.min(window.devicePixelRatio, 2)`.
- [ ] **CORS Compliance**: Remote CDN headers configured with `Access-Control-Allow-Origin: *`.
- [ ] **Fallback Ready**: `createImageBitmap` fallback to standard `HTMLImageElement` tested.
