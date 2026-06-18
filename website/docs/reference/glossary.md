---
sidebar_position: 3
---

# Glossary

Short definitions for terms that show up across the template. These are intentionally brief. Follow the linked pages when you need setup steps or implementation details.

## CAD-as-Code

Parametric CAD treated like a software project. The model lives in readable source files, changes are reviewed in Git, checks run locally and in the CI/CD pipeline, and manufacturing artifacts are regenerated from source instead of hand-managed.

See [Modeling conventions](/modeling/conventions), [Testing strategy](/modeling/testing), and [CI/CD pipeline and Dagger](/workflows/ci-and-dagger).

## Source of truth

The file or directory that should be edited directly. In this template, source CAD belongs under `cad/`. STEP, STL, GLB, screenshots, and release bundles are generated outputs.

See [Project layout](/getting-started/project-layout) and [Export and formats](/workflows/export-and-formats).

## Dev Container

A containerized development environment that carries the project tools with the repo. It keeps Python, build123d, OCP viewer support, Node docs tooling, and command-line tools consistent across Codespaces, VS Code, Cursor, and compatible IDEs.

See [Dev Container](/getting-started/dev-container) and [IDEs and workspaces](/getting-started/ide-and-workspaces).

## Builder

A `make_*()` function returning a build123d `Part` or `Compound`. Builders should be pure geometry: inputs in, CAD object out.

See [Parts and assemblies](/modeling/parts-and-assemblies).

## Artifact

A MakerRepo `@artifact` entry point with a fixed default configuration for publish and export workflows.

See [MakerRepo](/tools/makerrepo).

## Generator

A MakerRepo `@customizable` entry point that accepts parameters, usually through a Pydantic model.

See [MakerRepo](/tools/makerrepo).

## Smoke export

A quick export check that discovers artifacts and writes formats such as STEP and STL. It catches broken export paths before a release.

See [Export and formats](/workflows/export-and-formats) and [Export and CI/CD pipeline troubleshooting](/troubleshooting/export-and-ci).

## Release bundle

The generated files attached to a GitHub Release, such as STL and PNG outputs per artifact.

See [Releases](/workflows/releases).

## CI/CD pipeline

The repeatable automation that runs checks and publish steps outside a developer's editor. In this repo, Dagger provides the portable pipeline and GitHub Actions runs it on GitHub.

See [CI/CD pipeline and Dagger](/workflows/ci-and-dagger).

## Linting

Automated checks that flag code style, typing, and dead-code problems before they turn into harder-to-debug modeling issues.

See [uv and quality](/tools/uv-and-quality).

## MCP

Model Context Protocol. MCP servers give AI agents controlled tools for running geometry code and inspecting viewer feedback while the source files remain under version control.

See [MCP servers](/tools/mcp-servers).

## build123d

A Python CAD library built on Open CASCADE. The template uses build123d for parametric model code.

See [Open CASCADE](/reference/open-cascade) and the [build123d docs](https://build123d.readthedocs.io/).

## OCCT

Open CASCADE Technology, the C++ boundary-representation geometry kernel under build123d.

See [Open CASCADE](/reference/open-cascade).

## OCP

Open CASCADE Python bindings used for visualization and export.

See [OCP CAD Viewer](/getting-started/ocp-viewer) and [OCP on GitHub](https://github.com/CadQuery/OCP).

## MakerRepo

Decorators and CLI tools for discovering, describing, and exporting manufacturing artifacts from code.

See [MakerRepo](/tools/makerrepo).

## Reference asset

An external file used to inform a model without becoming the editable source of truth. Examples include a supplier STEP file, scanned STL, product photo, SVG outline, or measured drawing.

See [Reference assets](/modeling/reference-assets).

## Raster image

An image stored as a grid of pixels, such as PNG, JPG, BMP, TIFF, screenshots, scans, and photos. Raster images are useful references, but they do not carry editable CAD curves or dimensions by themselves.

See [Reference assets](/modeling/reference-assets#raster-images-pixels-as-calibrated-references), [Pillow](https://pillow.readthedocs.io/), and [OpenCV](https://docs.opencv.org/).

## Rasterization

The process of converting vector or 3D information into pixels. Rasterization is useful for display and screenshots, but it discards CAD topology, parametric history, and exact curves unless those are preserved separately.

See [Reference assets](/modeling/reference-assets#raster-images-pixels-as-calibrated-references).

## Vectorization

The process of converting raster pixels into vector paths. In this workflow, vectorization usually means tracing a bitmap into SVG or DXF before importing it as 2D CAD reference geometry.

See [Potrace](https://potrace.sourceforge.net/) and [Inkscape Trace Bitmap](https://inkscape-manuals.readthedocs.io/en/latest/tracing-an-image.html).

## Thresholding

An image-processing step that separates pixels into groups, often foreground and background, so edges or regions can be extracted.

See [OpenCV](https://docs.opencv.org/) and [scikit-image measure](https://scikit-image.org/docs/stable/api/skimage.measure.html).

## Contour

A line following the boundary of a region or a constant-value boundary in an image. Contours are useful when converting a silhouette, scan, or drawing into vector-like geometry.

See [scikit-image contour finding](https://scikit-image.org/docs/stable/auto_examples/edges/plot_contours.html).

## Calibration

The act of tying image pixels, scan points, or imported geometry to real-world dimensions and orientation. Calibration can use known dimensions, a ruler in the image, datum features, or a documented transform.

See [Reference assets](/modeling/reference-assets#suggested-repository-pattern).

## Mesh

A surface represented by vertices, edges, and faces, usually triangles. STL, OBJ, PLY, and many GLB files are mesh formats.

See [trimesh](https://trimesh.org/) and [Reference assets](/modeling/reference-assets#stl-obj-ply-glb-and-scanned-meshes-triangle-references).

## Watertight mesh

A closed mesh with no holes, cracks, or boundary leaks. Volume and inside/outside tests are only reliable when the mesh is watertight enough for the calculation being performed.

See [trimesh](https://trimesh.org/).

## Point cloud

A set of unconnected 3D sample points, often produced by 3D scanning or photogrammetry. Point clouds usually need cleanup, registration, and surface reconstruction before they are useful as CAD references.

See [Open3D point clouds](https://www.open3d.org/docs/0.19.0/python_api/open3d.geometry.PointCloud.html).

## Registration

The process of aligning one geometry dataset to another, such as fitting a scan to a model coordinate system.

See [Open3D ICP registration](https://www.open3d.org/docs/0.19.0/tutorial/pipelines/icp_registration.html).

## ICP

Iterative Closest Point. A registration method that refines the alignment between two point clouds or sampled surfaces by repeatedly matching nearby points and updating the transform.

See [Open3D ICP registration](https://www.open3d.org/docs/0.19.0/tutorial/pipelines/icp_registration.html).

## Tolerance

The accepted difference between generated geometry and a reference target. In reference-asset tests, tolerance should be written down in the asset manifest so pass/fail behavior is reviewable.

See [Testing strategy](/modeling/testing) and [Reference assets](/modeling/reference-assets#automation-ideas).
