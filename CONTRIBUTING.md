# Contributing to 3DSkill

Thank you for your interest in improving **3DSkill**!

## How to Contribute

1. **Fork the Repository** and clone your fork locally.
2. **Create a Feature Branch**:
   ```bash
   git checkout -b feat/my-new-blueprint
   ```
3. **Follow the Design & Code Doctrines**:
   - **No external 3D asset downloads**: Geometry must be procedurally generated via Three.js primitives and math.
   - **No external raster images**: Textures must be generated in memory via HTML5 Canvas 2D or procedural GLSL shaders.
   - **Always apply the 12-point self-check gate** in `SKILL.md`.
   - **Maintain 60+ FPS performance**: Pre-allocate scratch vectors outside `requestAnimationFrame`.
4. **Submit a Pull Request** describing the additions or fixes made.

## Guidelines for New Procedural Blueprints
- Must be recognizable by silhouette at a glance.
- Keep polygon counts balanced and clean.
- Include proper material roughness/metalness values and shadow casting/receiving flags.
