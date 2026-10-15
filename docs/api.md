# HTTP API surface

The private application defines 53 HTTP routes. This reference records the implemented route surface; it is not a hosted API service or an authentication guarantee. Camera event ingestion and management operations run on the same application.

| Method | Path | Purpose |
|---|---|---|
| `GET` | `/stream/{cam_id}` | Annotated multipart JPEG camera stream |
| `GET` | `/` | Branch overview page |
| `GET` | `/live` | Live annotated camera workspace |
| `GET` | `/dashboard` | Camera configuration workspace |
| `GET` | `/faces` | Branch-scoped face cards |
| `GET` | `/database` | Global people database |
| `GET` | `/activity` | Daily and period activity reporting workspace |
| `GET` | `/map` | Floor-plan workspace |
| `GET` | `/api/map` | Floor-plan, zones and calibration metadata |
| `GET` | `/api/map/live` | Fresh projected people positions |
| `GET` | `/api/map/replay` | Position / zone history for day replay |
| `GET` | `/floorplans/{name}` | Stored floor-plan image |
| `GET` | `/snapshot/{cam_id}` | Latest camera snapshot |
| `POST` | `/api/map/floorplan` | Upload a floor-plan image |
| `DELETE` | `/api/map/floorplan` | Remove a floor plan and related map configuration |
| `POST` | `/api/map/plan` | Save drawn floor geometry |
| `DELETE` | `/api/map/plan` | Clear drawn geometry |
| `POST` | `/api/map/align/{cam_id}` | Fit camera alignment to plan geometry |
| `POST` | `/api/map/zones` | Save named polygon zones |
| `POST` | `/api/map/calib/{cam_id}` | Save explicit image-to-plan correspondences |
| `GET` | `/api/status` | Camera connection states |
| `GET` | `/api/branches` | Branches, camera counts and live counts |
| `POST` | `/api/branches` | Create a branch |
| `DELETE` | `/api/branches/{branch_id}` | Remove a branch and its cameras |
| `POST` | `/api/cameras/{cam_id}/branch` | Change camera branch |
| `GET` | `/api/cameras` | Camera metadata and model switches |
| `POST` | `/api/cameras` | Probe and add a camera |
| `DELETE` | `/api/cameras/{cam_id}` | Stop and remove a camera |
| `POST` | `/api/cameras/{cam_id}/models` | Change model switches |
| `POST` | `/api/camera-face-event` | Receive camera XML / JPEG event payloads |
| `POST` | `/api/camera-event` | Receive camera XML / JPEG event payloads |
| `GET` | `/api/faces` | Filtered people cards and counts |
| `GET` | `/api/branch-alerts` | Cross-branch identity alert history |
| `POST` | `/api/branch-alerts/ack` | Acknowledge alerts |
| `GET` | `/api/whereis` | Current name owner per identity |
| `POST` | `/api/shutdown` | api shutdown |
| `GET` | `/api/appearance` | Live clothing observations / search |
| `GET` | `/api/days` | Recorded dates available in reporting |
| `GET` | `/api/idle` | Current and historical long-idle episodes |
| `POST` | `/api/idle/threshold` | Change idle threshold |
| `GET` | `/api/summary` | Daily work / phone / idle / presence summary |
| `GET` | `/api/period` | Multi-day summary |
| `GET` | `/api/routes` | Camera or zone routes |
| `GET` | `/api/clothing` | Daily clothing memory and sighting counts |
| `GET` | `/api/names` | Named people for search suggestions |
| `GET` | `/api/faces/{person_id}/samples` | Samples belonging to a card |
| `GET` | `/api/faces/crop/{name}` | Retained face crop |
| `POST` | `/api/faces/{person_id}/name` | Name a person; matching names can merge cards |
| `POST` | `/api/faces/{person_id}/edit` | api face edit |
| `POST` | `/api/faces/{person_id}/merge` | Merge duplicate person cards |
| `POST` | `/api/activity/clear` | Clear selected activity history |
| `POST` | `/api/faces/purge-unnamed` | Remove unnamed cards and associated activity rows |
| `DELETE` | `/api/faces/{person_id}` | Delete a person card |

## Behaviour

Branch-scoped reads use a `branch` query parameter; reports use day / period parameters and optional person filters where supported. Mutations generally accept JSON; floor-plan upload and camera events accept binary / mixed payloads. Streams return multipart JPEG and image endpoints return files.

This is the observed current surface, not a versioned public contract. The application needs consistent input validation, error status codes and authorization before external API exposure. No operational endpoints are hosted by this repository.
