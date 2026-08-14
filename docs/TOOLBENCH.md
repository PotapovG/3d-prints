# Codex 3D Toolbench v0

## Goal

Validate that Codex plus local 3D applications can cover the project's modeling needs without hosting or paying for an additional generative 3D model.

## Selected baseline

### FreeCAD — primary engineering CAD

Candidate upstream: `blwfish/freecad-mcp`.

Reasons for trial:

- explicitly built for practical parametric CAD and manufacturing workflows;
- 32-tool surface is broad enough without exposing hundreds of narrowly scoped tools;
- supports model inspection, mesh work, export and diagnostics;
- developed and tested on macOS in addition to Windows/Linux;
- has unit and live FreeCAD integration tests;
- provides viewport/document inspection capabilities needed for an iterative agent loop.

Caveat: the MCP intentionally grants the agent access to FreeCAD's Python environment, including filesystem/OS capabilities. Treat untrusted FreeCAD documents as untrusted code and keep this MCP local.

Trial policy: pin a tested commit/release. Do not auto-upgrade the production toolchain.

### Blender — free-form / decorative / mesh tool

Candidate upstream: `webita/blender-codex-mcp`.

Reasons for trial:

- explicitly targets Codex;
- can inspect a live scene and objects;
- can capture viewport screenshots for visual feedback;
- can execute Blender Python incrementally;
- can export GLB and can be extended to STL/other normal Blender formats without introducing another AI provider.

Disable optional external AI/model integrations. Set telemetry disabled for the local/private setup.

Caveat: arbitrary Blender Python execution is powerful. Save the document before large changes and keep iteration small.

### OpenSCAD — simple parametric fast path

Do **not** adopt an upstream MCP yet.

Reason: several current OpenSCAD MCP projects bundle image-generation APIs, CUDA reconstruction or direct-printer features that are outside our architecture and duplicate responsibilities.

Phase-0 trial should use OpenSCAD as a deterministic code/CLI tool. If Codex needs a richer feedback loop, implement or adopt a deliberately tiny MCP exposing only semantic operations such as:

- validate source;
- render preview;
- export STL/3MF;
- query bounding box / basic mesh stats.

No secondary AI service belongs in this adapter.

### OrcaSlicer — slicing

Use official OrcaSlicer as the backend. Avoid depending on an experimental MCP-specific fork in the first production path.

Target adapter surface:

- inspect/load model;
- apply pinned printer/process/filament profiles;
- arrange/orient where supported;
- slice;
- return time/filament/support summary;
- render/export a reviewable plate/layer preview where practical;
- export reproducible 3MF and G-code.

The adapter must not expose arbitrary shell execution to the agent.

### Centauri Carbon 2 — printer

Candidate upstream: `bjan/pycentauri` with its MCP interface.

Use local LAN access. Keep read-only monitoring available broadly, but control behind explicit configuration and project safety gates.

Required capabilities for the final path:

- status and attributes;
- camera snapshots;
- CANVAS state;
- files and upload;
- print history;
- start/pause/resume/stop after policy checks.

Pin the tested version because the CC2 protocol is reverse engineered and firmware-sensitive.

## Why no separate 3D AI in v0

Codex is expected to perform the reasoning and geometry planning while applications provide deterministic geometry operations and visual feedback.

The core evaluation loop is:

`intent -> tool operation -> viewport/render -> Codex visual inspection -> revision -> dimensional/topology inspection -> export`

Only introduce a dedicated generative 3D model if this toolbench fails a real project requirement, especially complex organic sculpting, and document that gap with an acceptance test first.
