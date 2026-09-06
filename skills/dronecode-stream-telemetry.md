---
name: dronecode-stream-telemetry
description: >-
  Consume PX4 vehicle telemetry over the MAVSDK gRPC API — which of the 59 TelemetryService RPCs to
  subscribe to, how server-streaming behaves on a bandwidth-limited link, and how to tell a dropped
  link from a quiet one.
api: MAVSDK gRPC API
contract: grpc/_index.yml
generated: '2026-09-06'
method: generated
source: >-
  Grounded in grpc/dronecode-mavsdk-telemetry.proto and grpc/dronecode-mavsdk-core.proto, saved
  verbatim from github.com/mavlink/MAVSDK-Proto @ 4fbf5ce1.
operations:
  - mavsdk.rpc.core.CoreService/SubscribeConnectionState
  - mavsdk.rpc.core.CoreService/SetMavlinkTimeout
  - mavsdk.rpc.telemetry.TelemetryService/SubscribePosition
  - mavsdk.rpc.telemetry.TelemetryService/SubscribeHome
  - mavsdk.rpc.telemetry.TelemetryService/SubscribeAttitudeEuler
  - mavsdk.rpc.telemetry.TelemetryService/SubscribeVelocityNed
  - mavsdk.rpc.telemetry.TelemetryService/SubscribeBattery
  - mavsdk.rpc.telemetry.TelemetryService/SubscribeGpsInfo
  - mavsdk.rpc.telemetry.TelemetryService/SubscribeFlightMode
  - mavsdk.rpc.telemetry.TelemetryService/SubscribeHealth
  - mavsdk.rpc.telemetry.TelemetryService/SubscribeHealthAllOk
  - mavsdk.rpc.telemetry.TelemetryService/SubscribeStatusText
  - mavsdk.rpc.telemetry.TelemetryService/SubscribeLandedState
  - mavsdk.rpc.telemetry.TelemetryService/SubscribeInAir
  - mavsdk.rpc.telemetry.TelemetryService/SubscribeArmed
  - mavsdk.rpc.telemetry.TelemetryService/GetGpsGlobalOrigin
---

# Stream telemetry

## The shape of this service

`TelemetryService` has 59 RPCs and almost all of them are **server-streaming** `Subscribe*` calls.
There is essentially no polling interface: `GetGpsGlobalOrigin` is one of the very few unary getters,
so "read the current position once" means opening a stream and taking the first message.

That matters for anything request/response shaped. An MCP tool, a webhook consumer, or a REST facade
over this API has to hold streams open and snapshot them; there is no unary equivalent to fall back
on.

## Subscribe to what you need, and no more

Every stream costs MAVLink link bandwidth, which on a typical telemetry radio is scarce. A minimal
useful set for a flight-monitoring agent:

- `SubscribeArmed`, `SubscribeInAir`, `SubscribeLandedState` — state machine
- `SubscribeFlightMode` — what the vehicle thinks it is doing
- `SubscribeHealthAllOk` — one boolean gate; `SubscribeHealth` only when it goes false
- `SubscribeBattery` — the clock on the whole flight
- `SubscribePosition` and `SubscribeHome` — where it is, where home is
- `SubscribeStatusText` — the autopilot's own messages, the fastest route to why something was denied

Add `SubscribeGpsInfo` when position quality matters, `SubscribeVelocityNed` and
`SubscribeAttitudeEuler` for control work. Leave `SubscribeRawImu`, `SubscribeScaledImu` and
`SubscribeOdometry` alone unless you have a reason — they are high rate.

## Silence is ambiguous

A stream that stops producing messages does not distinguish:

- the vehicle powering down,
- the radio link dropping,
- the autopilot deprioritising that stream under bandwidth pressure.

Run `CoreService/SubscribeConnectionState` alongside your telemetry streams — it is the authoritative
answer to "is there still a vehicle". `CoreService/SetMavlinkTimeout` sets how long MAVSDK waits
before declaring the system gone; tune it to your link rather than accepting the default on a marginal
radio.

There is no heartbeat, keepalive or `Retry-After` in the telemetry streams themselves.

## Errors on a stream

`TelemetryResult` uses the same enum shape as the rest of the API. The ones you will meet:

- `RESULT_NO_SYSTEM` — no vehicle. The stream will not produce anything.
- `RESULT_CONNECTION_ERROR` — link failure.
- `RESULT_UNSUPPORTED` — this airframe/firmware does not publish that data.
- `RESULT_TIMEOUT` — the request to start the subscription itself timed out.

Remember the gRPC status is usually `OK` even on failure — read `result` and `result_str`.

## No rate-limit signal

Nothing in this API tells you that you are asking for too much. Overrunning the link surfaces as
`RESULT_TIMEOUT` and `RESULT_CONNECTION_ERROR` — the same codes as a genuinely absent vehicle. See
`rate-limits/dronecode-rate-limits.yml`. Budget your subscriptions up front rather than backing off
after the fact, because there is nothing to back off from.
