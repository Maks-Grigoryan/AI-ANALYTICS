# Compute efficiency

The optimization work covers the full path from camera capture to operator tools.

| Area | Engineering work |
|---|---|
| GPU resources | Shared model instances and coordinated inference requests |
| Analysis workload | Separate execution budgets and camera-specific feature selection |
| CPU overhead | Efficient frame movement and focused image operations |
| Memory | Defined frame-buffer limits and controlled temporary allocations |
| Live viewing | Display encoding follows active viewers |
| Storage | Compact state history, buffered position recording and retention controls |
| Continuity | Tracking and state memory between heavier analysis updates |
| Recovery | Independently monitored camera connections and controlled reconnection |

These decisions make resource use an explicit part of the system design. They also provide practical controls for balancing camera coverage, enabled analytics and available compute.
