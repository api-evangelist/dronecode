# Dronecode Foundation

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **Not from the company, and here with a question?** You are welcome here — we would rather be the
> front line and point you the right way than have a good report go nowhere. What this repository
> can answer is narrow, though, so it is worth knowing who you are actually looking for:
>
> - **A question about how the API works, an account, billing, or a bug in the service** — that is
>   the company's own support, not us. We profile this API; we do not operate it and cannot see
>   your account.
> - **A bug in an open-source project we only catalog** — file it on that project's own repository.
>   This has happened with a real and correct bug report that reached us instead of the people who
>   could fix it, which helped nobody.
> - **Anything about this listing itself** — the description, the tags, the rating, a missing or
>   wrong artifact — is ours. Open an issue here.
> - **Not sure, or something general about API Evangelist or APIs.io** — open an issue on the
>   [APIs.io Inbox](https://github.com/api-search/inbox) and we will route it.
>
> **This repository contains no software, and we will never ask you to download anything.** There is
> no build, release, installer, or binary here — only text and machine-readable API descriptions, so
> there is nothing here that can be "corrupt" or need "repairing". Any issue, comment, or email
> claiming otherwise and offering a download link is not from us and is hostile. Do not follow the
> link; it is a lure. Report it to GitHub and, if you like, tell us at
> [info@apievangelist.com](mailto:info@apievangelist.com) so we can take it down.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

The Dronecode Foundation is a vendor-neutral Linux Foundation collaborative project and the home of the open source flight stack the drone industry runs on: PX4 Autopilot, the MAVLink communication protocol, MAVSDK, QGroundControl and the Pixhawk hardware standards. Its machine-readable surface is a gRPC one — MAVSDK-Proto publishes 38 protobuf services and 387 RPCs covering arming, takeoff, missions, geofencing, telemetry, camera, gimbal and parameter control — alongside the MAVLink dialect XML and the component-metadata JSON Schemas that define how vehicles describe themselves. Nothing is hosted: the consumer runs mavsdk_server themselves, and every contract, SDK and firmware is free and open source.

**Areas:** Drones · UAV · Robotics · MAVLink · PX4 · Autopilot · Aerial Robotics · gRPC · Open Source · Linux Foundation

## Dronecode Foundation on APIs.io

Everything profiled for Dronecode Foundation is published on APIs.io rather than in this repository. Start at the provider page — **[apis.io/providers/dronecode/](https://apis.io/providers/dronecode/)** — or go straight to one of the APIs or properties below.

### APIs (4)

- [PX4 Autopilot](https://apis.io/apis/dronecode/dronecode-api/) — PX4 is the open source flight control firmware the Dronecode Foundation stewards — the software running on the vehicle that MAVLink and MAVSDK talk to. It…
- [MAVSDK gRPC API](https://apis.io/apis/dronecode/mavsdk/) — MAVSDK is the Dronecode Foundation's programmable interface to any MAVLink vehicle, and the only surface in the estate with a full machine-readable…
- [MAVLink Protocol](https://apis.io/apis/dronecode/mavlink/) — MAVLink is the message-level interoperability standard for drones, and the Dronecode Foundation publishes it. The contract is the dialect XML — 19 files…
- [Dronecode Foundation Website (WordPress REST)](https://apis.io/apis/dronecode/dronecode-org-website-wordpress-rest/) — The public WordPress REST API (wp-json) of the Dronecode Foundation website at dronecode.org: the route index of the site's content management system,…

### Published properties

Each property below is a page on APIs.io carrying the machine-readable artifact and what it says.

#### MCP servers (1)

Model Context Protocol servers that expose the API to agents.

- [MCP candidate (no server published)](https://apis.io/servers/dronecode/mcp-candidate-no-server-published/)

#### JSON Schema (5)

The data models the API reads and writes.

- [Dronecode Mavlink Component Metadata Actuators.Schema](https://apis.io/schemas/dronecode/dronecode-mavlink-component-metadata-actuators.schema/)
- [Dronecode Mavlink Component Metadata General.Schema](https://apis.io/schemas/dronecode/dronecode-mavlink-component-metadata-general.schema/)
- [Dronecode Mavlink Component Metadata Parameter.Schema](https://apis.io/schemas/dronecode/dronecode-mavlink-component-metadata-parameter.schema/)
- [Dronecode Mavlink Component Metadata Peripherals.Schema](https://apis.io/schemas/dronecode/dronecode-mavlink-component-metadata-peripherals.schema/)
- [Dronecode Mavlink Component Metadata Translation.Schema](https://apis.io/schemas/dronecode/dronecode-mavlink-component-metadata-translation.schema/)

#### Security (3)

Authentication, trust centre, and vulnerability disclosure.

- [Dronecode Authentication](https://apis.io/security/dronecode/dronecode-authentication/)
- [Dronecode Domain Security](https://apis.io/security/dronecode/dronecode-domain-security/)
- [Dronecode Vulnerability Disclosure](https://apis.io/security/dronecode/dronecode-vulnerability-disclosure/)

#### Plans and pricing (1)

Published plans, tiers and prices.

- [Dronecode Plans Pricing](https://apis.io/plans/dronecode/dronecode-plans-pricing/)

#### Rate limits (1)

Published request limits.

- [Dronecode Rate Limits](https://apis.io/rate-limits/dronecode/dronecode-rate-limits/)

#### FinOps (1)

What the API costs to run, by operation.

- [Dronecode Finops](https://apis.io/finops/dronecode/dronecode-finops/)

## Where to find this API

Links to the company's own pages. This repository publishes no copy of their specifications — the canonical document is theirs and lives at the URLs below.

- [Website](https://www.dronecode.org/)
- [Documentation](https://docs.px4.io/)
- [API reference](https://mavsdk.mavlink.io/main/en/cpp/api_reference/index.html)
- [Pricing](https://dronecode.org/membership/)
- [Sign up](https://discuss.px4.io/signup)
- [Support](https://discuss.px4.io/)
- [Blog](https://dronecode.org/feed/)
- [Privacy policy](https://dronecode.org/privacy-policy/)
- [GitHub](https://github.com/mavlink)

## API Evangelist rating

**Kin Score 46.6** — developing · rubric 0.25.0 · scored 2026-10-23

| Facet | Score |
|---|---|
| Discoverability | 60.7 |
| Contract quality | 36.0 |
| Contract governance | 4.5 |
| Operational transparency | 39.5 |
| Developer ergonomics | 73.2 |
| Access clarity | 44.7 |

**Agent readiness 22.5** — agent-aware

| Agent-readiness dimension | Result |
|---|---|
| Spec Presence | yes |
| Agentic Access | — |
| Reversibility Documented | — |
| MCP Server | — |
| Auth Clarity | bearer (partial credit) |
| Idempotency | — |
| Error Semantics | derived (partial credit) |
| OpenAPI Examples | — |
| Rate Limit Signal | documented (partial credit) |
| Event Surface Described | — |
| Agent Skills | derived (partial credit) |
| Well Known Catalog | yes |
| Consent Identity | — |
| Agent Card | — |
| Dry Run Mode | — |
| Delegated Identity | — |
| Protected Resource Metadata | — |
| Dynamic Client Registration | — |
| Agentic Commerce | — |
| Pagination Ratio | na |
| Operation Distinctiveness | na |

**Regulatory alignment 30.4** — Horizontal (data, software, accessibility, platform)

The rubric is published at [github.com/api-evangelist/kin-score](https://github.com/api-evangelist/kin-score). The per-check breakdown behind these numbers — which checks were earned, partial or missed — along with score history and how this provider compares to its peers, is available at [apis.io/providers/dronecode/](https://apis.io/providers/dronecode/).

## How this profile is made

API Evangelist profiles a company's public API surface the way a developer would find it: the website, developer portal and documentation, the specifications it publishes, its public repositories, and its pricing, status and changelog pages. From that it derives the properties above — contracts split per API, workflows, schemas, collections, governance rules, plans and rate limits — and scores them against the published [Kin Score rubric](https://github.com/api-evangelist/kin-score).

The artifacts are published on [APIs.io](https://apis.io), not in this repository. This repository is the public record for Dronecode Foundation: the place to say something is wrong, missing or out of date, and to ask for a re-score or a removal.

## Open an issue

- **Something about this listing** — a wrong description, a missing API, a stale artifact or a question about the rating: [open an issue on this repository](https://github.com/api-evangelist/dronecode/issues/new).
- **A new API, or one that is not in the catalog yet** — [submit it on APIs.io](https://apis.io/add/).
- **Anything else** — [the APIs.io inbox](https://github.com/api-search/inbox/issues/new) or [info@apievangelist.com](mailto:info@apievangelist.com).
