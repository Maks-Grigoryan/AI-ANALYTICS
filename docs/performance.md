# Actual measurements and verification

## Capture environment

The earlier verification session on 15 October 2026 used the configured Hikvision devices, original GPU models, real saved map and private working copies of the operational databases. Three streams connected out of four configured cameras during this measurement window. The interface gallery now uses the owner-selected original screenshots; its later Live capture shows all four camera indicators online.

The workstation uses Intel Core Ultra 9 285K and NVIDIA RTX 5080. Twenty samples were taken over 21.0 seconds while real operator pages were being captured.

| Counter | Mean |
|---|---|
| Application + camera-worker CPU | 3.089 core equivalents |
| System GPU utilization | 24.95% |
| System GPU memory | 11563.70 MiB |

The CPU counter is based on process CPU-time deltas. GPU utilization and memory are system-wide counters and include other active GPU processes, including browser rendering. These are observations during the capture workload, not an isolated neural-inference benchmark. They do not establish thirty-camera capacity.

The unaltered numeric samples are in [runtime-sample.csv](runtime-sample.csv). Original screenshot hashes, earlier measurement context, map counts and training-artifact hashes are in [capture-provenance.json](capture-provenance.json).

## Model validation artifacts

The deployed handgun checkpoint matches the training run's best checkpoint byte-for-byte. Its recorded validation metrics at epoch 100 are precision 0.89790, recall 0.77831, mAP50 0.84494 and mAP50–95 0.60474.

The repository includes the original files saved by that run:

- [Training results](weapon-results.png)
- [Precision–recall curve](weapon-pr.png)
- [Normalized confusion matrix](weapon-confusion.png)
- [Original results CSV](weapon-metrics.csv)

The plots were copied from the training output, not redrawn. These detector validation results do not measure face recognition, activity-label accuracy or map position error. No separate test-set score is claimed.

## What was checked

- The real Floor Map, People Database, Activity, Cameras and Branches pages were loaded over HTTP from the running application and captured without fixture APIs or template reconstruction. Their JavaScript page-error count was zero.
- The five published interface images match the owner-supplied screenshot originals byte-for-byte, including the actual camera overlays and stored data.
- The saved map used two existing rooms and two zones; the calibration state was not altered for presentation.
- The Activity report date was selected through the actual day picker; its totals were not replaced.
- Original model plots and CSV files are verified by SHA-256 against the training artifacts.
- The operational stores previously passed SQLite integrity checks, and 41 active Python files passed syntax compilation. Capture writes were confined to private working database copies.

## Scaling scope

The measured verification workload had three connected streams. The owner-selected later Live screenshot shows four camera indicators online; it is not a new four-stream performance measurement. Thirty-camera operation, GPU decoding and distributed branch synchronization remain future work and have not been presented as measured results here.
