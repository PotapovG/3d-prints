# AGENTS.md

## Mission

Use Codex as the orchestration and reasoning layer for 3D modeling and printing. Prefer deterministic local tools over secondary AI/model services.

## Tool routing

Choose the simplest tool that preserves the required editability and precision.

- Use **OpenSCAD** for simple parametric geometry that is naturally represented as source code: boxes, spacers, bushings, adapters, organizers, hole patterns and simple mounts.
- Use **FreeCAD** for dimensionally accurate mechanical parts, sketches and constraints, assemblies, imported STEP/STL references, reverse-engineering work and geometry that benefits from a persistent CAD document.
- Use **Blender** for organic, decorative, free-form or mesh-heavy work.
- Do not introduce a separate text-to-3D/image-to-3D AI provider unless an acceptance test demonstrates a capability gap that cannot reasonably be solved with the current toolbench.

## Iteration rule

Never treat successful tool execution as proof that a model is correct.

For every non-trivial model:

1. inspect document/scene structure;
2. render or capture a viewport preview;
3. visually inspect the result;
4. inspect key dimensions or topology;
5. correct defects before export;
6. preserve editable source alongside exported manufacturing geometry.

## Print workflow

The intended state machine is:

`DRAFT -> MODELED -> VALIDATED -> SLICED -> READY_FOR_REVIEW -> APPROVED -> UPLOADED -> PRINTING -> COMPLETED -> REVIEWED`

The `APPROVED` transition is human-only.

### Approval invariant

Approval is bound to a manifest containing hashes/versions for at least:

- source/model revision;
- exported mesh/3MF;
- generated G-code;
- slicer version;
- printer profile;
- process profile;
- filament mapping.

Changing any approval-bound value invalidates approval.

## Physical safety

Without explicit human approval for the exact artifact, do not:

- start a print;
- resume an intentionally paused print;
- change nozzle/bed/chamber temperatures during a live print;
- issue arbitrary printer G-code;
- delete the currently printing file.

Reading status, CANVAS state, camera snapshots, file lists and history is allowed by default. Upload may be automated, but upload is not permission to print.

For monitoring, initially recommend pause/stop rather than autonomously stopping a print. Autonomous pause may be added later only after false-positive behavior has been measured.

## Source of truth

Git is the durable memory of this project. Chats are not.

Each printable project should eventually preserve:

- brief/specification;
- references and known measurements;
- editable CAD/source;
- exported mesh/STEP;
- slicer project or reproducible slicer inputs;
- print manifest;
- print result and photos;
- measurements/failure notes from the physical object.

Do not commit secrets, printer access codes, API tokens or local absolute paths that contain private user information.

## Change discipline

Keep wrappers thin. Before writing a new MCP or service, prove that an upstream tool or a small CLI adapter cannot satisfy the requirement.

Prefer semantic tools over arbitrary shell execution for slicing and printer control.
