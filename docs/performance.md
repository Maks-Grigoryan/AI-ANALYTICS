# Performance and verification

## Recorded environment

Historical development measurements were recorded with **four Hikvision cameras**, an **Intel Core Ultra 9 285K** and **one NVIDIA RTX 5080**. They were taken under varying visible-person counts, and should not be treated as a reproducible benchmark suite or a service-level guarantee.

| Measurement | Recorded value | Interpretation |
|---|---|---|
| CPU | 3.8–4.5 core equivalents | Aggregate application load across the measured four-camera setup |
| GPU utilization | 21–30% | Observed during the documented workload |
| VRAM | Approximately 11.6 GB | Shared models / runtime contexts and buffers; not a per-camera allocation |
| SDK decode | Approximately 0.45 core per camera | A significant scaling cost in the current CPU-decoding path |
| SCRFD face detection | Approximately 5.9 ms | Head-crop detection measurement |
| ArcFace, one face | Approximately 9.8 ms | Single-face embedding extraction |
| ArcFace, eight faces | Approximately 14.4 ms total | Approximately 1.8 ms per face in that measured batch |
| Gallery matching | Approximately 0.35 ms | Measured with 6,837 gallery vectors |
| SQLite commit | Approximately 2.14 → 0.01 ms | Historical before / after WAL configuration measurement |

The configured detector targets are pose 6 Hz, scene objects 1 Hz and handgun detection 6 Hz. A target is a scheduling setting, not a guaranteed achieved rate. Display FPS, detector Hz, per-person face attempts and inference latency are different measurements and should not be combined into a single FPS claim.

## Why the design reduces overhead

- Model instances are shared across cameras instead of being duplicated per camera.
- Greedy request batching amortizes eligible inference launches without a fixed wait window.
- Rectangular inference inputs avoid processing large amounts of letterbox padding.
- Optical flow supplies intermediate skeleton positions between pose detections.
- Scene objects run less frequently than rapidly changing pose / weapon evidence.
- Gallery matching uses a NumPy matrix and cached named-owner masks.
- Clothing masks are limited to person ROIs rather than the full image.
- Stable changes are recorded as intervals; SQLite uses WAL for frequent short writes.

## Handgun validation

The local training run records precision 0.89790, recall 0.77831, mAP50 0.84494 and mAP50–95 0.60474 at epoch 100. The deployed checkpoint matches that run's best checkpoint by SHA-256. See [models](models.md) for provenance and the numeric series.

These are detector validation scores. They do not measure face recognition, activity-label accuracy, floor-plan position error or end-to-end event detection. The configured test split exists, but no separate test-set score is published here.

## Verification performed for this showcase

| Check | Result |
|---|---|
| Syntax compilation of 41 active Python files | Passed |
| SQLite `PRAGMA quick_check` on both operational stores, read-only | `ok` |
| Floor-map geometry / zone regression script | Passed |
| Identity-ownership regression script | Passed |
| Clothing ROI equivalence | 600 comparisons; all identical |
| Cross-branch diagnostic script | Expected printed behaviour; not a comprehensive assertion-based suite |
| Original UI rendered with synthetic data | Six pages captured; no JavaScript page errors or unhandled fixture API paths |

The database-dependent regression scripts were isolated from operational stores. The synthetic examples were generated from original page templates. The Live gallery was subsequently replaced with real camera captures: the actual SDK workers and GPU models ran against private copies of the data stores, three camera streams produced frames, and a two-minute window was sampled to select populated views. The original operational databases were not modified by that capture session. This was a frame-selection session, not a new speed benchmark or accuracy evaluation.

## Scaling boundary

Four cameras are the documented workload. Thirty cameras are an intended expansion, not a completed validation. CPU video decoding, per-person tracking / persistence work, TensorRT batch limits and single-process identity ownership all matter.

The proposed expansion path includes GPU decoding, re-exported engine profiles, per-camera model profiles and a process per GPU. Cross-process ownership and synchronization need an explicit design before the current identity rules can be preserved at larger scale.
