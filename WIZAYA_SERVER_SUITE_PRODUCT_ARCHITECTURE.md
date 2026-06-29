# Wizaya Server Suite Product Architecture

Status: Corrected product framing, 2026-06-29.

## 1. Product Framing

Use this terminology consistently:

```text
Project Bantay Bayan (PBB)
= The civic/emergency-response initiative and public-interest platform.

Wizaya Server Suite
= The reusable technical foundation / edge-server infrastructure layer developed by Wizaya IT Solutions.

PBB Node Kit
= The field deployment package that runs the Wizaya Server Suite plus PBB applications on local hardware.

PBB Apps
= The mission-specific and citizen-facing applications built on top of the server suite.
```

Wizaya Server Suite is not a replacement name for Project Bantay Bayan. PBB remains the public-interest emergency response platform. Wizaya Server Suite is the reusable server foundation that can host, install, connect, monitor, and expose PBB applications on local edge hardware.

## 2. Scope Boundary

This document describes the proposed product architecture for Wizaya Server Suite as it relates to PBB Node Kits.

It does not rename individual PBB apps as Wizaya apps.

It does not claim every item is already implemented as a single packaged product. Implementation status must be read from the underlying PBB repositories and docs.

## 3. Relationship Model

```text
Project Bantay Bayan (PBB)
  -> civic/emergency-response program
  -> PBB workflows, operations, data, and public service outcomes

PBB Node Kit
  -> deployable field kit
  -> local hardware + Windows/WAMP/Technitium + Wizaya Server Suite + PBB apps

Wizaya Server Suite
  -> reusable edge-server foundation
  -> installer/provisioning, local DNS, service runtime, relay, realtime, map cache, gateway, monitoring

PBB Apps
  -> Hotline, Relay, Hub/HQ, Support, Landing, Realtime, MapServer, Maestro, Chat, Games, Account, Helper
  -> mission-specific app code and user workflows
```

## 4. What Belongs To The Server Suite

The suite should be understood as the edge-server layer that makes a PBB Node Kit operable.

| Layer | Responsibility | Current PBB Evidence |
|---|---|---|
| Provisioning | Install/update/configure local app packages | Kit Setup |
| Local runtime | WAMP/Apache/PHP/MySQL assumptions, Windows services | Kit Setup, app release metadata |
| Local DNS | Local `*.pbb.ph` hostnames | Kit Setup owner clarification: Technitium DNS requirement |
| Service registration | Register app processes/workers as Windows services | Kit Setup owner clarification |
| Node identity | Hub ID/token validation and local `hub.json` identity | Kit Setup, Relay `public\hub.json`, Hub/HQ |
| Store-and-forward | Relay messages, deliveries, handlers, webhook delivery | PBB Relay |
| Public gateway | Sanitized hub metadata and M2M gateway | PBB Landing |
| Realtime gateway | Local shared WebSocket service | PBB Realtime |
| Offline maps | Local tile/boundary cache and preflight/data prep target | PBB MapServer |
| Monitoring | Observe local worker/service telemetry | PBB Maestro |
| Local identity | Planned local-node Account Service | PBB Account proposal |

## 5. What Remains PBB App Layer

PBB apps remain mission-specific applications. They are not renamed as Wizaya apps.

| PBB App | Role In PBB | Relationship To Server Suite |
|---|---|---|
| PBB Hotline | Emergency reporting, operator handling, command/SITREP, support request creation | Runs on node runtime; uses Realtime, Relay, MapServer, Account later |
| PBB Support System | SITREP/support operations and support lifecycle handling | Runs on node runtime; consumes Relay and Realtime |
| PBB Chat | Local barangay chat | Runs as local app; uses Realtime; proposed Account SSO later |
| PBB Games | Optional local engagement/preparedness learning | Runs as optional local PHP/static app |
| PBB Landing | Local launcher and public-safe gateway | Part app, part server-suite surface |
| PBB Relay | Store-and-forward infrastructure | Core server-suite service for PBB |
| PBB Realtime | Shared local WebSocket gateway | Core server-suite service for PBB |
| PBB MapServer | Offline/local maps | Core server-suite mapping service |
| PBB Maestro | Observability | Core server-suite monitoring service |
| PBB Account | Planned local identity service | Core server-suite identity service |

## 6. PBB Node Kit Architecture

```text
Local PBB Node Hardware
  -> Windows
  -> WampServer
  -> Technitium DNS
  -> Kit Setup
      -> installs app bundles
      -> validates Hub HQ hub ID/token
      -> configures local DNS/vhosts/services
      -> runs data prep
      -> populates MapServer tiles/boundaries
  -> Wizaya Server Suite services
      -> Relay
      -> Realtime
      -> MapServer
      -> Maestro
      -> Landing
      -> Account (planned)
  -> PBB apps
      -> Hotline
      -> Support
      -> Chat
      -> Games
      -> future responder/helper apps
```

## 7. Edge-Server Responsibilities

Wizaya Server Suite should provide reusable capabilities that are not specific to one emergency workflow:

- local-first hosting and runtime setup
- local DNS and service discovery
- app package install/update lifecycle
- node identity and topology wiring
- LAN-first operation
- store-and-forward messaging
- public-safe gateway and metadata projection
- shared realtime transport
- local map/tile serving
- service health observation
- local identity/SSO, once Account V1 is implemented

These capabilities can support PBB and potentially other local edge-server deployments, but the current reviewed implementation evidence is from the PBB stack.

## 8. Connectivity Model

```text
LAN users
  -> local PBB app domains
  -> PBB apps
  -> local databases/services

Local PBB apps
  -> Relay for store-and-forward upstream/downstream messages
  -> Realtime for WebSocket rooms/events
  -> MapServer for cached tiles/boundaries
  -> Account for local SSO (planned)

Public/upper-level access
  -> PBB-managed FRP tunnel model (planned owner-confirmed design)
  -> Landing public hub metadata and M2M gateway
  -> Relay node endpoint
  -> upstream city/municipality/province/cloud HQ topology
```

## 9. Product Packaging View

PBB Node Kit should be the deployable product package for field use:

| Package Area | Contents |
|---|---|
| Platform prerequisites | WampServer, Technitium DNS, Windows service support |
| Installer | Kit Setup |
| Server-suite services | Relay, Realtime, MapServer, Maestro, Landing, Account planned |
| Mission apps | Hotline, Support, Chat, Games, future responder/helper app |
| Data prep | Hub identity, local config, map tiles/boundaries, Relay client/handler identities |
| Operations docs | runtime contracts, gateway runbook, authorization guides, emergency-mode procedures |

## 10. Implementation Status

| Area | Status |
|---|---|
| Kit Setup | Implemented in current PBB codebase |
| Relay | Implemented |
| Realtime | Implemented |
| MapServer | Implemented |
| Maestro | Implemented as observer-only |
| Landing | Implemented baseline |
| Hotline | Implemented |
| Support | Implemented baseline |
| Chat | Implemented baseline, local-only |
| Games | Implemented optional local app |
| Account | Planned local-node service; proposal/checklist exist, implementation not found |
| PBB-managed FRP tunnel | Owner-confirmed design; code-level implementation not confirmed in reviewed repos |
| Responder/helper mobile app | Planned/design-stage; implementation not confirmed |

## 11. Evidence

Primary PBB documentation:

- `C:\wamp64\www\pbb\documentations\PBB_SELECTED_APPS_TECHNICAL_BRIEFING.md`
- `C:\wamp64\www\pbb\documentations\PBB_SELECTED_APPS_ECOSYSTEM_MAP.md`
- `C:\wamp64\www\pbb\documentations\PBB_SELECTED_APPS_CODEX_FINDINGS.md`
- `C:\wamp64\www\pbb\documentations\PBB_NODE_IDENTITY_AND_HUB_JSON_CONTRACT.md`
- `C:\wamp64\www\pbb\documentations\PBB_LANDING_REGISTRY_AND_PUBLIC_GATEWAY_RUNBOOK.md`
- `C:\wamp64\www\pbb\documentations\PBB_ACCOUNT_V1_IMPLEMENTATION_PLAN.md`

Primary local repositories:

- `C:\wamp64\www\pbb\kit-setup`
- `C:\wamp64\www\pbb\relay`
- `C:\wamp64\www\pbb\realtime`
- `C:\wamp64\www\mapserver`
- `C:\wamp64\www\pbb\maestro`
- `C:\wamp64\www\pbb\landing`
- `C:\wamp64\www\pbb\hotline`
- `C:\wamp64\www\pbb\support`
- `C:\wamp64\www\pbb\chat`
- `C:\wamp64\www\pbb\games`
- `C:\wamp64\www\pbb\account`

## 12. Explicit Non-Goals

- Do not rename Project Bantay Bayan to Wizaya Server Suite.
- Do not present PBB as only a private vendor product.
- Do not rename PBB Hotline, PBB Relay, PBB Support, or other PBB apps as Wizaya apps.
- Do not claim Account, FRP gateway, or responder/helper mobile workflow are implemented until code confirms it.
- Do not mix unrelated legacy `C:\wamp64\www\wizaya` application architecture into PBB Node Kit product framing unless a separate migration/reuse decision is made.

