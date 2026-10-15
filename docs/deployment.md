# Deployment and operational scope

This is a documentation showcase. It does not include the private application, model checkpoints or Hikvision SDK distribution, and cannot be installed as a runnable system by cloning this repository.

## Current environment

| Layer | Implementation |
|---|---|
| Host | Windows workstation, Python 3.11 |
| Cameras | Hikvision devices using HCNetSDK / PlayM4 |
| Video decoding | SDK worker processes, CPU decoding |
| GPU inference | NVIDIA CUDA, TensorRT FP16; optional scene-object INT8 export |
| Face runtime | InsightFace `antelopev2`, ONNX Runtime CUDA |
| Application | Python camera threads, model batch queues, FastAPI / Uvicorn |
| Persistence | SQLite WAL for face and analytics stores; JSON configuration |
| Frontend | Inline HTML / CSS / JavaScript and SVG |

Engine artifacts are hardware / runtime dependent. A separately provisioned deployment needs compatible driver, CUDA, TensorRT and ONNX Runtime builds, the correct SDK distribution and the model assets. The development requirements file alone is not a complete environment lock.

## Configuration groups

- **Branches** — ID, name, note, floor-plan image or drawn geometry, named zones.
- **Cameras** — connection parameters, channel, branch, model switches, stream selection and floor calibration.
- **Models** — checkpoint / engine paths, image sizes, scheduling targets and class thresholds.
- **Identity** — face thresholds, enrollment / verification intervals, claim freshness and cross-branch window.
- **Clothing** — measurement rate, ROI quality, repeated-sighting rules and consistency thresholds.
- **Analytics** — debounce, stale-track grace, idle threshold and work-reset window.
- **Map trails** — sampling, write buffering and retention.

Credentials belong in private configuration. The owner-selected camera-management screenshot shows the original LAN addresses and status. The saved floor-plan editor is also shown; credentials and raw connection configuration are not distributed.

## Recovery mechanisms

The code implements isolated camera workers, staggered login, stream reconnection, dead-worker restart, explicit SDK shutdown, stale-track cleanup and downtime repair of open intervals. SQLite backups must account for WAL state; a live backup operation is preferable to copying only the main database file.

These mechanisms have development limitations. In particular, failed partial camera startup can retain decoder ports until the worker is restarted. Historical logs show this failure pattern, so it should be fixed before treating reconnection as unattended recovery.

## Current limitations to harden

- The application itself has no authentication / role layer. Shared use requires an appropriate controlled-access deployment boundary, and authorization should be integrated before exposing management operations broadly.
- Partial SDK-start failures require guaranteed resource cleanup.
- Person merges need validation of both identities and consistent historical-ID propagation across stores. Individual deletion also needs consistent cleanup.
- Daily reporting needs interval clipping at day boundaries for cross-midnight observations.
- The camera-add probe should run outside the HTTP event loop.
- User-controlled text requires consistent HTML escaping across all UI views.

These are known application edges rather than features claimed by this case study. The current operational database passed integrity checks; the identity-merge and midnight defects were confirmed in isolated regression checks.

## Planned work

| Item | Current status |
|---|---|
| Four-camera local workstation pipeline | Implemented; historical measurements available |
| Floor-plan drawing / calibration / zones / replay | Implemented; physical accuracy depends on calibration |
| Separate-host branch synchronization | Not implemented |
| GPU video decoding | Planned |
| Thirty-camera validation | Not completed |
| Expanded knife checkpoint | Not deployed; current custom weapon model is handgun-only |
| Second-stage weapon confirmation | Planned |
| Application-native authentication | Not implemented |
