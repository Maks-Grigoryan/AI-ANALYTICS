# Feature reference

## Branches and cameras

The entry page separates locations from the global people database. A branch groups cameras, a floor plan and zones; a person remains a single identity across all branches.

- Create and remove branches, with a name and note.
- Add a Hikvision camera after checking its SDK connection and credentials in an isolated process.
- Assign a camera to a branch and remove cameras from the interface.
- Enable or disable pose, objects, weapon, face and clothing processing per camera.
- Observe connection status and worker restart behaviour.
- Branch deletion removes its cameras from configuration; historical people and event records are retained.

These are logical groups on one host. They are not a distributed multi-server deployment.

## Live workspace

- Annotated camera panels with pose skeletons, tracked objects, identity labels, clothing colours and activity labels.
- Camera status indicators, adjustable one-to-four-column layout and draggable camera ordering.
- Name search that highlights the current camera holding an identity.
- Clothing search by shirt colour, trouser colour or both.
- Automatic image reconnection and browser-side status polling.
- A lighter visual mode for slower operator machines.

The transport is an HTTP multipart JPEG stream. The application does not require a React frontend, external UI framework or cloud inference service.

## People and identity

The recognizer groups repeated face sightings into cards. An operator can name a card, edit its position, phone and other profile fields, inspect its retained sample and merge duplicates. Named and unnamed cards have separate tabs and counts; cards can be filtered by branch.

Face embeddings are normalized vectors. A global gallery matrix is searched by cosine similarity, with a measured preference margin for named cards when an unnamed duplicate is nearly as similar. Enrollment and rechecking run at different intervals.

An identity has one current owner across camera tracks. Better face evidence can transfer ownership, cutting the old and new intervals at the handover. Clothing and recent position can preserve an identity when the face is hidden, and can veto weak contradictory face matches. Clothing is a supporting cue, not an independent biometric identifier.

The application also accepts camera-generated face-capture events in XML / JPEG payloads. Face boxes are associated with recent people tracks where possible. A shared gallery allows the same identity to be recognized at another branch, with an alert if sightings fall inside a configured cross-branch time window.

## Posture and activity

Posture and activity are separate axes. A person may be standing and using a phone, or sitting and working.

| Output | Evidence |
|---|---|
| Sitting / standing | Keypoint geometry, visible lower-body evidence and chair context |
| Working | Hand-area contact with keyboard, mouse, laptop or book / paper proxy, with temporal memory |
| On phone | Tracked phone and hand proximity, with contact logic to resolve competing work interactions |
| Idle | No supported work or phone interaction in the current state |

The implementation uses rules and temporal state. It does not infer actual task completion, attention, intent or productivity. Occlusion, small objects and misleading detections can affect labels.

## Time and event reporting

- Presence intervals remain independent of posture and activity transitions.
- Stable changes are debounced before recording, reducing label flicker in history.
- Overlapping observations are merged when calculating duration totals.
- Daily work, phone, idle and presence totals, with first / last appearance.
- Current long-idle list, configurable idle threshold and historical episodes.
- Period summaries for 3, 7 or 30 days, including days present and average work estimate per day.
- Camera routes or zone routes, depending on map configuration.
- Explicit history-clearing controls and cleanup operations.

Known edges remain around cross-midnight intervals and propagation of identity merges into historical tables. These are recorded in the deployment notes rather than treated as supported guarantees.

## Floor map

- Upload a floor-plan image or draw walls, rooms, doors and furniture.
- Place cameras, choose orientation, field of view and approximate visible range.
- Align a camera image to the plan, or supply at least four correspondence pairs for homography.
- Define named polygon zones and show which cameras are uncalibrated.
- Project ankle positions, or the bottom of the person box when ankles are unreliable, into plan coordinates.
- Show fresh live people markers and suppress stale ones.
- Record stable zone visits across camera handovers.
- Sample position trails and replay a day with a timeline and person selection.

Projection assumes a shared ground plane. Stairs, strong occlusion and poor calibration limit positional accuracy. The default trail retention is seven days.

## Handgun detection

A custom single-class YOLO26m model checks whole frames and draws red detection boxes. It shares the GPU with the other models through its own batch queue. The current threshold is 0.25 and the target frequency is 6 Hz.

The code can select class names from a future expanded checkpoint, and includes an experimental open-vocabulary backend. The showcased deployed model contains `handgun` only. No separate second-stage confirmation model or validated autonomous response workflow is claimed.
