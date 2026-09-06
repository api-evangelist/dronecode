---
name: dronecode-preflight-arm-takeoff-land
description: >-
  Bring a PX4 vehicle from connected to airborne and back down safely over the MAVSDK gRPC API —
  health gate, arm, takeoff, hold, land, disarm — with the reversal for every step named in advance.
  Rehearse in PX4 SITL before pointing at hardware.
api: MAVSDK gRPC API
contract: grpc/_index.yml
generated: '2026-09-06'
method: generated
source: >-
  Grounded in the verbatim protobuf contracts under grpc/ (github.com/mavlink/MAVSDK-Proto @ 4fbf5ce1).
  Every RPC named below was read out of those files; none is invented.
operations:
  - mavsdk.rpc.core.CoreService/SubscribeConnectionState
  - mavsdk.rpc.telemetry.TelemetryService/SubscribeHealthAllOk
  - mavsdk.rpc.telemetry.TelemetryService/SubscribeHealth
  - mavsdk.rpc.telemetry.TelemetryService/SubscribeBattery
  - mavsdk.rpc.telemetry.TelemetryService/SubscribeArmed
  - mavsdk.rpc.telemetry.TelemetryService/SubscribeLandedState
  - mavsdk.rpc.telemetry.TelemetryService/SubscribeInAir
  - mavsdk.rpc.action.ActionService/SetTakeoffAltitude
  - mavsdk.rpc.action.ActionService/Arm
  - mavsdk.rpc.action.ActionService/Takeoff
  - mavsdk.rpc.action.ActionService/Land
  - mavsdk.rpc.action.ActionService/Disarm
  - mavsdk.rpc.action.ActionService/ReturnToLaunch
---

# Preflight, arm, take off, land

## Before anything

**This flies a real aircraft.** The MAVSDK contract has no authentication and no idempotency, and it
does not tell you whether you are talking to a simulator or to hardware. Point at PX4 SITL first —
`make px4_sitl gz_x500`, offboard API on UDP 14540 (see `sandbox/dronecode-sandbox.yml`) — and only
move to a vehicle once the whole sequence runs clean.

You are talking to `mavsdk_server`, which you run yourself (default `0.0.0.0:50051`). There is no
Dronecode-hosted endpoint.

## 1. Confirm you have a vehicle

`CoreService/SubscribeConnectionState` streams connection state. Do not proceed on a single message —
wait for `is_connected: true`. Every other service returns `RESULT_NO_SYSTEM` (defined by 34 of the
36 services) until this is true, and that code looks identical whether the vehicle is off, out of
range, or you dialled the wrong port.

## 2. Gate on health, not on optimism

`TelemetryService/SubscribeHealthAllOk` is the single boolean gate. When it is false,
`SubscribeHealth` tells you which subsystem is not ready (gyrometer, accelerometer, magnetometer,
local/global position, home position). Also read `SubscribeBattery`.

Do not arm on a false health. `ActionService/Arm` will return `RESULT_COMMAND_DENIED` and you will
have learned nothing the health stream had not already told you.

## 3. Set the takeoff altitude before arming

`ActionService/SetTakeoffAltitude` takes metres above the takeoff point. Set it explicitly — do not
inherit whatever the last operator left in the parameter. Read it back with `GetTakeoffAltitude`.

## 4. Arm

`ActionService/Arm`. Motors spin at idle immediately; the contract's own comment says "Before arming
take all safety precautions and stand clear of the drone!"

**Reversal:** `ActionService/Disarm`, and the window is stated in the contract — it works only while
the vehicle "considers itself landed". Once you are airborne, disarm is refused with
`RESULT_COMMAND_DENIED_NOT_LANDED`. Your escape hatch after that point is `Land` or
`ReturnToLaunch`, not `Disarm`.

Confirm with `TelemetryService/SubscribeArmed` rather than trusting the RPC's return.

**Do not retry a failed Arm blindly.** There is no idempotency key in this API. `RESULT_TIMEOUT` means
you do not know whether the command reached the vehicle; read `SubscribeArmed` to find out before
sending it again.

## 5. Take off

`ActionService/Takeoff`. The vehicle must already be armed. Watch
`TelemetryService/SubscribeInAir` and `SubscribeLandedState` for the transition — `Takeoff` returns
as soon as the command is accepted, not when the vehicle is at altitude.

**Never retry `Takeoff` on a timeout.** It is not idempotent and re-issuing it against a vehicle that
is already climbing is undefined behaviour, not a no-op.

**Reversal:** `ActionService/Land` (descends where it is) or `ActionService/ReturnToLaunch` (climbs
to a clearance altitude, flies home, lands).

## 6. Land and disarm

`ActionService/Land`, then wait for `SubscribeLandedState` to report landed and `SubscribeInAir` to
go false. Only then `ActionService/Disarm` — see the window in step 4.

## Reading errors

The gRPC status will usually be `OK` even when the operation failed. Read
`ActionResult.result` and `result_str` on every response. `ActionResult` alone defines 25 codes;
`errors/dronecode-problem-types.yml` has all 327 across the 36 services.

The codes worth branching on here:

| Code | What it actually means |
|---|---|
| `RESULT_NO_SYSTEM` | No vehicle connected. Go back to step 1. |
| `RESULT_CONNECTION_ERROR` | Link dropped mid-command. Outcome unknown — verify with telemetry. |
| `RESULT_TIMEOUT` | No reply. **Not** proof the command did not execute. |
| `RESULT_COMMAND_DENIED` | Vehicle refused — usually a failed preflight check. |
| `RESULT_COMMAND_DENIED_NOT_LANDED` | You tried to disarm in flight. |
| `RESULT_BUSY` | Another command is in progress. Wait; do not queue. |

## What this skill will not do

`ActionService/Kill` drops the aircraft out of the sky and `Terminate` runs the configured
flight-termination routine (typically disarm plus parachute). Both are in the contract, neither is in
this skill, and neither should be wired to an agent without a deliberate human-in-the-loop design.
