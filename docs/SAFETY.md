# Physical and Tool Safety

## Trust boundaries

This project connects a coding agent to applications that can execute Python and eventually to a physical 3D printer. Treat those as different risk classes.

### Class A — read/inspect

Safe by default:

- inspect Git state and project files;
- inspect CAD/scene structure;
- capture viewport images;
- read printer status, CANVAS state, camera, history and file lists;
- inspect slicer reports and generated artifacts.

### Class B — local reversible edits

Allowed without per-operation approval when working inside the project workspace:

- create/edit CAD and mesh source;
- run local geometry operations;
- render previews;
- slice and export artifacts;
- upload a file to the printer if the upload does not start printing.

Use version control and save application documents before destructive batches.

### Class C — physical/control effects

Require explicit human approval or an explicitly defined later policy:

- start a print;
- resume a print intentionally paused by a person/safety workflow;
- live heater changes;
- arbitrary G-code;
- stop/cancel a print;
- any operation whose failure could cause a physical collision or heating behavior.

Initially, print monitoring may recommend pause/stop but must not autonomously stop a job.

## Approval binding

Human approval is approval of an exact artifact, not approval of a project name.

Before `APPROVED`, create a manifest that binds at minimum:

- model/source revision;
- STL/3MF hash;
- G-code hash;
- OrcaSlicer version;
- printer profile version;
- process profile version;
- filament/material mapping;
- target printer identity.

Any change invalidates approval and returns the job to `READY_FOR_REVIEW`.

## Untrusted 3D files

FreeCAD and Blender MCPs may expose Python execution and rich document behavior. Do not assume an arbitrary downloaded project file is inert.

For third-party models:

1. prefer mesh/STEP/STL/3MF imports over executing macros/scripts;
2. do not run bundled Python/macros from an untrusted source;
3. inspect the file in a constrained workflow before allowing broad agent automation;
4. keep external assets separate from trusted project source until reviewed.

## Network boundaries

Printer control should remain LAN-local where possible. Do not expose raw pycentauri or application socket bridges directly to the public internet.

Printer access codes and other secrets belong in local environment/configuration, never Git.

## Version pinning

Pin the tested versions/commits of:

- FreeCAD and its MCP bridge;
- Blender and its MCP bridge;
- OrcaSlicer;
- pycentauri;
- printer firmware compatibility notes.

Upgrade through a validation print path, not silently during a production job.
