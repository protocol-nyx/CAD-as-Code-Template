---
sidebar_position: 5
---

# Reference assets for CAD-as-Code

External files can power a model, but they should not silently become the model. In this template, the editable source of truth stays in `cad/`. Imported assets belong in a reference lane: measure them, align them, trace them, compare against them, then rebuild clean parametric geometry in [build123d](https://build123d.readthedocs.io/).

Think of the reference file as evidence on the bench:

- A STEP or BREP file is precise CAD evidence.
- An SVG or DXF file is a 2D stencil.
- A raster image is a pixel sketch that needs tracing, calibration, or interpretation.
- A mesh is a triangle fossil: measurable and useful, but not editable design intent.

## Current stack

| Tool | Already in the template? | Best role in a reference-asset workflow |
|------|---------------------------|-----------------------------------------|
| [build123d import/export](https://build123d.readthedocs.io/en/latest/import_export.html) | Yes | Import STEP, BREP, SVG, DXF, and STL; export generated model artifacts. |
| [Open CASCADE Technology](/reference/open-cascade) | Yes, through build123d/OCP | Precise boundary-representation geometry under the parametric model. |
| [trimesh](https://trimesh.org/) | Yes | Load and inspect STL, OBJ, PLY, GLB, and other triangular meshes. |
| [OCP CAD Viewer](/getting-started/ocp-viewer) | Yes | Visual review while rebuilding clean geometry from references. |
| [MakerRepo](/tools/makerrepo) and [CAD tooling export](/tools/cad-tooling/export) | Yes | Register, export, and verify generated artifacts from code. |
| [pytest](/modeling/testing) | Yes | Turn measurements and fit checks into repeatable tests. |

## Component types and use cases

### STEP and BREP: precise CAD references

Use these when the external component comes from another CAD system and still carries precise model geometry.

| What you can do | Suggested tools | Notes |
|-----------------|-----------------|-------|
| Import a supplier part or adjacent assembly. | [`build123d.import_step`](https://build123d.readthedocs.io/en/latest/import_export.html), build123d BREP import/export | Best handoff format when you need real CAD surfaces and solids. |
| Measure bounding boxes, faces, axes, and clearances. | build123d, [Open CASCADE](/reference/open-cascade), pytest | Good for checking fit around motors, bearings, fasteners, PCBs, and purchased parts. |
| Build new parametric geometry around imported geometry. | build123d builders in `cad/parts/` and `cad/assemblies/` | Keep the imported file as a reference; keep the editable design in code. |
| Export regenerated artifacts. | [MakerRepo](/tools/makerrepo), [`cad_tooling.export`](/tools/cad-tooling/export) | Generated STEP/STL/GLB/3MF should come from source, not from hand-edited reference files. |

Limit: STEP and BREP do not restore the original feature tree. You get precise geometry, not the sketch/extrude/fillet history that created it.

### SVG and DXF: 2D profile references

Use these when the external component is a flat outline, logo, gasket, panel shape, cut path, engraving, or drawing profile.

| What you can do | Suggested tools | Notes |
|-----------------|-----------------|-------|
| Import clean vector paths as faces, wires, or sketch geometry. | [`build123d.import_svg`](https://build123d.readthedocs.io/en/latest/import_export.html), [`build123d.import_dxf`](https://build123d.readthedocs.io/en/latest/import_export.html) | Best path from outside 2D art into CAD-as-Code. |
| Extrude, cut, emboss, engrave, or use as construction geometry. | build123d sketches and builders | Works best with simple, closed, scaled paths. |
| Convert a drawing or logo into a profile before modeling. | [Inkscape Trace Bitmap](https://inkscape-manuals.readthedocs.io/en/latest/tracing-an-image.html), [Potrace](https://potrace.sourceforge.net/) | Trace raster artwork to SVG, then import the SVG. |
| Compare model sections against external profiles. | build123d `section()` / projection tools, pytest | Useful for gasket seats, mating outlines, and laser-cut panels. |

Limit: vector paths are not automatically manufacturing-safe. Check scale, closed paths, duplicate paths, tiny segments, stroke-vs-fill behavior, and units before using them as geometry.

### Raster images: pixels as calibrated references

Raster images include PNG, JPG, BMP, TIFF, screenshots, scans, and photos. They are made of pixels, not curves or CAD entities.

| What you can do | Suggested tools | Notes |
|-----------------|-----------------|-------|
| Trace a silhouette, logo, sketch, or scan into vector geometry. | [Potrace](https://potrace.sourceforge.net/), [Inkscape Trace Bitmap](https://inkscape-manuals.readthedocs.io/en/latest/tracing-an-image.html) | Convert pixels to SVG/DXF first, then import the vector result. |
| Clean, crop, threshold, resize, or normalize an image before tracing. | [Pillow](https://pillow.readthedocs.io/) | Good lightweight preprocessing layer for Python scripts. |
| Extract edges, contours, feature points, holes, and scale marks. | [OpenCV](https://docs.opencv.org/), [scikit-image measure](https://scikit-image.org/docs/stable/api/skimage.measure.html) | Good for measurements and automation, especially when images are consistent. |
| Build a heightfield or bas-relief from grayscale values. | Pillow, NumPy, scikit-image, build123d surface/mesh handoff | Useful for decorative reliefs, terrain-like surfaces, stamps, and texture-driven experiments. |
| Use a photo as a human modeling reference. | OCP CAD Viewer screenshots, documented dimensions, manifests | Only trustworthy when calibrated with known dimensions, lens assumptions, and view direction. |

Limit: raster-to-CAD work is only as good as calibration. A photo without known scale, perspective, and lens distortion is visual reference, not engineering truth.

Glossary notes:

- [Raster image](/reference/glossary#raster-image): an image stored as a grid of pixels. Great for photos and scans; weak as direct CAD input.
- [Rasterization](/reference/glossary#rasterization): converting vector or 3D information into pixels. Once geometry is rasterized, it loses editable curves, topology, and dimensions unless you reconstruct them.
- [Vectorization](/reference/glossary#vectorization): converting pixels into vector paths, often by tracing edges or filled regions.
- [Thresholding](/reference/glossary#thresholding): splitting pixels into groups, commonly foreground/background, so contours can be extracted.
- [Contour](/reference/glossary#contour): a line around a region or constant-value boundary in an image.
- [Calibration](/reference/glossary#calibration): tying image pixels to real-world dimensions using known measurements or reference objects.

### STL, OBJ, PLY, GLB, and scanned meshes: triangle references

Meshes are excellent reference targets and poor parametric source files. They describe surfaces as triangles. They usually do not contain sketches, constraints, analytic cylinders, hole intent, feature names, or design history.

| What you can do | Suggested tools | Notes |
|-----------------|-----------------|-------|
| Load and inspect mesh files. | [trimesh](https://trimesh.org/) | Check extents, volume, watertightness, triangle count, normals, surface area, and center of mass. |
| Use a mesh as a visual overlay. | OCP CAD Viewer, Blender, MeshLab | Helpful while recreating a clean build123d model. |
| Slice a mesh into cross-sections. | trimesh section tools, build123d sections of generated geometry | Compare measured sections against rebuilt parametric geometry. |
| Sample surfaces and compare fit. | trimesh, NumPy, pytest | Test that a rebuilt model stays within a tolerance of the reference mesh. |
| Align a scan to a model or another scan. | [Open3D ICP registration](https://www.open3d.org/docs/0.19.0/tutorial/pipelines/icp_registration.html) | Useful when references arrive from photogrammetry, 3D scanning, or imperfect coordinate frames. |
| Clean or repair messy meshes. | [MeshLab](https://www.meshlab.net/), [PyMeshLab filters](https://pymeshlab.readthedocs.io/en/0.1.8/filter_list.html), [Blender STL tools](https://docs.blender.org/manual/en/latest/files/import_export/stl.html) | Keep these as preprocessing tools; do not make hand-edited meshes the repo source of truth. |
| Fit primitive clues for reverse modeling. | trimesh, Open3D, scikit-image, custom scripts | Use detected planes, cylinders, axes, and section curves as hints for parametric rebuilds. |

Limit: importing STL into build123d can make it visible or measurable, but it does not reverse-engineer design intent. Treat it as a ghost overlay and rebuild the living part in code.

Glossary notes:

- [Mesh](/reference/glossary#mesh): a surface represented by vertices, edges, and faces, usually triangles.
- [Watertight mesh](/reference/glossary#watertight-mesh): a closed mesh with no holes or non-manifold leaks; volume calculations are meaningful only when this is true.
- [Point cloud](/reference/glossary#point-cloud): unconnected 3D sample points, commonly produced by scans.
- [Registration](/reference/glossary#registration): aligning one geometry dataset to another.
- [ICP](/reference/glossary#icp): Iterative Closest Point, a registration method for refining point-cloud or mesh alignment.
- [Tolerance](/reference/glossary#tolerance): the accepted difference between generated geometry and the reference target.

## Suggested repository pattern

Add a reference lane instead of scattering outside files through model code:

```text
references/
  images/
  meshes/
  svg/
  step/
  manifests/
tests/fixtures/reference_assets/
cad_tooling/reference_assets.py
```

Each asset should have a small manifest:

```yaml
name: motor_plate_scan
asset_type: mesh
path: references/meshes/motor_plate_scan.stl
source: vendor scan
units: mm
scale_factor: 1.0
known_dimensions:
  mounting_hole_spacing: 42.0
alignment_transform: references/manifests/motor_plate_scan.transform.json
intended_use: Fit reference for rebuilt motor plate
confidence: medium
related_model: cad/parts/motor_plate.py
comparison_tolerance_mm: 0.5
license: internal reference only
```

A manifest makes the hidden assumptions reviewable: where the file came from, what units it uses, how it was scaled, how it was aligned, what it is allowed to prove, and how close the generated model should be.

## Automation ideas

Good next commands for this template:

| Command idea | What it proves |
|--------------|----------------|
| `just reference-mesh-summary references/meshes/foo.stl` | Reports extents, volume, watertightness, surface area, triangle count, and center of mass. |
| `just reference-svg-summary references/svg/foo.svg` | Reports imported faces/wires and warns about empty or open paths. |
| `just reference-compare model_name references/meshes/foo.stl` | Exports the generated model, samples both surfaces, and checks tolerance. |
| `just reference-report` | Builds a Markdown or JSON report for every manifest. |
| `pytest -m reference` | Runs reference-asset checks without mixing them into fast unit tests. |

## Practical rule

Use external assets to inform the model, not to replace it.

The CAD-as-Code promise is still the same: clean parametric source, repeatable exports, reviewable changes, and tests that protect design intent. External references can make the model smarter, but the repo should always be able to explain what was imported, what was inferred, and what was rebuilt.