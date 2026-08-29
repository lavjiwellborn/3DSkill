# Procedural Geometry Recipes

The goal is a recognizable object built entirely from primitive Three.js geometries and math — no modeling software, no downloaded asset. Recognizability comes from **silhouette**, not detail: get the outline right with a handful of shapes and people read it correctly at a glance.

---

## 1. The Core Geometric Principles

### The Lathe Trick for Curved Silhouettes
Stacking boxes and cones gets you a generic "layered pyramid," not a temple/pagoda/vase/lantern. The thing that makes those shapes read as what they are is a *curved* profile — and `THREE.LatheGeometry` builds any surface of revolution from a 2D profile curve, which is exactly how curved eaves and organic contours happen.

```js
// Define a profile as (x = radius from center axis, y = height), bottom to top.
// This one flares out then curls upward — the classic upturned pagoda-roof silhouette.
const roofProfile = [
  new THREE.Vector2(0.0, 0.0),
  new THREE.Vector2(2.4, 0.05),
  new THREE.Vector2(2.6, 0.35),   // flare out
  new THREE.Vector2(2.3, 0.55),   // curl back in and up
  new THREE.Vector2(1.6, 0.7),
  new THREE.Vector2(0.3, 0.78),
  new THREE.Vector2(0.0, 0.8),
];
const roofGeo = new THREE.LatheGeometry(roofProfile, 24);
```

### Match Roof Shape to Footprint Shape (Crucial Rule)
A revolved (`LatheGeometry`) shape is circular in plan, full stop. If the body underneath is a square/rectangular box, a Lathe roof on top reads as a circular disc awkwardly overlapping a square corner.
- **Round body (dome, silo, drum tower, lantern, vase) → Lathe roof is correct.**
- **Square/rectangular body (pagoda, house, temple) → Square cone roof is required.** Use `THREE.ConeGeometry(radius, height, 4)` rotated 45° (`roof.rotation.y = Math.PI / 4`) so flat faces line up with the box faces.
- **Upturned eave corners:** Add small separate `TorusGeometry` arc flourishes at each corner.

---

## 2. Worked Blueprints Library (5 Iconic Archetypes)

### Archetype A: Japanese Shrine Pagoda (Historical, Cultural, Architectural)
Tiers stack with proportional scaling (each tier 20% smaller). Employs stark plaster vs red lacquer contrast and wide eave overhangs.

```js
function buildPagoda(tierCount = 3) {
  const pagoda = new THREE.Group();
  let y = 0;
  const tierHeight = 1.1;

  const wallMat  = new THREE.MeshStandardMaterial({ color: 0xede6d6, roughness: 0.85 });
  const frameMat = new THREE.MeshStandardMaterial({ color: 0x9c2b2b, roughness: 0.55 });
  const roofMat  = new THREE.MeshStandardMaterial({ color: 0x14171c, roughness: 0.45, metalness: 0.1 });
  const stoneMat = new THREE.MeshStandardMaterial({ color: 0x6b6b6b, roughness: 0.9 });
  const goldMat  = new THREE.MeshStandardMaterial({ color: 0xd4af6a, roughness: 0.3, metalness: 0.75 });

  const pillarGeo = new THREE.CylinderGeometry(0.05, 0.05, 1.1, 8);
  const flourishGeo = new THREE.TorusGeometry(0.45, 0.05, 6, 10, Math.PI * 0.55);

  // Stone base plinth
  const plinth = new THREE.Mesh(new THREE.CylinderGeometry(2.6, 2.8, 0.3, 4), stoneMat);
  plinth.rotation.y = Math.PI / 4;
  plinth.position.y = -0.15;
  plinth.receiveShadow = true;
  pagoda.add(plinth);

  for (let i = 0; i < tierCount; i++) {
    const scale = 1 - i * 0.2;
    const wallSize = 2.0 * scale;
    const roofOverhang = 1.75;

    // Plaster core
    const wall = new THREE.Mesh(new THREE.BoxGeometry(wallSize, tierHeight, wallSize), wallMat);
    wall.position.y = y + tierHeight / 2;
    wall.castShadow = true;
    pagoda.add(wall);

    // Beams top & bottom
    [y, y + tierHeight].forEach((beamY) => {
      const beam = new THREE.Mesh(new THREE.BoxGeometry(wallSize * 1.08, 0.12, wallSize * 1.08), frameMat);
      beam.position.y = beamY;
      beam.castShadow = true;
      pagoda.add(beam);
    });

    // Perimeter pillars
    const pillarRadius = (wallSize / 2) * 1.02;
    for (let p = 0; p < 8; p++) {
      const angle = (p / 8) * Math.PI * 2 + Math.PI / 4;
      const pillar = new THREE.Mesh(pillarGeo, frameMat);
      pillar.scale.y = tierHeight / 1.1;
      pillar.position.set(Math.cos(angle) * pillarRadius, y + tierHeight / 2, Math.sin(angle) * pillarRadius);
      pillar.castShadow = true;
      pagoda.add(pillar);
    }

    // Square roof with deep overhang
    const roofRadius = (wallSize / 2) * Math.SQRT2 * roofOverhang;
    const roofHeight = wallSize * 0.35;
    const roof = new THREE.Mesh(new THREE.ConeGeometry(roofRadius, roofHeight, 4), roofMat);
    roof.rotation.y = Math.PI / 4;
    roof.position.y = y + tierHeight + roofHeight / 2;
    roof.castShadow = true;
    pagoda.add(roof);

    // Eave flourishes
    for (let c = 0; c < 4; c++) {
      const cornerAngle = (c / 4) * Math.PI * 2 + Math.PI / 4;
      const flourish = new THREE.Mesh(flourishGeo, frameMat);
      flourish.position.set(Math.cos(cornerAngle) * roofRadius * 0.75, y + tierHeight + 0.05, Math.sin(cornerAngle) * roofRadius * 0.75);
      flourish.rotation.y = -cornerAngle;
      flourish.rotation.z = Math.PI / 2.4;
      pagoda.add(flourish);
    }

    y += tierHeight + roofHeight + 0.15;
  }

  // Golden Spire (Sorin)
  const spire = new THREE.Mesh(new THREE.CylinderGeometry(0.04, 0.06, 1.8, 8), goldMat);
  spire.position.y = y + 0.9;
  pagoda.add(spire);
  for (let r = 0; r < 5; r++) {
    const ring = new THREE.Mesh(new THREE.TorusGeometry(0.28 - r * 0.035, 0.025, 8, 16), goldMat);
    ring.rotation.x = Math.PI / 2;
    ring.position.y = y + 0.25 + r * 0.28;
    pagoda.add(ring);
  }
  const jewel = new THREE.Mesh(new THREE.SphereGeometry(0.09, 12, 12), goldMat);
  jewel.position.y = y + 1.9;
  pagoda.add(jewel);

  return pagoda;
}
```

---

### Archetype B: Sci-Fi Exploration Vessel / Drone (Tech, Future, Aero)
Combines an aerodynamic hull, swept-back wing panels, cockpit canopy, and glowing thruster exhausts.

```js
function buildSciFiVessel() {
  const vessel = new THREE.Group();

  const hullMat = new THREE.MeshStandardMaterial({ color: 0x1e293b, roughness: 0.3, metalness: 0.85 });
  const trimMat = new THREE.MeshStandardMaterial({ color: 0xe2e8f0, roughness: 0.2, metalness: 0.9 });
  const glassMat = new THREE.MeshStandardMaterial({ color: 0x0ea5e9, roughness: 0.1, metalness: 0.3, transparent: true, opacity: 0.8 });
  const thrusterGlowMat = new THREE.MeshStandardMaterial({ color: 0x00f0ff, emissive: 0x00f0ff, emissiveIntensity: 3.0 });

  // Main fuselage (tapered cylinder rotated along Z)
  const fuselageGeo = new THREE.CylinderGeometry(0.5, 0.9, 4.2, 16);
  fuselageGeo.rotateX(Math.PI / 2);
  const fuselage = new THREE.Mesh(fuselageGeo, hullMat);
  fuselage.castShadow = true;
  vessel.add(fuselage);

  // Cockpit canopy (elongated sphere sliced)
  const canopyGeo = new THREE.SphereGeometry(0.65, 16, 16, 0, Math.PI * 2, 0, Math.PI * 0.5);
  const canopy = new THREE.Mesh(canopyGeo, glassMat);
  canopy.scale.set(0.9, 0.7, 1.8);
  canopy.position.set(0, 0.35, 0.4);
  vessel.add(canopy);

  // Swept Wings (left and right)
  [-1, 1].forEach((side) => {
    const wingShape = new THREE.Shape();
    wingShape.moveTo(0, 0);
    wingShape.lineTo(side * 2.8, -1.2);
    wingShape.lineTo(side * 2.6, -1.8);
    wingShape.lineTo(0, -0.6);
    wingShape.closePath();

    const extrudeSettings = { depth: 0.08, bevelEnabled: true, bevelSegments: 2, steps: 1, bevelSize: 0.03, bevelThickness: 0.03 };
    const wingGeo = new THREE.ExtrudeGeometry(wingShape, extrudeSettings);
    wingGeo.rotateX(Math.PI / 2);
    const wing = new THREE.Mesh(wingGeo, trimMat);
    wing.position.set(0, 0.05, 0.2);
    wing.castShadow = true;
    vessel.add(wing);

    // Wingtip Thruster pods
    const podGeo = new THREE.CylinderGeometry(0.15, 0.18, 1.2, 12);
    podGeo.rotateX(Math.PI / 2);
    const pod = new THREE.Mesh(podGeo, hullMat);
    pod.position.set(side * 2.7, -0.05, -1.5);
    vessel.add(pod);

    // Glow cone exhaust
    const glow = new THREE.Mesh(new THREE.ConeGeometry(0.14, 0.5, 12), thrusterGlowMat);
    glow.rotateX(-Math.PI / 2);
    glow.position.set(side * 2.7, -0.05, -2.2);
    vessel.add(glow);
  });

  // Main Rear Engine Core
  const engineRing = new THREE.Mesh(new THREE.TorusGeometry(0.6, 0.12, 12, 24), trimMat);
  engineRing.position.set(0, 0, -2.1);
  vessel.add(engineRing);

  const mainExhaust = new THREE.Mesh(new THREE.CylinderGeometry(0.45, 0.2, 0.6, 16), thrusterGlowMat);
  mainExhaust.rotateX(Math.PI / 2);
  mainExhaust.position.set(0, 0, -2.3);
  vessel.add(mainExhaust);

  return vessel;
}
```

---

### Archetype C: Cyberpunk Megastructure / Tower (Urban, Dark, Sci-Fi)
Stacked multi-level monolith with setback terraces, antenna arrays, and glowing windows.

```js
function buildCyberpunkTower() {
  const tower = new THREE.Group();

  const concreteMat = new THREE.MeshStandardMaterial({ color: 0x0f172a, roughness: 0.85 });
  const metalMat = new THREE.MeshStandardMaterial({ color: 0x334155, roughness: 0.3, metalness: 0.8 });
  const windowMat = new THREE.MeshStandardMaterial({ color: 0x0284c7, emissive: 0x38bdf8, emissiveIntensity: 2.0 });
  const beaconMat = new THREE.MeshStandardMaterial({ color: 0xff0055, emissive: 0xff0055, emissiveIntensity: 4.0 });

  const tiers = [
    { w: 3.2, h: 2.5, d: 3.2 },
    { w: 2.4, h: 3.0, d: 2.4 },
    { w: 1.6, h: 3.5, d: 1.6 },
    { w: 0.9, h: 2.0, d: 0.9 }
  ];

  let currentY = 0;
  tiers.forEach((t, i) => {
    // Main block
    const block = new THREE.Mesh(new THREE.BoxGeometry(t.w, t.h, t.d), concreteMat);
    block.position.y = currentY + t.h / 2;
    block.castShadow = true;
    tower.add(block);

    // Illuminated band / horizontal glass strip
    const band = new THREE.Mesh(new THREE.BoxGeometry(t.w * 1.02, 0.35, t.d * 1.02), windowMat);
    band.position.y = currentY + t.h * 0.7;
    tower.add(band);

    // Metal ledge collar
    const ledge = new THREE.Mesh(new THREE.BoxGeometry(t.w * 1.08, 0.1, t.d * 1.08), metalMat);
    ledge.position.y = currentY + t.h;
    tower.add(ledge);

    currentY += t.h;
  });

  // Top Communications Mast & Beacon
  const mast = new THREE.Mesh(new THREE.CylinderGeometry(0.04, 0.15, 2.5, 8), metalMat);
  mast.position.y = currentY + 1.25;
  tower.add(mast);

  const beacon = new THREE.Mesh(new THREE.SphereGeometry(0.12, 12, 12), beaconMat);
  beacon.position.y = currentY + 2.5;
  tower.add(beacon);

  return tower;
}
```

---

### Archetype D: Luxury Smart Device / Hardware Hero (SaaS, Product, FinTech)
Chamfered aluminum unibody, glass bezel, procedural camera array, and floating UI halo.

```js
function buildSmartDevice() {
  const device = new THREE.Group();

  const bodyMat = new THREE.MeshStandardMaterial({ color: 0x18181b, roughness: 0.25, metalness: 0.95 });
  const screenMat = new THREE.MeshStandardMaterial({ color: 0x050505, roughness: 0.05, metalness: 0.1 });
  const lensMat = new THREE.MeshStandardMaterial({ color: 0x1e1b4b, roughness: 0.1, metalness: 0.9 });
  const uiGlowMat = new THREE.MeshStandardMaterial({ color: 0x6366f1, emissive: 0x818cf8, emissiveIntensity: 1.8 });

  // Rounded rectangle chassis (using ExtrudeGeometry with rounded shape)
  const w = 2.4, h = 4.8, radius = 0.4;
  const shape = new THREE.Shape();
  shape.moveTo(-w/2 + radius, -h/2);
  shape.lineTo(w/2 - radius, -h/2);
  shape.quadraticCurveTo(w/2, -h/2, w/2, -h/2 + radius);
  shape.lineTo(w/2, h/2 - radius);
  shape.quadraticCurveTo(w/2, h/2, w/2 - radius, h/2);
  shape.lineTo(-w/2 + radius, h/2);
  shape.quadraticCurveTo(-w/2, h/2, -w/2, h/2 - radius);
  shape.lineTo(-w/2, -h/2 + radius);
  shape.quadraticCurveTo(-w/2, -h/2, -w/2 + radius, -h/2);

  const extrudeSettings = { depth: 0.28, bevelEnabled: true, bevelSegments: 4, steps: 1, bevelSize: 0.04, bevelThickness: 0.04 };
  const bodyGeo = new THREE.ExtrudeGeometry(shape, extrudeSettings);
  bodyGeo.center();
  const body = new THREE.Mesh(bodyGeo, bodyMat);
  body.castShadow = true;
  device.add(body);

  // Front OLED Glass Screen
  const screenGeo = new THREE.PlaneGeometry(w - 0.15, h - 0.2);
  const screen = new THREE.Mesh(screenGeo, screenMat);
  screen.position.z = 0.18;
  device.add(screen);

  // UI Highlight Bar (Simulated UI element)
  const uiBar = new THREE.Mesh(new THREE.PlaneGeometry(1.6, 0.4), uiGlowMat);
  uiBar.position.set(0, 0.8, 0.182);
  device.add(uiBar);

  // Rear Camera bump
  const bump = new THREE.Mesh(new THREE.CylinderGeometry(0.45, 0.45, 0.1, 24), bodyMat);
  bump.rotateX(Math.PI / 2);
  bump.position.set(0.6, 1.6, -0.2);
  device.add(bump);

  // Camera Dual Lenses
  [-0.15, 0.15].forEach((offset) => {
    const lens = new THREE.Mesh(new THREE.CylinderGeometry(0.14, 0.14, 0.12, 16), lensMat);
    lens.rotateX(Math.PI / 2);
    lens.position.set(0.6, 1.6 + offset, -0.22);
    device.add(lens);
  });

  return device;
}
```

---

### Archetype E: Low-Poly Floating Island & Bonsai Tree (Nature, Minimalist, Game)
Faceted terrain rock with stratified strata and a leafy geometric bonsai.

```js
function buildFloatingIsland() {
  const island = new THREE.Group();

  const grassMat = new THREE.MeshStandardMaterial({ color: 0x4ade80, roughness: 0.9, flatShading: true });
  const rockMat  = new THREE.MeshStandardMaterial({ color: 0x57534e, roughness: 0.95, flatShading: true });
  const woodMat  = new THREE.MeshStandardMaterial({ color: 0x78350f, roughness: 0.9, flatShading: true });
  const leafMat  = new THREE.MeshStandardMaterial({ color: 0x15803d, roughness: 0.8, flatShading: true });

  // Floating Rock Base (inverted faceted cone)
  const rockGeo = new THREE.ConeGeometry(3.0, 3.8, 7);
  rockGeo.rotateX(Math.PI);
  const rock = new THREE.Mesh(rockGeo, rockMat);
  rock.position.y = -1.9;
  rock.castShadow = true;
  island.add(rock);

  // Top Grass Cap
  const grassGeo = new THREE.CylinderGeometry(3.1, 3.0, 0.4, 7);
  const grass = new THREE.Mesh(grassGeo, grassMat);
  grass.position.y = 0.05;
  grass.receiveShadow = true;
  island.add(grass);

  // Bonsai Trunk (slanted cylinders)
  const trunk1 = new THREE.Mesh(new THREE.CylinderGeometry(0.18, 0.28, 1.4, 6), woodMat);
  trunk1.position.set(0.2, 0.8, 0);
  trunk1.rotation.z = -0.25;
  trunk1.castShadow = true;
  island.add(trunk1);

  const trunk2 = new THREE.Mesh(new THREE.CylinderGeometry(0.12, 0.18, 1.2, 6), woodMat);
  trunk2.position.set(0.55, 1.8, 0);
  trunk2.rotation.z = 0.35;
  trunk2.castShadow = true;
  island.add(trunk2);

  // Foliage Clusters (Faceted Icosahedrons)
  const foliagePositions = [
    [0.7, 2.4, 0, 0.9],
    [0.1, 1.9, 0.3, 0.65],
    [1.1, 1.8, -0.3, 0.6]
  ];
  foliagePositions.forEach(([fx, fy, fz, fscale]) => {
    const cluster = new THREE.Mesh(new THREE.IcosahedronGeometry(fscale, 1), leafMat);
    cluster.position.set(fx, fy, fz);
    cluster.castShadow = true;
    island.add(cluster);
  });

  return island;
}
```

---

## 3. Scene Composition & Environmental Depth

A solo object in empty space looks like a model viewer. A **composed scene** establishes depth, scale, and atmosphere:

### Visual Hierarchy
* **Hero Object**: Dominates **40–60% of viewport**.
* **Atmospheric Particles**: Soft, slow drift for depth.
* **Grounding Layer**: `ShadowMaterial` plane or subtle fog floor.
* **Bounding Scale**: Keep hero dimensions within **5–10 Three.js units** so camera, lights, and fog work in comfortable ranges.

### Themed Particle Presets
```js
function createParticleField(count = 200, spread = 15, color = 0xffffff, size = 0.04) {
  const positions = new Float32Array(count * 3);
  for (let i = 0; i < count; i++) {
    positions[i * 3]     = (Math.random() - 0.5) * spread;
    positions[i * 3 + 1] = Math.random() * spread * 0.6;
    positions[i * 3 + 2] = (Math.random() - 0.5) * spread;
  }
  const geo = new THREE.BufferGeometry();
  geo.setAttribute('position', new THREE.BufferAttribute(positions, 3));
  const mat = new THREE.PointsMaterial({
    color, size, transparent: true, opacity: 0.6, depthWrite: false, sizeAttenuation: true
  });
  return new THREE.Points(geo, mat);
}
```
- **Sunset Embers / Fireflies**: `color: 0xffaa44`, `count: 80`, upward drift.
- **Dust Motes**: `color: 0xeeeedd`, `count: 150`, gentle brownian motion.
- **Cherry Blossoms**: `color: 0xffbbcc`, `count: 40`, falling sway.
- **Cyber Data Sparks**: `color: 0x38bdf8`, `count: 200`, vertical rise.
