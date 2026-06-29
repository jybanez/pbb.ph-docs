# PBB Selected Apps Ecosystem Map

## 1. Ecosystem Overview

The selected codebase is a local-node-oriented PBB stack built around Laravel/PHP apps, plain PHP infrastructure tools, a static Helper UI library, and an Electron/PHP installer. Code confirms these primary roles:

- Hotline is the local emergency reporting, operator dispatch, command/SITREP, and admin app.
- Realtime is the shared WebSocket gateway used by Hotline-style product apps for presence, calls, chat, app events, and media transport.
- Relay is the store-and-forward HTTP sync service for local-app and hub-to-hub messages.
- Hub/HQ is the hub registry, heartbeat, token, geodata, settings, user, and recovery app.
- Maestro is the worker/process telemetry monitor.
- MapServer is the local map tile cache/proxy and boundary overlay server.
- Helper is a reusable static JS/CSS UI library, not a responder backend.
- Chatviewer is a local viewer for `chat_log.md`.
- Kit Setup is the Windows desktop/CLI installer and service orchestration tool.
- Support System is the support/SITREP operations app that receives Relay-delivered SITREPs and Hotline support requests, consolidates latest SITREPs, and sends support lifecycle updates back through Relay.
- Landing is the lightweight local LAN launcher plus public hub-safe metadata/gateway surface for node discovery and machine-to-machine Relay gateway exposure.
- Chat is a local barangay chat app for rooms, direct messages, safety reporting/moderation, badges, and Realtime-powered local messaging.
- Games is an optional local citizen engagement and preparedness-learning app with no confirmed operational API dependency.

Fresh-scan updates verified in selected repositories:

- Hotline now has SITREP Relay outbox/delivery support and a latest-SITREP submission command/job.
- Hotline now has Support Request persistence, relay submission/lifecycle services, and internal support-request update ingress.
- Hotline now exposes a backend-token-protected SITREP media manifest/download API and SDK for authorized backend consumers.
- Relay now exposes a backend-only relationship resolver at `POST /api/v1/relationships/resolve`.
- Kit Setup now documents/enforces additive app database migration policy for updater runs.
- Helper UI bundle updates include password fields, number-stepper form fields, row `className` alias support, and `ui.navigation.stack`.
- PBB Support System at `C:\wamp64\www\pbb\support` is now reviewed as a Laravel SITREP/support operations app.
- PBB Landing at `C:\wamp64\www\pbb\landing` is now reviewed as a lightweight PHP local launcher/public hub metadata/gateway surface.
- 2026-06-22 alignment: Kit Setup first added bundled Landing, MapServer, Maestro, Realtime, Relay, Hotline, and Support as first-class app packages, plus the Cebu MapServer boundary pack. Current 2026-06-29 local Kit version is `0.1.164`.
- 2026-06-22 alignment: the finalized Relay/Hotline/Support contract uses `hotline.command`, `sitrep.ingestor`, and `support.dispatch` machine identities; `sitrep.record` and `support.request` are separate flows.
- 2026-06-22 alignment: Relay now has operational `source.heartbeat.updated` webhooks stored separately from normal app-to-app messages, and Support receives those events at `POST /api/relay/source-heartbeats`.
- 2026-06-22 alignment: Chatviewer is DB-backed when `pbb_agentchat` exists, supports token-authenticated agent posting/claiming, and `GET /api/chat-entries.php` defaults to newest-first with `order=asc` available.
- 2026-06-29 alignment: PBB Chat at `C:\wamp64\www\pbb\chat` is reviewed as a Laravel 12 local/LAN-only barangay chat app. `release.json` disables public gateway exposure.
- 2026-06-29 alignment: PBB Games at `C:\wamp64\www\pbb\games` is reviewed as a plain PHP optional local games/learning app. Its README says version 1 does not call Hotline, Relay, Support, Realtime, Hub/HQ, Maestro, MapServer, or Kit Setup APIs.
- 2026-06-29 alignment: Kit Setup local `package.json` is `0.1.164`. Helper active loader cache revisions are `0.21.117`, even though Helper `package.json` still reports `0.21.83`.
- 2026-06-29 alignment: `C:\wamp64\www\pbb\account` exists but no app files were found; behavior is `Unknown / Not confirmed from code`.

Owner clarifications added after code review:

- Hub HQ provides node identity details. The project owner identified `https://relay.pbb.ph/hub.json` as a sample hub information endpoint used in the install/provisioning model.
- Kit Setup validates a node installation from an admin-provided hub ID and hub token.
- Kit Setup Data Prep auto-populates MapServer with tiles for the boundary dictated by Hub HQ hub information.
- Maestro is intentionally observer-only; Windows services registered during Kit Setup own process lifecycle/recovery.
- Chatviewer is personal development coordination tooling, not an operational PBB app.
- Production topology is currently planned around Philippine administrative levels: barangay -> city/municipality -> province -> cloud PBB HQ.
- Incidents are not intended to sync as editable records across different nodes. Each node consolidates SITREPs from sources plus its own SITREP and sends periodic SITREP upstream.
- Realtime is expected to run as one shared gateway instance per node.
- Responder/helper mobile workflow is planned/design-stage work. Owner clarification: it is intended as a field-ready, offline-capable companion app for barangay responders, rescue teams, medical responders, and utility helpers, covering dispatch assignment receipt, acknowledgement, navigation, staged response status, field notes/photos/outcome reports, and Relay-backed upstream sync after connectivity returns.
- Kit Setup requires WampServer and Technitium DNS for node installs; code confirms WAMP probes and Technitium DNS apply/verify actions.
- PBB does not depend on an external tunnel provider by design. Owner clarification: planned tunnel exposure uses PBB-managed FRP, with Hub HQ as the control plane, FRPS as the tunnel server, and FRPC on each local node so each PBB Relay node can receive a secure cloud-visible endpoint without static IPs, router port forwarding, or Cloudflare.

## 2. App Classification

| App | Category | Deployment Level | Primary Users | Primary Purpose |
|---|---|---|---|---|
| PBB Hotline | Citizen Apps, Operator Apps, Barangay Node Services | Local node / barangay or command-center style node | Citizens, operators, command users, admins | Emergency reporting, calls, incident handling, assignments, SITREPs |
| PBB Helper | Core UI library | Vendored into apps | Developers, consuming app frontends | Shared UI components for incident/admin/chat/media/map surfaces |
| PBB Relay | Core Infrastructure Services | Local node and hub-to-hub relay nodes | Local apps, relay operators, hub peers | Store-and-forward messaging, delivery retries, inbox/handler dispatch, attachments |
| PBB Hub / Hub HQ | HQ / Cloud Services, City/Municipality/Province Services | HQ/cloud or upper-level local node | Admins, hub machine clients | Hub registry, heartbeat, tokens, geodata, recovery |
| PBB Maestro | Monitoring / Maintenance Tools | Local node | Operators/admins, monitored workers | Worker heartbeat/event monitoring and stale status computation |
| PBB Realtime Server | Realtime Communication Services | Local node | Product apps, browsers, realtime admins | WebSocket gateway, admission, rooms, presence, chat, calls, media events |
| PBB MapServer | Mapping Services | Local node | Client apps, setup operators | Tile cache/proxy, boundary overlays, offline map prep |
| PBB Chatviewer | Monitoring / Maintenance Tools | Local development/PBB workspace | Project owner, PBB agents / development coordination | DB-backed agent chat, Markdown fallback, timeline/search, token-auth posting |
| PBB Kit Setup | Setup / Provisioning Tools | Windows setup machine / PBB Node Kit | Installer operator | Install/update/configure bundled PBB apps and services |
| PBB Support System | Operator Apps, HQ / Cloud Services, Support Operations | Support node / upper-level node / HQ-side support deployment | Support operators, receiving agencies, admins | SITREP consolidation and support request lifecycle handling |
| PBB Landing | Core Infrastructure Services, Setup / Provisioning Tools | Local node and hub public domain surface | Local users, Kit Setup, peer nodes/backend clients | LAN app launcher, public hub metadata projection, Kit-managed app registry, Relay gateway |
| PBB Chat | Citizen Apps, Barangay Node Services, Realtime Communication Services | Local barangay node / LAN-only app | Citizens/local chat users, admins/moderators | Local rooms, direct messages, reports/blocks/moderation, badges, Hotline handoff |
| PBB Games | Citizen Apps, Setup / Provisioning-adjacent local content | Local node / LAN-only optional app | Citizens/local visitors | Optional local games and emergency-preparedness learning activities |

## 3. High-Level Architecture Diagram

```text
Citizen browser / Operator browser / Command browser
  -> PBB Hotline (Laravel web + local DB)
       -> PBB Realtime HTTP admission
       -> PBB Realtime WebSocket `/realtime`
       -> PBB MapServer tile/boundary HTTP endpoints
       -> FFmpeg for local media finalization
       -> Relay hub JSON heartbeat/status lookup
       -> SITREP Relay outbox + latest-SITREP submission job/command
       -> Support Request relay submission/lifecycle update services
       -> internal SITREP media manifest/download API

Local product apps
  -> PBB Relay local client API
       -> Relay DB outbox/deliveries
       -> Relay queue worker
       -> upstream/downstream Relay `/api/v1/receive`
       -> local handler webhooks
       -> relationship resolver `/api/v1/relationships/resolve`

PBB Support System
  -> receives Relay-delivered SITREPs at `/api/relay/sitreps`
  -> receives Hotline `support.request` intake at `/api/relay/support-requests`
  -> stages latest valid SITREPs and consolidates current SITREP
  -> sends latest consolidated SITREP through Relay `/api/v1/messages`
  -> sends support lifecycle updates through Relay `/api/v1/messages`
  -> receives Relay `source.heartbeat.updated` webhooks at `/api/relay/source-heartbeats`
  -> publishes heartbeat snapshots to Realtime support rooms
  -> uses MapServer URLs for support maps/boundaries

PBB Chat
  -> local `pbb_chat` database
  -> PBB Realtime admission and backend ingress publishing
  -> Helper-backed chat UI, composer, login/account/password modals, icons
  -> handoff-only Hotline escalation endpoint returning configured Hotline URL
  -> local/LAN-only Landing launcher entry; public gateway disabled

PBB Games
  -> local PHP/static game registry and mode policy
  -> Helper-backed UI/game components
  -> no confirmed operational API calls to Hotline, Relay, Support, Realtime, Hub/HQ, Maestro, MapServer, or Kit Setup in version 1
  -> active incident/emergency modes hide or disable games

PBB Landing
  -> reads Relay public `hub.json`
  -> serves local launcher at `https://pbb.ph`
  -> exposes sanitized public `/.well-known/pbb-hub.json`
  -> accepts Kit Setup registry writes through token-protected `/internal/registry/*`
  -> gateways allowed peer `/relay/api/v1/*` requests to registered local Relay target

PBB Relay
  -> PBB Hub / Hub HQ registry and heartbeat APIs
  -> PBB Maestro telemetry APIs
  -> PBB-managed FRP tunnel path for cloud-visible Relay endpoint (planned owner-confirmed design)

PBB Realtime
  -> Product backend ingest/query endpoints
  -> PBB Maestro telemetry APIs

PBB Kit Setup
  -> Installs/configures MapServer, Maestro, Realtime, Relay, Hotline
  -> Validates Hub HQ hub ID/token during installation
  -> Plans/applies DNS, SSL/vhost, firewall, services, data prep, smoke checks
  -> Registers processes as Windows services
  -> Seeds Relay support identities and Support source heartbeat webhook token
```

## 4. Communication Matrix

| Source App | Target App | Protocol / Method | Data Sent | Trigger | Evidence |
|---|---|---|---|---|---|
| Hotline | Realtime | HTTP `/api/realtime/session` via admission payload and WebSocket `/realtime` | Signed realtime claims, room messages, presence, call signals, media events | Citizen/operator/command live flows | `hotline\routes\api\realtime.php`; `realtime\routes\api.php`; `realtime\RealtimeGateway.php` |
| Hotline | MapServer | HTTP map tile/style/boundary requests | Tile/boundary GETs | Operator map display | `hotline\README.md`; `mapserver\README.md` |
| Hotline | Relay URL | HTTP JSON GET | Hub JSON status | Scheduled hub heartbeat checks | `hotline\config\services.php`; `hotline\routes\console.php` |
| Hotline | Relay | Laravel job/HTTP relay client path | Latest SITREP handoff/outbox delivery | Queue worker and scheduler fallback | `hotline\app\Support\Sitreps\SitrepRelayOutboxService.php`; `hotline\app\Jobs\SubmitSitrepRelayDelivery.php`; `hotline\app\Console\Commands\SubmitLatestSitrepToRelay.php` |
| Hotline | Relay | Laravel service/HTTP relay client path | `support.request` envelopes with support request fields and justification codes | Support request creation/submission | `hotline\app\Support\SupportRequests\SupportRequestRelaySubmissionService.php`; `hotline\database\migrations`; `hotline\docs\support-request-relay-contract-proposal.md` |
| Relay | Support System | HTTP `POST /api/relay/sitreps` | Relay-delivered SITREP envelopes | Relay handler delivery | `support\routes\api.php`; `support\app\Http\Controllers\Api\RelaySitrepHandlerController.php` |
| Relay | Support System | HTTP `POST /api/relay/support-requests` | Hotline-origin `support.request` envelopes | Support request handler delivery | `support\routes\api.php`; `support\app\Http\Controllers\Api\RelaySupportRequestHandlerController.php` |
| Relay | Support System | HTTP `POST /api/relay/source-heartbeats` | `source.heartbeat.updated` operational webhook payloads | Source heartbeat status update | `relay\docs\relay-source-heartbeat-webhooks-implementation-checklist.md`; `support\app\Http\Controllers\Api\RelaySourceHeartbeatController.php` |
| Support System | Relay | HTTP `POST /api/v1/messages` | Consolidated `sitrep.record` envelopes | `support:sitreps:relay-latest` command/job | `support\app\Services\SitrepRelaySubmissionService.php`; `support\routes\console.php` |
| Support System | Relay / Hotline | HTTP `POST /api/v1/messages` via Relay | `support.request.received`, `accepted`, `rejected`, `assigned`, `en_route`, `fulfilled`, `closed` lifecycle updates | Support operator handling | `support\app\Services\SupportRequestLifecycleRelayService.php`; `support\app\Http\Controllers\Api\SupportRequestsController.php` |
| Backend apps | Relay | HTTP `POST /api/v1/relationships/resolve` | Source/target hub IDs, purpose, returned relationship credentials | Backend credential resolution | `relay\routes\api.php`; `relay\app\Http\Controllers\Api\Relay\Credentials\RelationshipController.php`; `relay\app\Relay\Credentials\RelayRelationshipResolver.php` |
| Support System / backend consumers | Hotline | HTTP `POST /api/internal/sitrep/media/manifest`, `GET /api/internal/sitrep/media/{kind}/{id}` | Media metadata and authorized downloads for SITREP evidence | Optional drill-down evidence access | `hotline\docs\sitrep-media-access-contract.md`; `hotline\packages\pbb-hotline-media-sdk`; `hotline\tests\Feature\Internal\SitrepMediaAccessTest.php` |
| Support System | MapServer | HTTP tile/boundary URLs | Map style/tile/boundary GETs | Support map display | `support\app\Support\Maps\MapServerUrls.php`; `support\app\Http\Controllers\Api\SupportMapController.php` |
| Support System | Relay hub JSON | HTTP GET | Hub identity/uplink data | SITREP consolidation and relay target selection | `support\app\Services\SitrepConsolidationService.php`; `support\config\services.php`; `support\.env.example` |
| Kit Setup | Landing | HTTP `/internal/registry/apps/{appId}` with bearer token | App launch/health/gateway registry records | App install/update registration | `landing\README.md`; `landing\src\Auth.php`; `landing\src\RegistryStore.php` |
| Landing | Relay hub JSON | Local file read | Hub identity and public projection source | Public `/.well-known/pbb-hub.json` rendering | `landing\config\landing.php`; `landing\src\HubSource.php`; `landing\src\HubProjection.php` |
| Landing | Relay | HTTP cURL gateway for `/relay/api/v1/*` | Allowed machine-to-machine Relay API traffic | Peer/backend calls through public hub domain | `landing\src\Gateway.php`; `landing\src\App.php`; `landing\storage\registry.json` |
| PBB Chat | PBB Realtime | HTTP backend ingest/admission and browser WebSocket SDK | Room/direct/notification admission and chat events | Local chat and safety actions | `chat\app\Services\ChatRealtimeEventPublisher.php`; `chat\app\Http\Controllers\Api\ChatRealtimeController.php`; `chat\config\chat.php` |
| PBB Chat | PBB Hotline | HTTP/browser handoff URL response | Chat user context, optional summary/context flag, configured Hotline URL | `POST /api/chat/escalate-to-hotline` | `chat\app\Http\Controllers\Api\HotlineEscalationController.php`; `chat\config\chat.php` |
| PBB Chat | PBB Helper | Vendored JS/CSS UI contracts | Chat thread/composer/login/file/icon UI surfaces | Browser rendering | `chat\README.md`; `hotline-helpers\js\ui\ui.loader.js`; `hotline-helpers\CHANGELOG.md` |
| PBB Games | PBB Helper | Vendored JS/CSS/game components | Launcher/game UI components and styles | Browser rendering | `games\README.md`; `games\assets\helper`; `hotline-helpers\js\ui\ui.game.state.chrome.js` |
| PBB Games | PBB Hotline | Configured URL/link only | Emergency guidance/navigation target | Emergency/disabled mode messaging | `games\config\games.php`; `games\README.md` |
| Relay | Relay peers | HTTP `/api/v1/receive`, `/api/v1/receive-batch`, upload APIs | Relay envelopes, attachments, receipts | Delivery worker | `relay\routes\api.php`; `relay\config\relay.php` |
| Local apps | Relay | HTTP `/api/v1/messages`, inbox/handler/upload APIs | Local relay envelopes and files | App submission | `relay\routes\api.php` |
| Relay | Hub/HQ | HTTP HQ registry/heartbeat APIs | Registry sync, heartbeat | CLI/worker/scheduled external process | `relay\config\relay.php`; `relay\app\Console\Commands` |
| Hub/HQ | Relay/node heartbeat URLs | HTTP status checks | Health request/response | Laravel scheduler | `hub.ph\routes\console.php`; `hub.ph\app\Services\HubHeartbeatChecker.php` |
| Relay | Maestro | HTTP telemetry endpoints | Worker heartbeat/events | Relay worker telemetry | `relay\config\relay.php`; `maestro\routes\api.php` |
| Realtime | Maestro | HTTP telemetry endpoints | Gateway heartbeat/dispatch stats | `realtime:serve` timers | `realtime\config\realtime.php`; `realtime\RealtimeServeCommand.php` |
| Realtime | Product backend | HTTP forwarding | Media chunks, product queries | WebSocket media/query events | `realtime\app\Realtime\Media`; `realtime\app\Realtime\ProductQuery` |
| Kit Setup | App installers | CLI/PHP/file operations | install config, package manifests, reports | Setup/update workflow | `kit-setup\src\KitSetupRunner.php`; `kit-setup\package.json` |
| Kit Setup | Relay and Support Data Prep | Generated config/secrets | `hotline.command`, `sitrep.ingestor`, `support.dispatch`, Support Source Heartbeats webhook token | Node Data Prep | `kit-setup\docs\relay-hotline-support-data-prep-contract.md`; `kit-setup\src\KitSetupRunner.php` |
| Chatviewer | MySQL `pbb_agentchat` | PDO | Chat entries, agents, recipients, topics | Agent chat API/UI reads | `chatviewer\src\Db.php`; `chatviewer\src\ChatRepository.php` |
| Chatviewer | PBB root | Local file read fallback | `chat_log.md` parsed into JSON | Browser API request when DB unavailable | `chatviewer\api\chat-log.php` |

## 5. Shared Database / Shared Storage Map

| Resource | Used By | Purpose | Evidence |
|---|---|---|---|
| Separate Laravel databases | Hotline, Relay, Hub, Maestro, Realtime | Each app has own migrations and `.env.example` DB config | each app `database\migrations`; `.env.example` |
| `storage/app` and public storage | Hotline | Attachments/media/SITREP artifacts; media API returns authorized downloads rather than raw paths | Hotline migrations and media controllers; `hotline\docs\sitrep-media-access-contract.md` |
| `storage/app/relay/hub-credentials.json` | Relay | Relay-private relationship credential snapshot for resolver | `relay\config\relay.php`; `relay\app\Relay\Credentials\RelayHubCredentialStore.php` |
| `storage/tiles` | MapServer | Tile cache | `mapserver\config.php`; `mapserver\README.md` |
| `storage/boundaries` | MapServer | Generated GeoJSON overlays | `mapserver\README.md` |
| `resources/boundaries` | MapServer | Vendored PSGC boundary sources | `mapserver\resources\boundaries` |
| `storage/app/realtime-media-chunks` | Realtime | Media chunk spool path default | `realtime\config\realtime.php` |
| `public/vendor/helpers.pbb.ph` / vendored helper folders | Hotline, Hub, Maestro, MapServer, Chatviewer | Shared UI library | file inventories |
| `C:\wamp64\www\pbb\chat_log.md` | Chatviewer | Source chat log | `chatviewer\api\chat-log.php` |
| `packages/bundled/*.zip` | Kit Setup | Bundled app installation packages | `kit-setup\package.json` |
| `packages/packages.bundled.json` | Kit Setup | Trusted bundled package manifest for Landing, MapServer, Maestro, Realtime, Relay, Hotline, Support | `kit-setup\packages\packages.bundled.json`; `kit-setup\package.json` |
| `pbb_agentchat` database | Chatviewer | Agent identities, chat entries, recipients, revisions, topics, audit | `chatviewer\src\ChatRepository.php`; `chatviewer\src\Db.php` |
| `relay_webhook_subscribers`, `relay_webhook_deliveries` | Relay | Operational webhook subscribers and delivery state | `relay\tools\data-prep\apply-settings.php`; `relay\tests\Feature\Relay\RelayHeartbeatTest.php` |
| `pbb_support` database tables | Support System | Relay inbound SITREPs, staged/current consolidated SITREPs, support requests/messages/actions, relay deliveries, settings | `support\database\migrations`; `support\.env.example` |
| `support_settings` encrypted values | Support System | Relay URL/token, handler token, source/target systems, map/realtime settings | `support\app\Support\Settings\SupportSettings.php`; `support\database\migrations\2026_06_12_000001_create_support_settings_table.php` |
| `landing\storage\registry.json` | Landing, Kit Setup | App launcher, health, and public gateway registry records | `landing\config\landing.php`; `landing\src\RegistryStore.php`; `landing\README.md` |
| `landing\storage\logs\*.log` | Landing | Registry audit and gateway logs | `landing\config\landing.php`; `landing\src\RegistryStore.php`; `landing\src\Gateway.php` |
| `C:\wamp64\www\pbb\relay\public\hub.json` | Landing, Support System, Kit Setup context | Hub identity/public projection source and topology context | `landing\config\landing.php`; `support\.env.example`; owner clarification |
| `pbb_chat` database | PBB Chat | Local rooms, memberships, messages, direct conversations, message requests, reports/blocks, badges, sessions/queues | `chat\.env.example`; `chat\database\migrations`; `chat\routes\web.php` |
| `chat\public\openapi\pbb-chat.yaml` | PBB Chat | Chat API baseline documentation | `chat\README.md`; file inventory |
| `games\config\games.php`, `games\config\games.registry.php` | PBB Games | Mode policy and local game registry | `games\config\games.php`; `games\config\games.registry.php`; `games\health.php` |
| `games\assets\helper` | PBB Games | Vendored Helper UI/game assets | `games\README.md`; file inventory |
| `C:\wamp64\www\pbb\account` | Unknown / Not confirmed from code | Folder exists but no app files were found in scan | local folder inventory |

## 6. Offline-First Architecture Summary

Confirmed local/LAN-capable parts:

- Hotline can run as a local Laravel app with local DB; citizen offline UI exists.
- Realtime can run on local bind/port and serve LAN WebSocket clients.
- Relay stores messages/deliveries locally and retries upstream delivery.
- Maestro monitors local workers over LAN HTTP telemetry.
- MapServer serves cached/pre-populated map tiles and generated boundaries locally.
- Chatviewer is fully local.
- Helper is static and vendored.
- Support System can run locally/LAN with its own MySQL database after Relay has delivered data; outbound sync requires local Relay availability.
- Landing can serve the local launcher and read local registry/hub JSON files without internet.
- PBB Chat can run as a local/LAN Laravel app with local `pbb_chat` database and local Realtime; public gateway exposure is disabled in `release.json`.
- PBB Games can run locally after install with static/PHP assets and no database; emergency/active-incident mode behavior is local config-driven.
- Kit Setup can install bundled ZIPs locally.
- Owner clarification: Setup Data Prep populates MapServer tiles for the Hub HQ-defined boundary during node installation.

Requires upstream/internet/cloud connectivity:

- MapServer cache misses use upstream tile providers unless local base URLs/cache are configured.
- Relay hub-to-hub delivery needs peer reachability.
- Relay HQ registry/heartbeat needs Hub/HQ reachability.
- Hub/HQ heartbeat visibility requires nodes to reach Hub/HQ.
- Landing public metadata/gateway behavior requires the public hub domain/tunnel path to be reachable.
- Support System upstream SITREP and support lifecycle delivery require Relay and upstream topology reachability.
- PBB Chat live updates require local PBB Realtime availability. Internet/cloud is not confirmed as required; public gateway is explicitly disabled.
- PBB Games has no confirmed cloud/upstream dependency after install.
- Kit Setup validates local WampServer and Technitium DNS prerequisites. DNS apply/verify can use Technitium locally; SSL/remote-check/provider-specific steps may still need network depending on configuration.

Confirmed retry/queue behavior:

- Relay delivery and local handler retry/backoff.
- Realtime backend event and media chunk pending/failed/published or forwarded state.
- Hotline has Laravel queue/scheduler, stale media finalization, confirmed SITREP Relay delivery/outbox support, and confirmed Support Request relay submission/lifecycle services. A durable browser-side citizen outbox was still not confirmed.
- Support System has database queue usage for SITREP relay delivery and support request lifecycle delivery jobs. Landing has no queue/retry layer; Relay handles store-and-forward behind it.
- Relay source-heartbeat operational webhooks are queued/tracked in `relay_webhook_deliveries`; Support accepts duplicate protection by `event_id`.
- PBB Chat has local DB persistence and Realtime publish skipping/logging when backend ingress is not configured; durable browser-side offline compose queues and Relay sync were not confirmed.
- PBB Games has no retry/queue layer because it does not manage operational sync data.

Conflict handling:

- Relay has idempotency/receipts. Owner clarification: cross-node incident edit conflict resolution is not a major expected path because incidents are not synced as editable records between different nodes.
- Realtime has cached responses for duplicate envelope IDs inside the gateway. Cross-process conflict logic was not confirmed.
- Hotline incident cross-node conflict handling was not confirmed and is not currently expected to be central; periodic upstream SITREP consolidation is the confirmed intended flow.

## 7. Relay and Sync Architecture Summary

Confirmed sync path:

```text
Local app
-> Relay local client API `/api/v1/messages`
-> `hub_relay_messages`
-> `hub_relay_deliveries`
-> queue job `ProcessRelayDelivery`
-> target Relay `/api/v1/receive` or `/receive-batch`
-> target `hub_relay_receipts` idempotency
-> optional local handler dispatch
```

What is synced:

- Generic relay envelopes with message type, source system, source hub, target hubs, payload JSON, tags, attachments, priority, and correlation/reference IDs.
- Attachments through attachment/upload session tables.
- HQ registry hub/link snapshots and heartbeat status.
- Hotline latest SITREPs and support requests are submitted through Relay-facing services.
- Support System receives SITREPs/support requests through Relay handlers. The current confirmed identities are `sitrep.ingestor` for `sitrep.record` and `support.dispatch` for `support.request` / `support.request.cancelled`.
- Support submits consolidated `sitrep.record` upstream and submits support lifecycle updates back through Relay.
- Relay emits `source.heartbeat.updated` operational webhooks outside the normal `hub_relay_messages` app-to-app path.
- Landing can expose a public `/relay/api/v1/*` gateway to the registered local Relay target, but it does not implement the store-and-forward queue itself.

Known gaps:

- Hotline-to-Relay latest-SITREP handoff and Support Request envelopes are now confirmed from post-briefing code/chat-log updates. Broad editable incident replication remains intentionally outside the current design; incidents are consolidated into SITREPs and support workflows rather than synced as competing mutable incident records across nodes.
- Support Request lifecycle uses `support.request` plus `support.request.received`, `support.request.under_review`, `support.request.accepted`, `support.request.rejected`, `support.request.assigned`, `support.request.en_route`, `support.request.fulfilled`, and `support.request.closed`. `support.request.completed` should not be emitted.
- Relay relationship resolution is backend-only. It accepts source hub, target hub, and purpose, then returns relationship credentials and topology data. Returned shared keys must never be exposed to browser code.
- Relay resolver `domain` is topology domain from `hub.json`; it is not a Hotline media base URL. A separate app endpoint metadata contract is still needed before remote Hotline media fetches can be fully automated.
- Conflict resolution beyond idempotent receipt/content hash was not confirmed in code; owner clarification reduces this risk for incidents because nodes send periodic SITREPs upstream rather than syncing editable incident records across nodes.
- Relay topology can be manual or HQ-driven; owner clarification confirms Hub HQ-provided node identity and a barangay->city/municipality->province->cloud HQ topology concept, but the full production field mapping still needs implementation documentation.
- PBB-managed FRP tunnel implementation details were not confirmed in the reviewed code. Owner clarification confirms the intended design: Hub HQ control plane, FRPS tunnel server, FRPC clients on local nodes, and cloud-visible Relay endpoints without static IPs, port forwarding, Cloudflare, or other external tunnel providers.

## 7A. Planned Responder / Helper Mobile Flow

Owner-confirmed planned workflow; implementation was not found in the reviewed repositories:

```text
Citizen report / Hotline intake
-> PBB command or hotline validation
-> responder/helper assignment
-> mobile app receives incident type, location, priority, notes, photos, safety instructions
-> responder acknowledges assignment
-> status updates: accepted -> en route -> on scene -> assistance provided -> resolved
-> field notes/photos/outcome report
-> local PBB network operation while internet is unavailable
-> PBB Relay sync upward when connectivity returns
-> command/citizen feedback loop
```

Primary intended users: barangay responders, rescue teams, medical responders, and utility helpers.

## 7B. Planned PBB-Managed Tunnel Flow

Owner-confirmed planned topology; implementation was not confirmed in the reviewed code:

```text
Hub HQ control plane
-> provisions/coordinates Relay tunnel endpoint
-> FRPS tunnel server
-> FRPC client on each local PBB node
-> cloud-visible secure endpoint for each PBB Relay node
```

Design constraints from owner clarification:

- No external tunnel provider dependency by design.
- No static IP requirement for local nodes.
- No router port forwarding requirement.
- No Cloudflare dependency.

## 8. Realtime Architecture Summary

Confirmed Realtime users:

- Hotline uses Realtime admission routes and SDK.
- PBB Chat uses Realtime admission for room/direct/notification flows and backend ingest publishing for chat events.
- Support System publishes accepted source heartbeat snapshots to Realtime support rooms.
- Realtime is product-app agnostic and exposes PHP backend SDK/docs.

Confirmed WebSocket events/channels:

- `session.auth.request`
- `room.join.request`
- `room.leave.request`
- `session.health.request`
- `presence.subscribe`
- `presence.publish`
- `app.event.publish`
- `media.chunk.prepare`
- `media.chunk.publish`
- `chat.message.publish`
- `sandbox.attachment.chunk.publish`
- `call.signal.publish`
- `ping`

Known behavior:

- Browser clients use signed short-lived tokens and capabilities.
- Rooms and presence rosters are in memory in the `realtime:serve` process.
- Backend-published events and media chunk outcomes persist in DB and are drained by the gateway.
- Multiple gateway processes do not share in-memory state. Owner clarification: the production expectation is one shared Realtime gateway instance per node.
- WebRTC signaling is supported as `call.signal.publish`; Realtime itself is not the media SFU/MCU in inspected code.
- PBB Chat room naming includes `chat.thread.pbb-chat.{room_uuid}` and allowed prefixes include `chat.thread.` and `chat.direct.`.
- PBB Games has no realtime functionality confirmed.

## 9. Mapping Architecture Summary

Confirmed map architecture:

```text
Hotline MapLibre frontend
-> MapServer `/tiles/vector/*`, `/tiles/glyphs/*`, `/tiles/terrain/*`, `/tiles/poi/*`
-> MapServer cache under `storage/tiles`
-> upstream providers on cache miss
```

Boundary overlays:

```text
Client
-> MapServer `/boundaries/{scope}/{code}.geojson`
-> resources/boundaries shapefile/CSV
-> generated cache under `storage/boundaries/http`
-> GeoJSON response with ETag/Last-Modified/CORS/cache headers
```

Offline map behavior exists only for cached/prepared tiles and local boundary resources. Complete offline coverage depends on tile population/data prep.

Owner clarification: during node installation, Kit Setup Data Prep is expected to auto-populate MapServer with tiles for the boundary dictated by Hub HQ hub information.

No mapping/geolocation functionality was confirmed in PBB Chat or PBB Games.

## 10. Kit Setup / Provisioning Flow

Confirmed from code:

```text
Kit Setup desktop UI / config builder
-> admin-provided Hub HQ hub ID and hub token validation
-> Hub HQ hub information / node identity
-> kit config JSON
-> PHP runner `bin/kit-setup.php`
-> package preparation from bundled/local/archived packages
-> app installer execution
-> DNS plan/apply/verify
-> SSL/vhost plan/apply
-> firewall apply
-> service plan/start/stop/verify
-> data prep, including MapServer tile population for Hub HQ-defined boundary
-> smoke checks/stage reports
-> update plan/apply
```

Bundled packages in `package.json` include MapServer, MapServer boundary pack, Maestro, Realtime, Relay, and Hotline. Kit Setup delegates app-specific database initialization and `.env` writing to app installers where those installers exist.

Owner clarification: during node installation, all PBB processes are registered as Windows services. Maestro observes these processes; it is not the service recovery controller.

## 11. Monitoring and Maintenance Flow

Confirmed:

```text
Relay or Realtime worker process
-> Maestro telemetry endpoint with app_code and telemetry token
-> Maestro validates token hash and app_code
-> upserts `maestro_workers`
-> records `maestro_worker_events`
-> scheduled `maestro:reconcile-stale-workers`
-> UI/API lists worker status/events
```

Maestro computes worker status as starting/idle/busy/stale/stopped. Owner clarification: Maestro is strictly observer-only; Kit Setup/Windows services own service lifecycle and recovery behavior.

## 12. Security and Privacy Summary

| Area | Finding | Affected Apps | Severity | Evidence | Recommended Action |
|---|---|---|---|---|---|
| Incident/location/media data | Hotline stores citizen identity, incident, live location, messages, attachments, and media locally | Hotline | High | Hotline migrations | Define retention, encryption, backup, access control |
| Realtime token security | Realtime admission depends on signing secret, issuer, capability, room policy | Realtime, Hotline | High | `realtime\config\realtime.php`; `RealtimeGateway.php` | Rotate secrets and test room/capability policies |
| Relay API/transport secrets | Relay uses local client API keys, hub shared/HMAC/mtls modes, handler auth tokens | Relay | High | `relay\config\relay.php`; migrations | Hash/rotate secrets; prefer HMAC/mtls for hub peers |
| Relay relationship resolver credentials | Resolver returns relationship credentials for backend clients | Relay, consuming backend apps | High | `relay\routes\api.php`; `RelationshipController.php`; `RelayRelationshipResolver.php` | Keep backend-only; never expose returned shared keys to browser code |
| SITREP media access token | Hotline media manifest/download API is token-protected and evidence-oriented | Hotline, Support System | High | `hotline\docs\sitrep-media-access-contract.md`; `hotline\packages\pbb-hotline-media-sdk` | Rotate token, audit access, define media cache/purge policy in consumers |
| Support default admin credential | Support README documents default `admin@support.pbb.ph` / `password` login for local development | Support System | High if shared deployment keeps default; Low for dev only | `support\README.md` | Force password change or disable seeded default before shared deployment |
| Support handler/client tokens | Support rejects inbound Relay handler calls without matching bearer token and uses Relay client token for outbound messages | Support System, Relay | High | `support\app\Http\Controllers\Api\RelaySitrepHandlerController.php`; `support\app\Services\SitrepRelaySubmissionService.php`; `support\app\Support\Settings\SupportSettings.php` | Rotate, store encrypted, mask in UI/logs, and document token ownership |
| Support source heartbeat webhook token | Relay operational webhook uses a dedicated Support token and Support accepts bearer or `X-Relay-Webhook-Key` | Relay, Support System, Kit Setup | High | `kit-setup\docs\relay-hotline-support-data-prep-contract.md`; `RelaySourceHeartbeatController.php`; `relay\tools\data-prep\apply-settings.php` | Generate per-node token through Kit, keep server-side only, rotate on compromise |
| Landing registry token | Landing internal registry writes are bearer-token protected by `PBB_LANDING_REGISTRY_TOKEN_HASH` | Landing, Kit Setup | High | `landing\src\Auth.php`; `landing\README.md`; `landing\config\landing.php` | Generate per-node token in Kit Setup, store only hash, and keep `/internal/*` local-only |
| Landing Relay gateway exposure | Landing forwards `/relay/api/v1/*` only for allowed peers and registry-enabled M2M gateway config | Landing, Relay | High | `landing\src\Gateway.php`; `landing\src\RegistryStore.php`; `landing\tests\run.php` | Keep peer-domain allowlist strict and document gateway threat model with FRP/public DNS setup |
| Chat local community data | Chat stores local messages, direct conversations, reports, blocks, badges, and session data | PBB Chat | High | `chat\database\migrations`; `chat\routes\web.php` | Define retention, moderation access, backup, and purge policy |
| Chat public exposure should remain disabled | Chat release metadata disables public gateway exposure | PBB Chat, Landing, Kit Setup | Medium | `chat\release.json` | Keep Chat LAN-only unless a separate public security model is designed |
| Games emergency distraction risk | Games has active-incident and emergency modes but mode changes are config-driven | PBB Games | Medium | `games\config\games.php`; `games\src\ModePolicy.php` | Document/automate operator mode switching during active incidents |
| Hub tokens | Hub machine endpoints use token auth | Hub/HQ, Relay | High | `hub.ph\routes\api.php`; `hub_tokens` migration | Rotate tokens and scope access |
| Map provider keys | MapServer requires provider API keys for defaults | MapServer | Medium | `mapserver\config.php` | Keep keys out of source and mask diagnostics |
| Admin surface role gaps | Hub geodata coordinate updates are auth-only; Maestro management APIs auth-only | Hub/HQ, Maestro | Medium | `hub.ph\routes\api.php`; `maestro\routes\api.php` | Confirm intended permissions and add roles where needed |
| Chatviewer read API exposure | Chatviewer read APIs are unauthenticated, while posting/claiming uses project tokens | Chatviewer | Low if kept local/private; Medium if shared | `chatviewer\api\chat-log.php`; `chatviewer\api\chat-entries.php`; `ChatRepository.php` | Keep LAN/local/private or add read auth before broader deployment |
| No auth on MapServer static endpoints | MapServer public endpoints have no app auth | MapServer | Medium for shared deployments | `mapserver\README.md` | Keep LAN/local/private or add reverse proxy restrictions |
| Installer admin privileges | Kit Setup installer requests admin rights | Kit Setup | High | `kit-setup\package.json` | Use signed packages, verify manifests, mask reports |

## 13. System-Wide Technical Gaps

| Gap | Affected Apps | Impact | Recommended Priority |
|---|---|---|---|
| Broad editable incident sync is intentionally not the current design | Hotline, Relay, Hub | Future docs should avoid describing incidents as replicated mutable records across nodes | Medium |
| SITREP/support upstream contracts still need endpoint/runbook documentation | Relay, Hotline, Hub, Support System, Kit Setup | Code confirms role identities and message names, but production schedule, endpoint metadata, and runbook details need stable documentation | High |
| Support role enforcement needs review | Support System | Admin users/settings routes are auth-protected, but route-level role middleware was not confirmed in the reviewed code | High |
| Landing registry/gateway contract needs final operational docs | Landing, Kit Setup, Relay, Hub/HQ | Registry fields, gateway peer rules, public projection, and FRP/vhost ownership are security-sensitive deployment contracts | High |
| Cross-app endpoint metadata missing for media access | Relay, Hotline, Support System, Hub/HQ | Relay resolver provides topology/credentials but not the source Hotline media base URL | High |
| Planned responder/helper implementation not yet mapped to code | Hotline, Relay, Realtime, planned responder app | Assignment/status/field-report contracts need implementation docs and tests | High |
| Planned PBB-managed FRP tunnel gateway not yet mapped to code | Hub/HQ, Relay, Kit Setup, Landing | Secure cloud-visible Relay and Landing public endpoint lifecycle needs implementation docs and operational runbook | High |
| Realtime in-memory rooms/presence not shared across processes | Realtime, Hotline | Acceptable for owner-confirmed one-instance-per-node topology; failover restarts still lose live state | Medium |
| Map offline completeness depends on successful Setup Data Prep | MapServer, Hotline, Kit Setup | Field kits may have map gaps if tile/boundary preflight is skipped or fails | High |
| Service recovery runbook not documented here | Maestro, Kit Setup, Relay, Realtime | Owner clarified Maestro observes only and Windows services own lifecycle; operators still need recovery expectations | Medium |
| Role/permission model differs by app | Hotline, Hub, Relay, Realtime, Maestro | Security review complexity | Medium |
| Chatviewer has authenticated writes but unauthenticated reads | Chatviewer | Sensitive internal notes if exposed beyond the owner's local/personal use | Low if kept local/private; Medium if shared |
| PBB Chat offline scope is not fully specified | PBB Chat, Realtime, Landing | LAN-only operation is confirmed, but durable offline message queueing and Relay sync were not confirmed | Medium |
| PBB Games emergency mode operation needs a runbook | PBB Games, Kit Setup/Landing context | Games should not compete with emergency workflows during active incidents | Medium |
| PBB Account folder is unconfirmed | Account/future identity apps, Chat | Folder exists but no app files were found; shared account behavior should not be assumed | Low |
| Documentation mixes proposals and implemented code | Helper, Realtime, Relay, Hotline | Future readers may over-assume features | Medium |

## 14. Recommended Next Technical Priorities

| Priority | Recommendation | Reason | Affected Apps |
|---|---|---|---|
| 1 | Publish the finalized Relay/Hotline/Support Data Prep and runtime contract as an operator runbook | The two Support identities and source heartbeat webhook are now implemented and should replace older generic incident-sync assumptions | Hotline, Relay, Hub, Support System, Kit Setup |
| 2 | Define app endpoint metadata for cross-node Hotline media access | Relay relationship resolver returns credentials/topology but not a source app URL | Hub/HQ, Relay, Hotline, Support System |
| 3 | Finalize Landing registry, public projection, and Relay gateway deployment contract | Landing is now confirmed as the public-safe hub metadata and M2M Relay gateway surface | Landing, Kit Setup, Relay, Hub/HQ |
| 4 | Document the Hub HQ `hub.json` identity contract and Kit Setup hub ID/token validation flow | Hub HQ provides node identity and Kit Setup validates installation from admin-provided credentials | Hub, Relay, MapServer, Kit Setup, Landing, Support System |
| 5 | Review and harden Support System authorization boundaries and webhook token rotation | Support has auth-protected admin/settings routes and new webhook credentials; route-level role middleware was not confirmed | Support System, Relay, Kit Setup |
| 6 | Define the responder/helper mobile assignment, status, field-report, and sync API contract | Owner clarified the intended field response loop; implementation is not confirmed in reviewed code | Hotline, Relay, Realtime, planned responder app |
| 7 | Document the PBB-managed FRP tunnel gateway control-plane and node-client lifecycle | Owner clarified the tunnel model and non-dependence on external tunnel providers | Hub/HQ, Relay, Kit Setup, Landing |
| 8 | Harden machine/API secrets and diagnostics | Several services carry high-impact local credentials | Relay, Realtime, Hub, Maestro, MapServer, Support System, Landing |
| 9 | Automate or document offline map preflight after Setup Data Prep | MapServer only serves cached/prepared tiles offline, and Data Prep should verify Hub HQ-defined coverage | MapServer, Hotline, Kit Setup, Support System |
| 10 | Document Maestro as observer-only and Windows service recovery as service-layer responsibility | Current code monitors; owner clarified Kit Setup/Windows services manage lifecycle | Maestro, Kit Setup |
| 11 | Decide and document PBB Chat offline/sync boundary | Code confirms LAN/local Realtime chat, but not durable offline queues or Relay synchronization | PBB Chat, Realtime, Landing |
| 12 | Add a Games emergency-mode operator runbook | Games has safety modes but no confirmed runtime admin control | PBB Games, Kit Setup, Landing |

## 15. Questions for Project Owner

| Question | Why It Matters |
|---|---|
| What are the exact fields in the Hub HQ `hub.json` identity contract that each local app must consume? | Owner clarified Hub HQ provides node identity; implementation docs still need the per-field contract. |
| What is the exact periodic SITREP envelope, schedule, and idempotency key? | Owner clarified incidents are not synced across nodes; SITREP consolidation is the upstream flow. |
| Should Support System be deployed only at upper-level/HQ-side support nodes, or also on barangay node kits? | Code supports a local Laravel deployment, but operational placement affects Relay topology, users, and data retention. |
| Which Landing registry fields are authoritative from Kit Setup versus editable by future admin UIs? | Landing currently treats registry as Kit-managed node metadata; ownership affects update safety. |
| Which public host path should expose Landing and Relay through the PBB-managed FRP tunnel? | Landing reads hub domain and can gateway Relay; FRP control-plane details were owner-confirmed but not code-confirmed. |
| Should Hotline work with a durable browser-side offline incident queue, or only local server/LAN offline? | Current code confirms local/LAN and frontend offline handling, not durable browser outbox. |
| Which data must be retained, archived, encrypted, or purged for incidents, media, chat, and location? | Sensitive emergency data is stored locally. |
| What pass/fail thresholds should the offline map preflight enforce after Setup Data Prep? | Owner clarified Setup Data Prep populates MapServer tiles from Hub HQ boundary data; deployment needs objective validation. |
| Should PBB Chat ever synchronize messages upstream, or is it strictly local/LAN-only community chat? | Current code confirms local/LAN Chat with Realtime and public gateway disabled, not Relay sync. |
| Should PBB Games mode be controlled manually by config, by Kit Setup, or by a local incident state signal? | Code confirms mode policy but not a runtime control source. |
| Is `C:\wamp64\www\pbb\account` intended to become a shared account/identity app? | Folder exists but no app files were found; future Chat identity integration should not be inferred. |

## 16. Evidence Addendum For 2026-06-29 Alignment

- `C:\wamp64\www\pbb\chat\README.md`
- `C:\wamp64\www\pbb\chat\release.json`
- `C:\wamp64\www\pbb\chat\.env.example`
- `C:\wamp64\www\pbb\chat\routes\web.php`
- `C:\wamp64\www\pbb\chat\config\chat.php`
- `C:\wamp64\www\pbb\chat\app\Services\ChatRealtimeEventPublisher.php`
- `C:\wamp64\www\pbb\chat\app\Http\Controllers\Api\ChatRealtimeController.php`
- `C:\wamp64\www\pbb\chat\app\Http\Controllers\Api\HotlineEscalationController.php`
- `C:\wamp64\www\pbb\chat\app\Http\Controllers\Api\ChatSafetyController.php`
- `C:\wamp64\www\pbb\chat\database\migrations`
- `C:\wamp64\www\pbb\games\README.md`
- `C:\wamp64\www\pbb\games\health.php`
- `C:\wamp64\www\pbb\games\config\games.php`
- `C:\wamp64\www\pbb\games\config\games.registry.php`
- `C:\wamp64\www\pbb\games\src\ModePolicy.php`
- `C:\wamp64\www\pbb\games\src\GameRegistry.php`
- `C:\wamp64\www\hotline-helpers\js\ui\ui.loader.js`
- `C:\wamp64\www\hotline-helpers\CHANGELOG.md`
- `C:\wamp64\www\pbb\kit-setup\package.json`
- `C:\wamp64\www\pbb\kit-setup\packages\packages.bundled.json`
