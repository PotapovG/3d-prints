# 3d-prints

Agent-assisted 3D design and printing workspace built around Codex and deterministic 3D tools.

## North Star

A user should be able to describe a part, provide photos or later a 3D scan, and have Codex:

1. choose the appropriate modeling tool;
2. create and iteratively inspect the model;
3. validate it for printing;
4. slice it for the ELEGOO Centauri Carbon 2 Combo;
5. present a print plan for human approval;
6. upload and start only the exact approved artifact;
7. monitor the print through printer telemetry and camera images;
8. record the result and feed real-world measurements back into the next revision.

Codex is the only AI required by the core architecture. 3D applications are tools, not additional model providers.

## Baseline toolbench

- **FreeCAD MCP** — primary tool for dimensionally accurate and constraint-based CAD.
- **OpenSCAD / thin adapter** — fast path for simple parametric printable geometry. No external AI dependency.
- **Blender Codex MCP** — free-form and decorative geometry plus viewport screenshot feedback.
- **OrcaSlicer** — slicing backend; initially through CLI/thin adapter rather than an experimental fork.
- **pycentauri MCP** — local control and telemetry for Centauri Carbon 2 + CANVAS + camera.
- **Git** — source of truth for project specs, editable sources, profiles, manifests and print feedback.

## Safety invariant

Codex may model, inspect, validate, slice and upload autonomously. Starting a physical print requires explicit human approval bound to the exact print artifact checksum. Any modification after approval invalidates that approval.

See `AGENTS.md` and `docs/` before changing the workflow.
