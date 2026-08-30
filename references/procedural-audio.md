# Zero-Asset Procedural Web Audio Engine

This reference covers creating **cinematic, generative soundscapes** using the native **Web Audio API** with zero downloaded MP3, WAV, or audio asset files. Sound is synthesized mathematically in real-time, responding dynamically to scroll velocity, cursor coordinates, and user interaction.

---

## 1. Core Principles of Web Audio in Scrollytelling

1. **Zero External Assets**: All sound is generated mathematically from oscillators, noise buffers, and biquad filters. Zero network requests, instant loading.
2. **User Gesture Unlock**: Browsers block audio until the user interacts with the page. The audio engine remains suspended until the user explicitly toggles sound or interacts with an audio enable button.
3. **Scroll-Driven Modulation**: As the user scrolls rapidly, audio parameters (filter cutoff frequency, oscillator pitch, noise volume) swell dynamically to reflect cinematic velocity.
4. **Smooth Gain Transitions**: Never abruptly start or stop audio nodes. Always use `setTargetAtTime()` or `exponentialRampToValueAtTime()` to prevent speaker popping.

---

## 2. Production Procedural Audio Engine

```js
class ProceduralAudioEngine {
  constructor() {
    this.ctx = null;
    this.isMuted = true;
    this.droneGain = null;
    this.windGain = null;
    this.windFilter = null;
    this.droneOsc1 = null;
    this.droneOsc2 = null;
  }

  // 1. Initialize audio nodes upon first user interaction
  init() {
    if (this.ctx) return;
    const AudioContext = window.AudioContext || window.webkitAudioContext;
    this.ctx = new AudioContext();

    // Master Gain
    this.masterGain = this.ctx.createGain();
    this.masterGain.gain.setValueAtTime(0.0, this.ctx.currentTime);
    this.masterGain.connect(this.ctx.destination);

    // --- A. DEEP CINEMATIC AMBIENT DRONE ---
    this.droneGain = this.ctx.createGain();
    this.droneGain.gain.setValueAtTime(0.12, this.ctx.currentTime);

    // Low-pass filter for warm nocturnal hum
    const droneFilter = this.ctx.createBiquadFilter();
    droneFilter.type = 'lowpass';
    droneFilter.frequency.setValueAtTime(140, this.ctx.currentTime);

    // Primary fundamental 55Hz (A1)
    this.droneOsc1 = this.ctx.createOscillator();
    this.droneOsc1.type = 'sawtooth';
    this.droneOsc1.frequency.setValueAtTime(55, this.ctx.currentTime);

    // Detuned sub-oscillator 55.4Hz for subtle acoustic phasing
    this.droneOsc2 = this.ctx.createOscillator();
    this.droneOsc2.type = 'sine';
    this.droneOsc2.frequency.setValueAtTime(55.4, this.ctx.currentTime);

    this.droneOsc1.connect(droneFilter);
    this.droneOsc2.connect(droneFilter);
    droneFilter.connect(this.droneGain);
    this.droneGain.connect(this.masterGain);

    this.droneOsc1.start();
    this.droneOsc2.start();

    // --- B. SCROLL VELOCITY WIND SYNTHESIZER ---
    // Procedural White Noise Buffer
    const bufferSize = this.ctx.sampleRate * 2;
    const noiseBuffer = this.ctx.createBuffer(1, bufferSize, this.ctx.sampleRate);
    const output = noiseBuffer.getChannelData(0);
    for (let i = 0; i < bufferSize; i++) {
      output[i] = Math.random() * 2 - 1;
    }

    const whiteNoise = this.ctx.createBufferSource();
    whiteNoise.buffer = noiseBuffer;
    whiteNoise.loop = true;

    // Resonant bandpass filter for rushing wind sensation
    this.windFilter = this.ctx.createBiquadFilter();
    this.windFilter.type = 'bandpass';
    this.windFilter.frequency.setValueAtTime(300, this.ctx.currentTime);
    this.windFilter.Q.setValueAtTime(3.0, this.ctx.currentTime);

    this.windGain = this.ctx.createGain();
    this.windGain.gain.setValueAtTime(0.0, this.ctx.currentTime);

    whiteNoise.connect(this.windFilter);
    this.windFilter.connect(this.windGain);
    this.windGain.connect(this.masterGain);

    whiteNoise.start();
  }

  // 2. Modulate sound in real-time based on scroll speed
  updateScrollVelocity(velocity) {
    if (!this.ctx || this.isMuted) return;
    const now = this.ctx.currentTime;
    const absVel = Math.min(1.0, Math.abs(velocity) * 8.0);

    // Modulate wind volume and cutoff frequency
    const targetGain = absVel * 0.18;
    const targetFreq = 300 + absVel * 900;

    this.windGain.gain.setTargetAtTime(targetGain, now, 0.08);
    this.windFilter.frequency.setTargetAtTime(targetFreq, now, 0.08);
  }

  // 3. Play kinetic UI feedback click
  playClickSound() {
    if (!this.ctx || this.isMuted) return;
    const osc = this.ctx.createOscillator();
    const gain = this.ctx.createGain();

    osc.type = 'sine';
    osc.frequency.setValueAtTime(880, this.ctx.currentTime); // High harmonic ping
    osc.frequency.exponentialRampToValueAtTime(220, this.ctx.currentTime + 0.06);

    gain.gain.setValueAtTime(0.1, this.ctx.currentTime);
    gain.gain.exponentialRampToValueAtTime(0.001, this.ctx.currentTime + 0.06);

    osc.connect(gain);
    gain.connect(this.masterGain);

    osc.start();
    osc.stop(this.ctx.currentTime + 0.07);
  }

  // 4. Toggle mute state cleanly
  toggleMute() {
    this.init();
    if (this.ctx.state === 'suspended') {
      this.ctx.resume();
    }

    const now = this.ctx.currentTime;
    if (this.isMuted) {
      this.masterGain.gain.setTargetAtTime(1.0, now, 0.15);
      this.isMuted = false;
    } else {
      this.masterGain.gain.setTargetAtTime(0.0, now, 0.15);
      this.isMuted = true;
    }
    return !this.isMuted;
  }
}
```

---

## 3. UI Toggle Button Integration

```html
<!-- Floating Audio Toggle Button -->
<button id="sound-toggle" class="sound-button" aria-label="Toggle Sound">
  <span id="sound-icon">🔇</span>
  <span id="sound-label">AUDIO OFF</span>
</button>

<style>
  .sound-button {
    position: fixed;
    top: 2rem;
    right: 2rem;
    z-index: 50;
    background: rgba(15, 23, 42, 0.85);
    border: 1px solid rgba(56, 189, 248, 0.25);
    color: #e2e8f0;
    font-family: ui-monospace, monospace;
    font-size: 0.72rem;
    letter-spacing: 0.1em;
    padding: 0.5rem 1rem;
    border-radius: 100px;
    cursor: pointer;
    backdrop-filter: blur(12px);
    display: flex;
    align-items: center;
    gap: 0.5rem;
    transition: all 0.25s ease;
  }
  .sound-button:hover {
    border-color: #38bdf8;
    color: #fff;
    box-shadow: 0 0 15px rgba(56, 189, 248, 0.25);
  }
  .sound-button.active {
    background: rgba(56, 189, 248, 0.2);
    border-color: #38bdf8;
    color: #38bdf8;
  }
</style>

<script>
  const soundEngine = new ProceduralAudioEngine();
  const soundBtn = document.getElementById('sound-toggle');
  const soundLabel = document.getElementById('sound-label');
  const soundIcon = document.getElementById('sound-icon');

  soundBtn.addEventListener('click', () => {
    const isPlaying = soundEngine.toggleMute();
    soundBtn.classList.toggle('active', isPlaying);
    soundIcon.textContent = isPlaying ? '🔊' : '🔇';
    soundLabel.textContent = isPlaying ? 'AUDIO ON' : 'AUDIO OFF';
    soundEngine.playClickSound();
  });

  // Calculate scroll velocity inside requestAnimationFrame loop
  let lastScrollY = window.scrollY;
  let scrollVelocity = 0;

  function updateAudio() {
    const currentY = window.scrollY;
    scrollVelocity = (currentY - lastScrollY) / 100;
    lastScrollY = currentY;

    soundEngine.updateScrollVelocity(scrollVelocity);
    requestAnimationFrame(updateAudio);
  }
  updateAudio();
</script>
```
