---
name: dronecode-upload-and-run-mission
description: >-
  Upload a waypoint mission to a PX4 vehicle over the MAVSDK gRPC API, run it, follow progress, and
  clear or pause it — including how to abort an upload mid-transfer and what "reversal" does and does
  not mean once waypoints have been flown.
api: MAVSDK gRPC API
contract: grpc/_index.yml
generated: '2026-09-06'
method: generated
source: >-
  Grounded in grpc/dronecode-mavsdk-mission.proto and grpc/dronecode-mavsdk-geofence.proto, saved
  verbatim from github.com/mavlink/MAVSDK-Proto @ 4fbf5ce1. Every RPC named is in those files.
operations:
  - mavsdk.rpc.mission.MissionService/UploadMission
  - mavsdk.rpc.mission.MissionService/SubscribeUploadMissionWithProgress
  - mavsdk.rpc.mission.MissionService/CancelMissionUpload
  - mavsdk.rpc.mission.MissionService/DownloadMission
  - mavsdk.rpc.mission.MissionService/ClearMission
  - mavsdk.rpc.mission.MissionService/StartMission
  - mavsdk.rpc.mission.MissionService/PauseMission
  - mavsdk.rpc.mission.MissionService/SetCurrentMissionItem
  - mavsdk.rpc.mission.MissionService/SubscribeMissionProgress
  - mavsdk.rpc.mission.MissionService/IsMissionFinished
  - mavsdk.rpc.mission.MissionService/SetReturnToLaunchAfterMission
  - mavsdk.rpc.geofence.GeofenceService/UploadGeofence
  - mavsdk.rpc.geofence.GeofenceService/ClearGeofence
---

# Upload and run a mission

Rehearse in PX4 SITL first (`sandbox/dronecode-sandbox.yml`). The contract gives you no way to tell a
simulator from an aircraft.

## 1. Fence before mission

`GeofenceService/UploadGeofence` first, if you are using one. A geofence uploaded after a mission has
started does not retroactively constrain the waypoints already queued. `ClearGeofence` removes the
whole set — there is no partial removal in the contract.

`RESULT_TOO_MANY_GEOFENCE_ITEMS` means the vehicle's storage is the limit, not the API.

## 2. Upload, with progress

`MissionService/UploadMission` transfers the `MissionPlan`. For anything but a handful of items,
subscribe to `SubscribeUploadMissionWithProgress` in parallel — it streams
`ProgressData` so you can tell a slow radio link from a stalled transfer.

**Aborting mid-transfer:** `CancelMissionUpload`. This is only meaningful while the progress stream is
still running. After the upload completes, the reversal is `ClearMission`, not cancel.

Codes specific to upload:

- `RESULT_TOO_MANY_MISSION_ITEMS` — the plan exceeds vehicle storage.
- `RESULT_UNSUPPORTED_MISSION_CMD` — a command in your plan is not supported by this airframe/firmware.
- `RESULT_MISSION_TYPE_NOT_CONSISTENT` — mixed mission types in one plan.
- `RESULT_INVALID_ARGUMENT` — malformed item.
- `RESULT_TRANSFER_CANCELLED` — your own cancel landed.

**Do not retry `UploadMission` on `RESULT_TIMEOUT` without checking.** There is no idempotency key.
`DownloadMission` and compare, or `ClearMission` and start over — a blind retry can leave a partially
transferred plan on the vehicle.

## 3. Decide the end-of-mission behaviour before you start

`SetReturnToLaunchAfterMission(true)` makes the vehicle come home when the last item completes.
Read it back with `GetReturnToLaunchAfterMission`. Set this deliberately; the default is whatever the
last operator left.

## 4. Start and follow

`MissionService/StartMission`, then `SubscribeMissionProgress` for `current` / `total` item indices.
`IsMissionFinished` is the unary check.

`RESULT_NO_MISSION_AVAILABLE` means nothing is uploaded. `RESULT_CURRENT_INVALID` means the current
item index is out of range for the loaded plan.

## 5. What "reversal" means here

This is the part an agent gets wrong.

- `PauseMission` holds position. The mission resumes from the **same item** on the next
  `StartMission`. It is a stop, not an undo.
- `ClearMission` removes the plan from the vehicle. Waypoints already flown stay flown — clearing
  does not fly the aircraft back.
- `SetCurrentMissionItem` moves the pointer. Rewinding to item 0 makes the vehicle re-fly the
  mission; it does not restore any prior state.
- Nothing in this service returns the aircraft to where it started. That is
  `ActionService/ReturnToLaunch`, in the other service.

Plan the abort path before you call `StartMission`: pause, then RTL, then land.

## 6. Verify rather than trust

`DownloadMission` reads the plan back off the vehicle. Compare it against what you uploaded before
arming. The upload RPC returning `RESULT_SUCCESS` tells you the transfer completed, not that the
vehicle stored what you meant.

## Errors

`MissionResult` and `GeofenceResult` are separate enums with separate integer values — do not share
a handler across services. Full catalog: `errors/dronecode-problem-types.yml`.
