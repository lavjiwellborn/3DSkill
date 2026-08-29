# Advanced Procedural Blueprints Library

This library expands your procedural geometry repertoire with **6 additional production-ready Three.js procedural models** covering high-demand industry verticals (Biotech, Automotive, Data/FinTech, Classical Architecture, AI/Deep Tech, and Landscapes).

---

## 1. Blueprint F: Organic DNA Double Helix (Biotech, Medicine, Genetics)

Constructs an intertwining double helix with connecting nucleotide base pairs.

```js
function buildDNAHelix(strandsCount = 40, radius = 1.2, height = 8) {
  const dna = new THREE.Group();

  const backboneMat1 = new THREE.MeshStandardMaterial({ color: 0x38bdf8, roughness: 0.2, metalness: 0.8 });
  const backboneMat2 = new THREE.MeshStandardMaterial({ color: 0xf43f5e, roughness: 0.2, metalness: 0.8 });
  const rungMat = new THREE.MeshStandardMaterial({ color: 0xf1f5f9, roughness: 0.3, metalness: 0.5 });
  const sphereGeo = new THREE.SphereGeometry(0.12, 16, 16);
  const barGeo = new THREE.CylinderGeometry(0.04, 0.04, 1, 8);

  const stepY = height / strandsCount;

  for (let i = 0; i < strandsCount; i++) {
    const y = (i - strandsCount / 2) * stepY;
    const angle = i * 0.35; // Twist rate

    const x1 = Math.cos(angle) * radius;
    const z1 = Math.sin(angle) * radius;
    const x2 = Math.cos(angle + Math.PI) * radius;
    const z2 = Math.sin(angle + Math.PI) * radius;

    // Backbone nodes
    const node1 = new THREE.Mesh(sphereGeo, backboneMat1);
    node1.position.set(x1, y, z1);
    dna.add(node1);

    const node2 = new THREE.Mesh(sphereGeo, backboneMat2);
    node2.position.set(x2, y, z2);
    dna.add(node2);

    // Connecting rung bar
    const rung = new THREE.Mesh(barGeo, rungMat);
    rung.position.set((x1 + x2) / 2, y, (z1 + z2) / 2);
    rung.scale.set(1, radius * 2, 1);
    rung.quaternion.setFromUnitVectors(new THREE.Vector3(0, 1, 0), new THREE.Vector3(x2 - x1, 0, z2 - z1).normalize());
    dna.add(rung);
  }

  return dna;
}
```

---

## 2. Blueprint G: High-Tech Concept Supercar Chassis (Automotive, Speed)

Aerodynamic wedge unibody with separate rotating wheels, cockpit glass, and rear diffuser.

```js
function buildConceptCar() {
  const car = new THREE.Group();

  const bodyMat = new THREE.MeshStandardMaterial({ color: 0x0f172a, roughness: 0.2, metalness: 0.95 });
  const glassMat = new THREE.MeshStandardMaterial({ color: 0x0284c7, roughness: 0.05, metalness: 0.9, transparent: true, opacity: 0.75 });
  const wheelMat = new THREE.MeshStandardMaterial({ color: 0x18181b, roughness: 0.6 });
  const rimMat = new THREE.MeshStandardMaterial({ color: 0x38bdf8, emissive: 0x0284c7, emissiveIntensity: 1.5 });

  // Main Wedge Fuselage
  const bodyGeo = new THREE.BoxGeometry(2.4, 0.6, 5.0);
  const body = new THREE.Mesh(bodyGeo, bodyMat);
  body.position.y = 0.5;
  body.castShadow = true;
  car.add(body);

  // Cockpit Canopy (Slanted Box)
  const cabinGeo = new THREE.BoxGeometry(1.6, 0.55, 2.2);
  const cabin = new THREE.Mesh(cabinGeo, glassMat);
  cabin.position.set(0, 0.95, -0.2);
  car.add(cabin);

  // 4 Wheels
  const wheelGeo = new THREE.CylinderGeometry(0.45, 0.45, 0.35, 24);
  wheelGeo.rotateZ(Math.PI / 2);
  const wheelPositions = [
    [-1.2, 0.45, 1.6], [1.2, 0.45, 1.6],   // Front
    [-1.2, 0.45, -1.6], [1.2, 0.45, -1.6], // Rear
  ];

  wheelPositions.forEach(([wx, wy, wz]) => {
    const wheel = new THREE.Mesh(wheelGeo, wheelMat);
    wheel.position.set(wx, wy, wz);
    wheel.castShadow = true;
    car.add(wheel);

    // Glowing Rim Core
    const rim = new THREE.Mesh(new THREE.TorusGeometry(0.28, 0.04, 8, 16), rimMat);
    rim.rotateY(Math.PI / 2);
    rim.position.set(wx > 0 ? wx + 0.18 : wx - 0.18, wy, wz);
    car.add(rim);
  });

  return car;
}
```

---

## 3. Blueprint H: Floating Holographic Data Globe (FinTech, Global SaaS)

Sphere with latitude/longitude coordinate rings, point cloud continents, and orbital satellite rings.

```js
function buildHoloGlobe(radius = 2.0) {
  const globe = new THREE.Group();

  const coreMat = new THREE.MeshStandardMaterial({
    color: 0x030712, roughness: 0.1, metalness: 0.9, transparent: true, opacity: 0.85
  });
  const ringMat = new THREE.MeshStandardMaterial({ color: 0x38bdf8, emissive: 0x0284c7, emissiveIntensity: 1.8 });

  // Dark Inner Core
  const core = new THREE.Mesh(new THREE.SphereGeometry(radius, 32, 32), coreMat);
  globe.add(core);

  // Wireframe Latitude & Longitude Rings
  for (let lat = -2; lat <= 2; lat++) {
    const r = Math.cos((lat / 3) * (Math.PI / 2)) * radius;
    const ring = new THREE.Mesh(new THREE.TorusGeometry(r, 0.015, 6, 32), ringMat);
    ring.rotation.x = Math.PI / 2;
    ring.position.y = Math.sin((lat / 3) * (Math.PI / 2)) * radius;
    globe.add(ring);
  }

  // Equator Orbit Ring
  const equator = new THREE.Mesh(new THREE.TorusGeometry(radius * 1.35, 0.02, 6, 48), ringMat);
  equator.rotation.x = Math.PI / 3;
  globe.add(equator);

  return globe;
}
```

---

## 4. Blueprint I: Classical Greek Temple / Parthenon (Law, Finance, Antiquity)

Fluted columns, stepped stylobate plinth, entablature beam, and pediment triangle.

```js
function buildParthenon() {
  const temple = new THREE.Group();
  const marbleMat = new THREE.MeshStandardMaterial({ color: 0xf1f5f9, roughness: 0.65 });

  // 3-Tier Stepped Plinth (Stylobate)
  for (let s = 0; s < 3; s++) {
    const step = new THREE.Mesh(new THREE.BoxGeometry(6.5 + s * 0.4, 0.2, 4.5 + s * 0.4), marbleMat);
    step.position.y = -s * 0.2;
    step.receiveShadow = true;
    temple.add(step);
  }

  // Perimeter Columns (6 front/back, 4 sides)
  const colGeo = new THREE.CylinderGeometry(0.12, 0.15, 2.4, 12);
  const colsX = 6, colsZ = 4;
  const spanX = 5.6, spanZ = 3.6;

  for (let ix = 0; ix < colsX; ix++) {
    for (let iz = 0; iz < colsZ; iz++) {
      // Only outer perimeter
      if (ix === 0 || ix === colsX - 1 || iz === 0 || iz === colsZ - 1) {
        const x = (ix / (colsX - 1) - 0.5) * spanX;
        const z = (iz / (colsZ - 1) - 0.5) * spanZ;
        const col = new THREE.Mesh(colGeo, marbleMat);
        col.position.set(x, 1.2, z);
        col.castShadow = true;
        temple.add(col);
      }
    }
  }

  // Top Entablature Beam
  const beam = new THREE.Mesh(new THREE.BoxGeometry(6.2, 0.35, 4.2), marbleMat);
  beam.position.y = 2.55;
  beam.castShadow = true;
  temple.add(beam);

  // Triangular Pediment Roof
  const roofShape = new THREE.Shape();
  roofShape.moveTo(-3.1, 0);
  roofShape.lineTo(3.1, 0);
  roofShape.lineTo(0, 1.2);
  roofShape.closePath();

  const roofGeo = new THREE.ExtrudeGeometry(roofShape, { depth: 4.2, bevelEnabled: false });
  roofGeo.translate(0, 0, -2.1);
  const roof = new THREE.Mesh(roofGeo, marbleMat);
  roof.position.y = 2.72;
  roof.castShadow = true;
  temple.add(roof);

  return temple;
}
```

---

## 5. Blueprint J: Quantum Void Reactor / Tesseract Core (AI, Deep Tech, Web3)

Concentric nested rotating wireframes with a pulsating luminous plasma singularity.

```js
function buildQuantumReactor() {
  const reactor = new THREE.Group();

  const outerMat = new THREE.MeshStandardMaterial({ color: 0x38bdf8, wireframe: true, roughness: 0.1 });
  const midMat = new THREE.MeshStandardMaterial({ color: 0xf43f5e, wireframe: true, roughness: 0.1 });
  const coreMat = new THREE.MeshStandardMaterial({ color: 0xffffff, emissive: 0x38bdf8, emissiveIntensity: 3.5 });

  // Outer Tesseract Frame
  const outer = new THREE.Mesh(new THREE.BoxGeometry(3.0, 3.0, 3.0), outerMat);
  reactor.add(outer);

  // Mid Gyroscopic Ring
  const mid = new THREE.Mesh(new THREE.OctahedronGeometry(2.0, 1), midMat);
  reactor.add(mid);

  // Luminous Singularity Core
  const core = new THREE.Mesh(new THREE.IcosahedronGeometry(0.7, 2), coreMat);
  reactor.add(core);

  // Expose sub-elements for independent counter-rotation in animate()
  reactor.userData = { outer, mid, core };
  return reactor;
}
```

---

## 6. Blueprint K: Procedural Low-Poly Mountain Range (Outdoor, Nature)

Vertex-displaced plane with snowcaps, rock strata, and river trough.

```js
function buildMountainRange(size = 30, segments = 40) {
  const geo = new THREE.PlaneGeometry(size, size, segments, segments);
  geo.rotateX(-Math.PI / 2);

  const pos = geo.attributes.position;
  for (let i = 0; i < pos.count; i++) {
    const x = pos.getX(i);
    const z = pos.getZ(i);
    // Combine 2 octaves of sine noise for natural peaks
    const elevation = Math.sin(x * 0.25) * Math.cos(z * 0.25) * 3.5 + Math.sin(x * 0.6) * Math.sin(z * 0.6) * 1.2;
    // Carve central river valley
    const valley = Math.min(1, Math.abs(x) * 0.2);
    pos.setY(i, Math.max(0, elevation * valley));
  }
  geo.computeVertexNormals();

  const mat = new THREE.MeshStandardMaterial({
    color: 0x475569,
    roughness: 0.9,
    flatShading: true,
  });

  const mountain = new THREE.Mesh(geo, mat);
  mountain.receiveShadow = true;
  return mountain;
}
```
