# Roadmap

## Phase 0 — Codex 3D toolbench

Goal: prove that Codex plus deterministic 3D applications covers the required modeling workflows without another AI model.

1. Install and pin FreeCAD + selected FreeCAD MCP.
2. Install OpenSCAD and validate direct CLI/code workflow.
3. Install and pin Blender + Codex-focused Blender MCP with optional external AI integrations disabled.
4. Run AT-001, AT-002 and AT-003 from `ACCEPTANCE_TESTS.md`.
5. Record actual tool friction, failures and context/tooling cost.
6. Decide which adapters, if any, are justified by measured gaps.

Exit: engineering and parametric paths are usable from Codex; the organic path has a measured capability boundary.

## Phase 1 — first physical plastic

Goal: complete one reproducible, approved print on Centauri Carbon 2.

1. Pin official OrcaSlicer and CC2 profiles.
2. Build the thinnest possible slicer interface around the official backend.
3. Pin and configure pycentauri locally in read-only mode first.
4. Validate status, camera, CANVAS and file upload.
5. Implement print manifest and checksum-bound human approval.
6. Enable printer control only after read-only preflight passes.
7. Execute an engineering calibration object end to end.
8. Record physical result, photos and measurements in Git.

Exit: `prompt -> editable CAD -> preview -> STL/STEP -> slice -> review -> human approval -> upload/start -> monitor -> result` works without manual file shuffling.

## Phase 1.25 — multi-slicer routing and competitive slicing

Goal: choose the best slicer and slicing strategy for each job instead of permanently binding the pipeline to one backend.

The LLM/Codex provides intent and may explain the decision, but slicer selection itself must be deterministic, reproducible and policy-driven.

1. Separate `slice` from `send-to-printer`; printer transport/control must not determine which slicer is used.
2. Implement a `Slicer Router` with hard capability constraints first, then weighted scoring.
3. Track versioned slicer capabilities and printer/profile compatibility rather than relying on model memory.
4. Provide initial adapters for:
   - ElegooSlicer;
   - OrcaSlicer;
   - PrusaSlicer;
   - CuraEngine.
5. Score candidates using at least:
   - printer/profile compatibility;
   - multicolor/CANVAS compatibility;
   - headless/automation reliability;
   - expected surface quality;
   - support strategy;
   - dimensional accuracy;
   - speed;
   - filament consumption;
   - purge/waste efficiency;
   - profile confidence.
6. Add competitive slicing: when top candidates are sufficiently close, slice the same job with multiple backends instead of choosing from policy alone.
7. Build a result analyzer that normalizes slicer outputs into comparable metrics, including estimated time, model/support/purge filament, color/tool changes and other available G-code statistics.
8. Keep slicer versions, profiles, router policy and scoring weights version-controlled so every decision can be reproduced.
9. Use the first multicolor benchmark scenario to validate the router on the real CC2 Combo:
   - four identical 4-color Minions in one job;
   - compare four standing on their feet versus four lying on their backs;
   - compare at least ElegooSlicer and OrcaSlicer;
   - record time, total filament, purge/waste, support material and color changes;
   - select the winner from declared priorities plus measured slice results, not from a hard-coded preferred slicer.
10. Persist the chosen strategy and all candidate summaries in the print manifest for later physical-result correlation.

Exit: for a supported job, Codex can request a slicing strategy without naming a slicer; the router selects or competitively evaluates backends, explains the measurable trade-off, and produces a reproducible approved artifact for the existing Phase-1 print pipeline.

## Phase 1.5 — feedback and observability

- spool inventory and estimated remaining material;
- print telemetry capture;
- camera sampling and visual anomaly analysis;
- first-layer/spaghetti/detachment detection as advisory signals;
- structured print-result feedback;
- empirical dimensional clearances for this exact printer/material/profile combination;
- correlate physical print outcomes with slicer, slicer version, profile, orientation and router decision;
- use accumulated local evidence to tune router weights/policies, while keeping policy changes explicit and version-controlled.

Autonomous control remains conservative; monitoring recommends pause/stop until sufficient local evidence exists.

## Phase 2 — photo and scan workflows

- photos as engineering references with known-scale measurements;
- 3D scans as first-class references;
- mesh cleanup/alignment;
- reverse engineering into editable CAD where useful;
- scan-to-model comparison and revision workflows.

## Phase 3 — catalog and convenience UI

Only after the workflow is stable:

- browsable model/print catalog;
- searchable project history;
- renders and print photos;
- material/time/cost analytics;
- convenient submission/review UI if ChatGPT/Codex alone becomes limiting.

## Non-goals until proven necessary

- hosting a separate LLM or 3D generative model;
- building a large orchestration service before the direct Codex toolbench is tested;
- replacing upstream 3D application capabilities with custom code;
- autonomous print start without human artifact approval.
