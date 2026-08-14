# Codex Handoff — Phase 0 3D Toolbench

## Goal

Build and validate the local 3D toolbench for this repository without introducing any additional AI/model provider.

Read first:

- `AGENTS.md`
- `docs/TOOLBENCH.md`
- `docs/ACCEPTANCE_TESTS.md`
- `docs/SAFETY.md`
- `docs/ROADMAP.md`

Do not implement OrcaSlicer or printer control yet. Phase 0 ends after the three modeling acceptance tests and a written evaluation.

## Operating constraints

1. Codex is the only reasoning/model layer.
2. Do not configure Meshy, Hunyuan, Rodin, Gemini, Venice or any other generative 3D/image service.
3. Do not enable optional external AI integrations in Blender MCPs.
4. Do not install an OpenSCAD MCP that pulls secondary AI/CUDA reconstruction into the stack.
5. Prefer upstream tools and thin configuration over custom middleware.
6. Do not expose local FreeCAD/Blender socket bridges to non-loopback interfaces.
7. Do not commit credentials, absolute private paths or local MCP secrets.
8. Do not interact with the physical printer in this phase.

## Step 1 — environment inventory

Record in `docs/PHASE0_RESULTS.md`:

- OS and architecture;
- Codex runtime/app version if available;
- Python version;
- `uv` availability/version;
- FreeCAD installed version/path or `missing`;
- Blender installed version/path or `missing`;
- OpenSCAD installed version/path or `missing`.

Do not assume package-manager paths; discover them.

## Step 2 — FreeCAD MCP

Candidate: `https://github.com/blwfish/freecad-mcp`.

Before installation, read the candidate's current `AGENT-INSTALL.md`, `SECURITY.md` and tool instructions. Use its stable installation branch/release, not its active development branch, unless a documented blocker requires otherwise.

Install/configure it for the current Codex MCP runtime using standard stdio MCP registration. FreeCAD should listen only through the local bridge described by the upstream project.

Verify at minimum:

- MCP server appears in Codex;
- FreeCAD bridge health/connection check passes;
- a document can be created;
- scene/document objects can be inspected;
- a viewport screenshot can be captured in GUI mode.

Record the exact upstream commit SHA used. Do not upgrade during the acceptance test run.

## Step 3 — OpenSCAD deterministic path

Install/use official OpenSCAD locally.

For Phase 0, do **not** add a third-party OpenSCAD MCP unless direct source + CLI makes AT-002 materially impractical.

Codex may create `.scad` source directly and use OpenSCAD CLI for:

- syntax/render validation;
- preview image rendering;
- STL export.

If basic mesh/bounding-box inspection is needed, use a small deterministic local utility already available in the environment or add the smallest dependency necessary. Do not add another AI service.

## Step 4 — Blender Codex MCP

Candidate: `https://github.com/webita/blender-codex-mcp`.

Read its current README/security notes before installation. Configure only the local socket bridge and core Codex tools.

Requirements:

- bind Blender bridge to localhost only;
- set telemetry disabled if supported;
- do not configure Poly Haven/Sketchfab unless needed for a later non-AI asset test;
- do not configure Rodin/Hunyuan/other AI integrations;
- save the `.blend` before large scripted changes.

Verify at minimum:

- health check;
- scene inspection;
- object inspection;
- viewport screenshot;
- incremental Blender Python execution;
- local file export.

Record exact upstream commit SHA used.

## Step 5 — run acceptance tests

Run in this order:

1. `AT-001 — FreeCAD engineering part`
2. `AT-002 — OpenSCAD parametric fast path`
3. `AT-003 — Blender free-form object`

Create each test under:

```text
projects/acceptance/
  at-001-freecad-bracket/
  at-002-openscad-organizer/
  at-003-blender-pig-astronaut/
```

For each project preserve:

- `brief.md` with the exact test prompt;
- editable source (`FCStd`, `scad`, `blend`, scripts where relevant);
- exported geometry;
- preview screenshots/renders;
- `result.md` containing pass/fail for every criterion, actual dimensions/statistics, tool versions, tool friction and any manual intervention.

No criterion may be silently waived. If a tool fails, diagnose it and record the failure rather than switching to an external AI model.

## Step 6 — evaluate, do not overbuild

After all three tests, create/update `docs/PHASE0_RESULTS.md` with:

- PASS/FAIL per test;
- setup complexity;
- reliability of viewport feedback;
- amount of manual GUI interaction required;
- whether Codex could revise models parametrically;
- tool-call/context friction;
- security/operational concerns encountered;
- exact upstream revisions tested.

Then make only these decisions:

1. FreeCAD MCP: adopt / replace / needs thin wrapper.
2. OpenSCAD: direct CLI sufficient / tiny MCP justified.
3. Blender MCP: sufficient for simple decorative work / capability gap documented.
4. Additional generative 3D AI: **not justified unless AT-003 exposes a concrete required gap**.

Do not start Phase 1 automatically. Stop after committing Phase-0 results so the user can review the toolbench evidence before printer integration begins.
