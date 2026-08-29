# Procedural Geometry Recipes & Blueprints

The goal of procedural geometry is a recognizable physical subject constructed entirely from Three.js mathematical primitives — no modeling software, no downloaded 3D assets.

Recognizability is determined by **silhouette**, not triangle density. If the outer contour is proportioned correctly, viewers read the subject instantly at a glance.

---

## 1. Core Geometric Principles

### The Lathe Trick for Curved Silhouettes
Stacking simple boxes and cylinders creates a generic "block pyramid," not a refined architectural subject. Revolved contours (`THREE.LatheGeometry`) build smooth surfaces of revolution from a 2D profile curve.

```js
// Profile: (x = radius from center axis, y = height), defined from bottom to top.
// Flares outward then curls upward:
const roofProfile = [
  new THREE.Vector2(0.0, 0.0),
  new THREE.Vector2(2.4, 0.05),
  new THREE.Vector2(2.6, 0.35),   // flare out
  new THREE.Vector2(2.3, 0.55),   // curl back in and up
  new THREE.Vector2(1.6, 0.70),
  new THREE.Vector2(0.3, 0.78),
  new THREE.Vector2(0.0, 0.80),
];
const curvedLatheGeo = new THREE.LatheGeometry(roofProfile, 24);
```

### Match Roof Shape to Footprint Shape (Crucial Rule)
A `LatheGeometry` shape is circular in plan. If the body underneath is a square/rectangular box, a Lathe roof reads as a circular disc awkwardly overlapping square corners.
- **Round body (dome, silo, drum tower, lantern, vase) → `LatheGeometry` roof is correct.**
- **Square/rectangular body (pagoda, house, temple, tower) → Square cone roof is required.** Use `THREE.ConeGeometry(radius, height, 4)` rotated 45° (`roof.rotation.y = Math.PI / 4`) so flat faces line up with box faces.
- **Upturned eave corners:** Add small separate `TorusGeometry` arc flourishes at each corner.

---

## 2. Fully Worked Geometry Blueprints

### Blueprint A: Japanese Shrine Pagoda
*Plan Shape Rule:* Square body with square 4-sided cone roof rotated 45°. Tiers stack with 20% proportional reduction. Deep overhang factor (1.75× wall width).

```js
function buildPagoda(tierCount = 3) {
  const pagoda = new THREE.Group();
  let y = 0;
  const tierHeight = 1.1;

  const wallMat  = new THREE.MeshStandardMaterial({ color: 0xede6d6, roughness: 0.85 });
  const frameMat = new THREE.MeshStandardMaterial({ color: 0x8b4a3e, roughness: 0.55 });
  const roofMat  = new THREE.MeshStandardMaterial({ color: 0x14171c, roughness: 0.45, metalness: 0.1 });
  const stoneMat = new THREE.MeshStandardMaterial({ color: 0x6b6259, roughness: 0.9 });
  const goldMat  = new THREE.MeshStandardMaterial({ color: 0xd9a55c, roughness: 0.3, metalness: 0.8 });

  const pillarGeo = new THREE.CylinderGeometry(0.05, 0.05, 1.1, 8);
  const flourishGeo = new THREE.TorusGeometry(0.45, 0.05, 6, 10, Math.PI * 0.55);

  // Stone base plinth (4-sided cylinder = chamfered square)
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

    // Eave flourishes at 4 corners
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

  // Golden Spire (Sōrin)
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

### Blueprint B: Modern Minimalist Office Tower (Nocturne Cityscape)
*Plan Shape Rule:* Rectangular tiered massing with stepped vertical setbacks. Reuses the square-on-square alignment of the pagoda, but replaces sloping roofs with horizontal cantilevered concrete slabs and glowing window bands.

```js
function buildModernTower() {
  const tower = new THREE.Group();

  const concreteMat = new THREE.MeshStandardMaterial({ color: 0x6b6259, roughness: 0.85 });
  const trimMat = new THREE.MeshStandardMaterial({ color: 0x2c2c2e, roughness: 0.35, metalness: 0.8 });
  const windowMat = new THREE.MeshStandardMaterial({
    color: 0x10171a,
    emissive: 0xf2a94e,
    emissiveIntensity: 1.6,
    roughness: 0.2
  });

  const tiers = [
    { width: 3.2, height: 2.8, depth: 2.8 },
    { width: 2.4, height: 3.2, depth: 2.2 },
    { width: 1.6, height: 3.6, depth: 1.6 },
    { width: 1.0, height: 1.8, depth: 1.0 }
  ];

  let currentY = 0;
  tiers.forEach((t) => {
    // 1. Structural Concrete Core
    const core = new THREE.Mesh(new THREE.BoxGeometry(t.width, t.height, t.depth), concreteMat);
    core.position.y = currentY + t.height / 2;
    core.castShadow = true;
    tower.add(core);

    // 2. Horizontal Ribbon Window Bands
    const bandHeight = t.height * 0.35;
    const windows = new THREE.Mesh(new THREE.BoxGeometry(t.width * 1.02, bandHeight, t.depth * 1.02), windowMat);
    windows.position.y = currentY + t.height * 0.55;
    tower.add(windows);

    // 3. Cantilevered Floor Ledge Collar
    const slab = new THREE.Mesh(new THREE.BoxGeometry(t.width * 1.12, 0.12, t.depth * 1.12), trimMat);
    slab.position.y = currentY + t.height;
    slab.castShadow = true;
    tower.add(slab);

    currentY += t.height;
  });

  // Rooftop Mechanical Penthouse & Spire
  const penthouse = new THREE.Mesh(new THREE.BoxGeometry(0.7, 0.8, 0.7), trimMat);
  penthouse.position.y = currentY + 0.4;
  tower.add(penthouse);

  const mast = new THREE.Mesh(new THREE.CylinderGeometry(0.02, 0.08, 2.2, 8), trimMat);
  mast.position.y = currentY + 0.8 + 1.1;
  tower.add(mast);

  const beacon = new THREE.Mesh(new THREE.SphereGeometry(0.08, 12, 12), windowMat);
  beacon.position.y = currentY + 0.8 + 2.2;
  tower.add(beacon);

  return tower;
}
```

---

### Blueprint C: Low-Poly Floating Island & Bonsai
*Plan Shape Rule:* Inverted cone rock base tapering downward to an off-center tip; flat circular grass cap on top; organic multi-segment trunk topped with faceted icosahedron foliage masses. Reuses proportional weighting (heavy base tapering to fine crown) while using `flatShading: true` faceted primitives.

```js
function buildFloatingIsland() {
  const island = new THREE.Group();

  const rockMat  = new THREE.MeshStandardMaterial({ color: 0x6b6259, roughness: 0.95, flatShading: true });
  const grassMat = new THREE.MeshStandardMaterial({ color: 0x4a6b48, roughness: 0.85, flatShading: true });
  const woodMat  = new THREE.MeshStandardMaterial({ color: 0x5c2c22, roughness: 0.9, flatShading: true });
  const leafMat  = new THREE.MeshStandardMaterial({ color: 0x2d5a36, roughness: 0.75, flatShading: true });

  // 1. Inverted 7-sided Cone Base (strata rock)
  const rockGeo = new THREE.ConeGeometry(3.2, 4.0, 7);
  rockGeo.rotateX(Math.PI);
  const rock = new THREE.Mesh(rockGeo, rockMat);
  rock.position.y = -2.0;
  rock.castShadow = true;
  island.add(rock);

  // 2. Top Grass Cap Plinth
  const grassGeo = new THREE.CylinderGeometry(3.3, 3.2, 0.35, 7);
  const grass = new THREE.Mesh(grassGeo, grassMat);
  grass.position.y = 0.05;
  grass.receiveShadow = true;
  island.add(grass);

  // 3. Multi-Segment Bonsai Trunk
  const trunkLower = new THREE.Mesh(new THREE.CylinderGeometry(0.2, 0.3, 1.4, 6), woodMat);
  trunkLower.position.set(0.2, 0.75, 0);
  trunkLower.rotation.z = -0.22;
  trunkLower.castShadow = true;
  island.add(trunkLower);

  const trunkUpper = new THREE.Mesh(new THREE.CylinderGeometry(0.12, 0.2, 1.2, 6), woodMat);
  trunkUpper.position.set(0.55, 1.8, 0);
  trunkUpper.rotation.z = 0.32;
  trunkUpper.castShadow = true;
  island.add(trunkUpper);

  // 4. Faceted Foliage Clusters (Icosahedrons)
  const clusters = [
    { pos: [0.75, 2.4, 0.0],  scale: 0.95 },
    { pos: [0.10, 1.9, 0.35], scale: 0.70 },
    { pos: [1.15, 1.7, -0.3], scale: 0.65 },
  ];
  clusters.forEach(({ pos, scale }) => {
    const foliage = new THREE.Mesh(new THREE.IcosahedronGeometry(scale, 1), leafMat);
    foliage.position.set(...pos);
    foliage.castShadow = true;
    island.add(foliage);
  });

  return island;
}
```

---

### Blueprint D: Sacred Torii Gate & Stone Lantern Companion
*Plan Shape Rule:* Torii features dual cylindrical pillars supporting a sweeping upward-curving upper lintel (kasagi) and straight lower tie-beam (nuki). Lantern uses square stone base, square windowed firebox, and pitched square roof. Reuses the exact pagoda square roof and stone plinth formulas at a smaller scale.

```js
function buildToriiGate() {
  const torii = new THREE.Group();

  const lacquerMat = new THREE.MeshStandardMaterial({ color: 0x8b4a3e, roughness: 0.55 });
  const blackMat   = new THREE.MeshStandardMaterial({ color: 0x14171c, roughness: 0.45 });
  const stoneMat   = new THREE.MeshStandardMaterial({ color: 0x6b6259, roughness: 0.9 });

  const pillarHeight = 3.6;
  const pillarSpacing = 2.4;

  // 1. Dual Stone Footing Plinths
  [-1, 1].forEach((side) => {
    const footing = new THREE.Mesh(new THREE.CylinderGeometry(0.3, 0.35, 0.25, 12), stoneMat);
    footing.position.set(side * pillarSpacing / 2, 0.125, 0);
    footing.receiveShadow = true;
    torii.add(footing);

    // Main Columns (slight inward tilt)
    const pillar = new THREE.Mesh(new THREE.CylinderGeometry(0.16, 0.2, pillarHeight, 16), lacquerMat);
    pillar.position.set(side * pillarSpacing / 2, pillarHeight / 2 + 0.25, 0);
    pillar.rotation.z = -side * 0.04;
    pillar.castShadow = true;
    torii.add(pillar);
  });

  // 2. Lower Tie-Beam (Nuki)
  const nuki = new THREE.Mesh(new THREE.BoxGeometry(pillarSpacing + 0.9, 0.16, 0.22), lacquerMat);
  nuki.position.set(0, pillarHeight * 0.78, 0);
  nuki.castShadow = true;
  torii.add(nuki);

  // 3. Upper Main Lintel (Kasagi) with slight curved profile
  const kasagi = new THREE.Mesh(new THREE.BoxGeometry(pillarSpacing + 1.6, 0.24, 0.32), lacquerMat);
  kasagi.position.set(0, pillarHeight + 0.25, 0);
  kasagi.castShadow = true;
  torii.add(kasagi);

  // Black Roof Cap on top of Kasagi
  const roofCap = new THREE.Mesh(new THREE.BoxGeometry(pillarSpacing + 1.7, 0.08, 0.36), blackMat);
  roofCap.position.set(0, pillarHeight + 0.40, 0);
  roofCap.castShadow = true;
  torii.add(roofCap);

  return torii;
}
```
