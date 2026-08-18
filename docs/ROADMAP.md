# Roadmap

## Product contract — target user experience

The project is not an interactive CAD assistant. The target is an unattended Codex-operated 3D production pipeline.

The user provides:

- a goal;
- reference images/files where useful;
- constraints and priorities;
- a Definition of Done.

Codex is responsible for the rest:

`goal + refs + DoD -> plan -> choose tools -> model/edit -> render -> inspect -> revise -> validate -> choose slicing strategy -> slice -> inspect -> produce print-ready artifacts`

The normal workflow must not require the user to operate Blender, FreeCAD, OpenSCAD or a slicer, or to approve intermediate modeling iterations. Codex must critique and revise its own work until the DoD is satisfied or a genuine hard blocker is reached.

The default human gate is the final physical action: review the finished artifact/summary and approve or reject printing. Autonomous print start remains out of scope until explicitly enabled later.

## Phase 0 — Codex 3D toolbench

Goal: prove that Codex plus deterministic 3D applications covers the required modeling workflows without another AI model.

1. Install and pin FreeCAD + selected FreeCAD MCP.
2. Install OpenSCAD and validate direct CLI/code workflow.
3. Install and pin Blender + Codex-focused Blender MCP with optional external AI integrations disabled.
4. Run AT-001, AT-002 and AT-003 from `ACCEPTANCE_TESTS.md`.
5. Record actual tool friction, failures and context/tooling cost.
6. Decide which adapters, if any, are justified by measured gaps.

Exit: engineering and parametric paths are usable from Codex; the organic path has a measured capability boundary.

Phase 0 is a technical preflight, not the product MVP. Passing individual tool tests does not prove unattended execution.

## Phase 0.5 — autonomous job runner

Goal: turn individual 3D tools into a resumable, zero-touch execution loop driven by a goal and DoD.

1. Define a versioned job manifest containing:
   - goal and DoD;
   - references and their provenance;
   - printer/material/color constraints;
   - quality/time/waste priorities;
   - permitted tools and versions;
   - artifact/checkpoint locations;
   - current state, attempts and failure reasons.
2. Implement explicit stages:
   - intake/plan;
   - model or edit;
   - render/preview;
   - visual self-inspection;
   - geometry/printability validation;
   - revision loop;
   - slicing strategy selection;
   - slicing;
   - slice/G-code validation;
   - final artifact packaging.
3. Make each stage idempotent or safely resumable so a long Codex run can continue after context compaction, process restart or tool failure without rebuilding successful work from memory.
4. Require persistent checkpoints in Git/workspace rather than relying on conversation context as state.
5. Add bounded autonomous retry/revision loops. A failed render, bad topology, thin feature, poor likeness or bad slicing result should normally cause another iteration, not a question to the user.
6. Define hard blockers that are allowed to stop the job, such as missing essential source data, unsupported file corruption, unavailable required tooling or safety/approval boundaries.
7. Produce a compact final report with:
   - DoD pass/fail evidence;
   - final previews;
   - validation results;
   - chosen tools/versions;
   - slicing metrics;
   - artifact checksums;
   - unresolved limitations, if any.
8. Add execution telemetry: stage duration, attempts, tool failures, token/context cost and reasons for retries.

Exit: Codex can accept a bounded 3D goal, work unattended through multiple tool/revision cycles, resume from persisted state, and return either a DoD-complete artifact set or a concrete hard-blocker report without requiring intermediate user operation.

## Phase 1 — first physical plastic

Goal: complete one reproducible, approved print on Centauri Carbon 2 through the autonomous job runner.

1. Pin an initial known-good slicer/backend and CC2 profiles for bootstrap.
2. Build the thinnest possible slicer interface around that backend.
3. Pin and configure pycentauri locally in read-only mode first.
4. Validate status, camera, CANVAS and file upload.
5. Implement print manifest and checksum-bound human approval.
6. Enable printer control only after read-only preflight passes.
7. Execute an engineering calibration object end to end through the autonomous runner.
8. Record physical result, photos and measurements in Git.

Exit: `goal -> autonomous build/validation -> slice -> final review -> human approval -> upload/start -> monitor -> result` works without manual file shuffling or manual CAD/slicer operation.

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
9. Use a multicolor benchmark on the real CC2 Combo:
   - four identical 4-color Minions in one job;
   - compare four standing on their feet versus four lying on their backs;
   - compare at least ElegooSlicer and OrcaSlicer;
   - record time, total filament, purge/waste, support material and color changes;
   - select the winner from declared priorities plus measured slice results, not from a hard-coded preferred slicer.
10. Persist the chosen strategy and all candidate summaries in the print manifest for later physical-result correlation.

Exit: for a supported job, Codex can request a slicing strategy without naming a slicer; the router selects or competitively evaluates backends, explains the measurable trade-off, and produces a reproducible artifact for the Phase-1 print pipeline.

## Phase 1.4 — zero-touch reference-to-print MVP

Goal: prove the actual product experience on a non-trivial organic, multicolor job: the user supplies a goal, references and DoD, then leaves Codex to finish the job without intermediate manual work.

Primary acceptance scenario: **printable Minion in a thong/string underwear** for Centauri Carbon 2 Combo.

1. User supplies reference images plus a concise goal/DoD; no base 3D model is assumed.
2. Codex decides autonomously whether the best route is:
   - build the character from references;
   - locate/import a legally usable base asset and modify it;
   - combine existing geometry with newly modeled clothing/details.
3. If an external base asset is used, record its source/license/provenance in the job manifest.
4. Codex chooses the modeling tool/path itself; the user must not need to operate Blender or another CAD tool.
5. Codex renders the result from multiple useful views, inspects those renders against the references/DoD and performs visible revisions when necessary.
6. Codex validates at least:
   - recognizability against the intended character/reference;
   - watertight/manifold printable geometry where required;
   - disconnected/floating parts;
   - minimum feature/wall thickness;
   - target dimensions/scale;
   - overhang/support implications;
   - bed contact/stability;
   - multicolor region integrity.
7. Codex may revise geometry after slicing if slice preview or G-code analysis exposes problems. Slicing is not a one-way terminal stage.
8. Use the Slicer Router to choose/compare orientation, support and slicer strategies according to declared priorities.
9. Final package must include at least:
   - editable source (`.blend` or equivalent);
   - printable STL/3MF as appropriate;
   - final multi-angle previews;
   - chosen slicer/profile/orientation;
   - slice preview or equivalent evidence;
   - estimated time/material/support/purge;
   - print manifest and checksums;
   - concise DoD evidence report.
10. The only normal user interaction after submission is final `approve print` / `reject` (or review of a genuine hard blocker).

MVP pass condition: the user can submit the Minion goal and references, leave the system unattended, and later return to a genuinely print-ready artifact set without having manually modeled, positioned, repaired or sliced anything and without having approved intermediate iterations.

## Phase 1.5 — feedback and observability

- spool inventory and estimated remaining material;
- print telemetry capture;
- camera sampling and visual anomaly analysis;
- first-layer/spaghetti/detachment detection as advisory signals;
- structured print-result feedback;
- empirical dimensional clearances for this exact printer/material/profile combination;
- correlate physical print outcomes with modeling path, slicer, slicer version, profile, orientation and router decision;
- use accumulated local evidence to tune router weights/policies, while keeping policy changes explicit and version-controlled;
- use physical-result feedback to improve future modeling/validation heuristics, not only slicer selection.

Autonomous control remains conservative; monitoring recommends pause/stop until sufficient local evidence exists.

## Phase 2 — photo and scan workflows

- photos as engineering references with known-scale measurements;
- 3D scans as first-class references;
- mesh cleanup/alignment;
- reverse engineering into editable CAD where useful;
- scan-to-model comparison and revision workflows;
- allow the autonomous runner to choose between photo-only reconstruction, scan-assisted reconstruction and existing-model modification.

## Phase 3 — catalog and convenience UI

Only after the zero-touch workflow is stable:

- browsable model/print catalog;
- searchable project history;
- renders and print photos;
- material/time/cost analytics;
- convenient submission/review UI if ChatGPT/Codex alone becomes limiting.

## Non-goals until proven necessary

- requiring the user to learn or manually operate Blender/FreeCAD/OpenSCAD/slicers as part of the normal workflow;
- hosting a separate LLM or 3D generative model before measured evidence shows Codex + deterministic tools cannot meet the organic-modeling DoD;
- replacing upstream 3D application capabilities with custom code where adapters are sufficient;
- building a large orchestration platform before the autonomous runner demonstrates concrete gaps;
- autonomous print start without human artifact approval.
