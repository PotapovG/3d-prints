# Toolbench Acceptance Tests

These tests decide whether Codex + deterministic 3D tools are sufficient before we build additional middleware or introduce another AI model.

## Common rules

For every test Codex must:

1. preserve editable source;
2. work incrementally rather than generate one giant opaque script;
3. obtain at least one visual preview from the 3D tool;
4. inspect that preview and perform at least one explicit geometry/dimension check;
5. export a printable STL and, where appropriate, STEP/FCStd/SCAD/BLEND source;
6. record what tool and version produced the output;
7. not send anything to the physical printer during Phase 0.

A test fails if manual mouse editing is required to complete the specified geometry.

---

## AT-001 — FreeCAD engineering part

### Intent

Validate dimensionally accurate mechanical CAD and visual feedback.

### Prompt

Create an L-shaped mounting bracket with these nominal dimensions:

- base: 80 x 40 x 5 mm;
- vertical leg: 40 mm high, 5 mm thick;
- two 5 mm through-holes in the base;
- hole centers 20 mm from each end along the 80 mm axis and centered across the 40 mm width;
- external corner fillets: 3 mm where geometrically valid.

### Required outputs

- editable FreeCAD document;
- STEP;
- STL;
- viewport screenshot/render;
- measured bounding box and hole diameter/spacing report.

### Pass criteria

- bounding dimensions within CAD numerical precision of specification;
- both holes present and through the correct face;
- model is a valid solid;
- no unexpected extra bodies/objects in export;
- Codex can revise one parameter (base length 80 -> 90 mm) without rebuilding manually.

---

## AT-002 — OpenSCAD parametric fast path

### Intent

Validate source-controlled procedural CAD without a dedicated MCP or extra AI.

### Prompt

Create a stackable organizer tray:

- outer size: 120 x 80 x 30 mm;
- wall: 2 mm;
- bottom: 2.4 mm;
- 3 equal compartments along the 120 mm direction;
- external corner radius approximately 4 mm;
- expose dimensions, wall, bottom and compartment count as top-level parameters.

### Required outputs

- `.scad` source;
- rendered preview image;
- STL;
- parameter list and bounding-box report.

### Pass criteria

- changing `compartments` from 3 to 4 produces a valid model without rewriting geometry logic;
- STL is manifold/printable by basic mesh validation;
- preview is sufficient for Codex to detect obvious geometry mistakes;
- no external model/API is required.

---

## AT-003 — Blender free-form object

### Intent

Measure how far Codex + Blender can go without a generative 3D model.

### Prompt

Create a simple stylized pig astronaut suitable for FDM printing:

- approximately 100 mm total height;
- large rounded head and compact body;
- recognizable pig snout and ears;
- simple backpack and helmet ring;
- arms and legs attached to the body with no floating geometry;
- deliberately toy-like, not photorealistic;
- avoid thin details below roughly 1.5 mm.

### Required outputs

- editable `.blend`;
- at least two viewport screenshots from different angles;
- STL;
- mesh statistics and bounding box.

### Mandatory iteration

After the first screenshot Codex must critique the result itself and make at least one visible revision before final export.

### Pass criteria

- object is clearly recognizable from screenshots;
- exported printable geometry has no detached intended components;
- target height is within 5 mm;
- no critical feature is obviously below the requested minimum thickness;
- result is good enough for a simple toy/decorative print without a second AI model.

### Interpretation

Failure of AT-003 does **not** block engineering-print MVP. It records the capability boundary that may justify a later optional generative-3D provider.

---

## Phase-0 exit decision

- AT-001 pass: adopt FreeCAD MCP for engineering path.
- AT-002 pass: keep OpenSCAD as lightweight fast path; only add a tiny MCP if preview/inspection ergonomics are inadequate.
- AT-003 pass: no dedicated 3D generation model required for current scope.
- AT-003 fail: continue with engineering MVP and record the exact organic-modeling gap before evaluating any additional model/provider.
