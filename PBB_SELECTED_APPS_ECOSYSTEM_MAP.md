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
- Natalium is a Laravel 12 local health app for patient registry, health programs, care workflow, maternal/child health, birth-defect surveillance, referrals, prescriptions, citizen/staff surfaces, reporting/export, operations, and Account SSO/app-admin sync.
- Utility / Vena is a Laravel utility-operator app that consumes Hotline incident snapshots through Relay, normalizes utility incidents, supports assets/teams/settings, and coordinates operator/responder missions.
- Salus is an in-progress Laravel 12 Auxilus Mos local evacuation-center and relief-distribution module with implemented routes/controllers/models/migrations for multiple center activations, registration, movement/occupancy, relief-batch receipt tracking, citizen QR status, Realtime updates, and Hotline aggregate-summary access.
- Library is now implemented as Laravel 12 Library Cloud plus Laravel 12 Library Node, with shared PHP/JS packages. Cloud handles governed ingestion, canonical content, signing/releases, node distribution, fleet reports, and patron/admin surfaces. Node handles offline catalog/search/resource serving, SDK/embed access, Account hooks, release/storage/trust state, admin health/settings, and local content delivery.
- Learning / Lumaria is now implemented as a Laravel 12 local LMS-agnostic learning gateway with Account SSO/app-admin hooks, provider/catalog/instance APIs, admin diagnostics/settings, emergency-state APIs, OpenAPI/docs routes, Vite assets, and `pbb_learning` migrations.

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
- 2026-07-07 correction: PBB Account at `C:\wamp64\www\pbb\account` is now reviewed as a Laravel 12 local node identity and SSO service. Code confirms canonical `accounts`, trusted clients, hashed one-time SSO authorization codes, Account admin surfaces, app-admin provisioning hooks, session identity APIs, optional Realtime admission, a PHP SDK, and seeded development clients for Chat, Hotline, and Landing.
- 2026-07-07 alignment: PBB Natalium at `C:\wamp64\www\pbb\natalium` was then reviewed as an earlier legacy PHP foundation; 2026-07-24 supersedes this with the current Laravel implementation.
- 2026-07-07 alignment: PBB Utility / Vena at `C:\wamp64\www\pbb\utility` is reviewed as a Laravel 12 app using MySQL `pbb_utility`, roles `admin`, `operator`, `command`, and `responder`, MapLibre map config, and inbound-only Relay intake for `hotline.incident.upserted` targeted to `utility.vena`.
- 2026-07-07 older-app Account rescan: Hotline, Relay, Chat, Support, Realtime, and Maestro now have Account SSO redirect/callback/logout code and/or Account app-admin APIs using local `users.pbb_user_id` links. Landing has Account launcher/session UI behavior and registry metadata for `pbb-account`. Kit Setup app-installer docs now codify Account SSO/app-admin environment names, app-admin token requirements, the `X-PBB-Account-Client: pbb-account` header, and `users.pbb_user_id` migration expectations. Hub/HQ and MapServer did not show PBB Account SSO/app-admin integration in this targeted scan.
- 2026-07-12 Salus implementation alignment: PBB Salus at `C:\wamp64\www\pbb\salus` is now reviewed as an in-progress Laravel 12 app, not only a design document set. Local files confirm `artisan`, `composer.json`, `package.json`, `app`, `routes`, `database`, `public`, `resources`, `tests`, and `vendor` are present. `docs\implementation-checklist.md` reports implementation through milestone 19, 87 implemented API paths documented, Laravel foundation complete, operational modules implemented, Account/Realtime/Hotline contracts implemented, and release/installation not started. Local `npm run openapi:check` passed and confirmed OpenAPI coverage for 87 implemented API paths. V1 still explicitly has no direct Relay, Support/Imperium, MapServer, Maestro, full inventory, native mobile, continuous geofencing, or cross-node private evacuee sync.
- 2026-07-24 Library/Learning/Natalium alignment: `C:\wamp64\www\pbb\library`, `C:\wamp64\www\pbb\learning`, and `C:\wamp64\www\pbb\natalium` now contain Laravel implementations. Library is split into Cloud and Node Laravel apps with shared packages; Learning has Laravel routes/migrations/Account integration and V1 status docs; Natalium has moved to Laravel 12 with health-program/care-workflow/maternal/child/referral/reporting/operations modules.

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

### Product Names, Aliases, and Suite Grouping

Owner-provided naming clarification, 2026-07-24:

| Repository / App Name | Product Name / Alias | Product Group |
|---|---|---|
| PBB Library | Libria | Citizen Facing Apps |
| PBB Games | Tabulus | Citizen Facing Apps |
| PBB Chat | Civitas | Citizen Facing Apps |
| PBB Healthcare / PBB Natalium | Natalium | Citizen Facing Apps |
| PBB Learning | Lumaria | Citizen Facing Apps / Learning |
| PBB Hotline | Vox | Auxilos Mos bundle system |
| PBB Support System | Imperium | Auxilos Mos bundle system |
| PBB Utility | Vena | Auxilos Mos bundle system |
| PBB Salus | Salus | Local evacuation/relief module; current local docs also reference Auxilus Mos spelling |
| PBB Kit Setup | Kit Setup | Wizaya Server Suite deployment module |
| PBB Landing | PBB Landing | Wizaya Server Suite deployment module |
| PBB Account | PBB Account | Wizaya Server Suite core service |
| PBB Relay | PBB Relay | Wizaya Server Suite core service |
| PBB Realtime | PBB Realtime | Wizaya Server Suite core service |
| PBB Maestro | PBB Maestro | Wizaya Server Suite core service |
| PBB MapServer | PBB MapServer | Wizaya Server Suite core service |
| PBB Helper | PBB Helper | Development Tools |
| PBB Chatviewer | Syndicatum | Development Tools |
| PBB Hub / Hub HQ | Hub HQ | HQ/control-plane app |

## 2. App Classification

| App | Product / Alias | Category | Deployment Level | Primary Users | Primary Purpose |
|---|---|---|---|---|---|
| PBB Hotline | Vox | Citizen Apps, Operator Apps, Barangay Node Services, Auxilos Mos | Local node / barangay or command-center style node | Citizens, operators, command users, admins | Emergency reporting, calls, incident handling, assignments, SITREPs |
| PBB Helper | PBB Helper | Development Tools, Core UI library | Vendored into apps | Developers, consuming app frontends | Shared UI components for incident/admin/chat/media/map surfaces |
| PBB Relay | PBB Relay | Wizaya Server Suite Core Services | Local node and hub-to-hub relay nodes | Local apps, relay operators, hub peers | Store-and-forward messaging, delivery retries, inbox/handler dispatch, attachments |
| PBB Hub / Hub HQ | Hub HQ | HQ / Cloud Services, City/Municipality/Province Services | HQ/cloud or upper-level local node | Admins, hub machine clients | Hub registry, heartbeat, tokens, geodata, recovery |
| PBB Maestro | PBB Maestro | Wizaya Server Suite Core Services, Monitoring / Maintenance Tools | Local node | Operators/admins, monitored workers | Worker heartbeat/event monitoring and stale status computation |
| PBB Realtime Server | PBB Realtime | Wizaya Server Suite Core Services, Realtime Communication Services | Local node | Product apps, browsers, realtime admins | WebSocket gateway, admission, rooms, presence, chat, calls, media events |
| PBB MapServer | PBB MapServer | Wizaya Server Suite Core Services, Mapping Services | Local node | Client apps, setup operators | Tile cache/proxy, boundary overlays, offline map prep |
| PBB Chatviewer | Syndicatum | Development Tools, Monitoring / Maintenance Tools | Local development/PBB workspace | Project owner, PBB agents / development coordination | DB-backed agent chat, Markdown fallback, timeline/search, token-auth posting |
| PBB Kit Setup | Kit Setup | Wizaya Server Suite Deployment Modules | Windows setup machine / PBB Node Kit | Installer operator | Install/update/configure bundled PBB apps and services |
| PBB Support System | Imperium | Operator Apps, HQ / Cloud Services, Auxilos Mos | Support node / upper-level node / HQ-side support deployment | Support operators, receiving agencies, admins | SITREP consolidation and support request lifecycle handling |
| PBB Landing | PBB Landing | Wizaya Server Suite Deployment Modules | Local node and hub public domain surface | Local users, Kit Setup, peer nodes/backend clients | LAN app launcher, public hub metadata projection, Kit-managed app registry, Relay gateway |
| PBB Chat | Civitas | Citizen Facing Apps, Barangay Node Services, Realtime Communication Services | Local barangay node / LAN-only app | Citizens/local chat users, admins/moderators | Local rooms, direct messages, reports/blocks/moderation, badges, Hotline handoff |
| PBB Games | Tabulus | Citizen Facing Apps, Setup / Provisioning-adjacent local content | Local node / LAN-only optional app | Citizens/local visitors | Optional local games and emergency-preparedness learning activities |
| PBB Healthcare / PBB Natalium | Natalium | Citizen Facing Apps, Operator Apps, Barangay Node Services, Health / Continuity-of-Care Apps | Local health-center/barangay node | Health workers, practitioners, patient applicants/citizens, admins/management | Patient registry, health programs, care workflow, maternal/child health, birth defects, referrals, prescriptions, citizen/staff workflows, reporting/export, operations |
| PBB Utility | Vena | Utility Company Services, Operator Apps, Responder / Helper Apps, Auxilos Mos | Utility operator node / local PBB node | Utility admins, operators, command users, responders/helpers | Inbound Hotline incident utility handling, assets/teams, maps, missions, responder acknowledgement |
| PBB Account | PBB Account | Wizaya Server Suite Core Services, Identity / SSO | Local node identity service | Citizens/users, Account admins, trusted PBB apps | Canonical account credentials, SSO authorization code flow, trusted clients, app-admin provisioning bridge, session identity |
| PBB Salus | Salus | Citizen Apps, Operator Apps, Barangay Node Services, Evacuation / Relief Operations | Local barangay node / LAN-only app | Admins, operators, workers, command users, citizens | Evacuation center activation, registration, movement/occupancy, relief-batch receipt tracking, citizen QR status, Hotline aggregate summaries |
| PBB Library | Libria | Citizen Facing Apps, Core Infrastructure Services, Education / Reference Services | Library Cloud plus local Library Node | Citizens, students, teachers, responders, health workers, Learning, content admins | Governed reference catalog, signed content releases, offline node search/resource/content serving, SDK/embed, Library-to-Learning reference provider |
| PBB Learning | Lumaria | Citizen Apps, Education / Learning Services, Operator/Admin Apps | Local node learning gateway and optional dedicated learning server | Learners, teachers/facilitators, barangay staff, responders, admins, institutional partners | LMS-agnostic provider/catalog/instance gateway, Account SSO, emergency-safe learning surface, diagnostics/settings, Library reference integration |

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

PBB Natalium
  -> PBB Account SSO authorize/callback/logout
  -> Account-admin provisioning/sync APIs
  -> local `pbb_natalium` database
  -> patient registry, program enrollment, care workflow, maternal/child health, birth-defect, referral, prescription, citizen/staff, reporting/export, and operations APIs
  -> local document and backup paths
  -> Relay hub JSON URL for node context

PBB Utility / Vena
  -> Relay delivers `hotline.incident.upserted` to `/api/relay/incidents`
  -> Vena stores raw inbound records and normalized utility incidents
  -> operator dashboard and mission creation
  -> responder mission list/detail/acceptance
  -> MapLibre config from `/vena-map.json` using local MapServer URL env

PBB Salus
  -> implemented local `pbb_salus` Laravel domain/migration model
  -> multiple evacuation centers per barangay operation
  -> express/regular registration, validation, tags/QRs, optional individual movement or aggregate gate counts
  -> relief distribution batches and receipt/correction workflow, not full V1 inventory
  -> PBB Account SSO/app-admin and Salus-local roles/capabilities
  -> PBB Realtime change notifications followed by authorized REST refetch
  -> Hotline/Vox pulls privacy-safe aggregate summary through bearer-protected internal API
  -> Landing local launcher registration; public gateway disabled

PBB Library
  -> Library Cloud Laravel app ingests/normalizes governed reference sources
  -> Library Cloud builds signed manifests/releases and exposes authenticated node APIs
  -> Library Node Laravel app verifies, stages, activates, rolls back, indexes, and serves offline content
  -> local public catalog/search/resource URLs such as `https://library.pbb.ph`
  -> SDK/embed routes for approved origins
  -> Account SSO/app-admin hooks for administration
  -> Learning uses Library Node as a reference provider API
  -> routine large Library content transfer uses Library release/object download flows, not PBB Relay messages

PBB Learning / Lumaria
  -> Laravel 12 LMS-agnostic local learning gateway/control plane
  -> local `pbb_learning` database for providers, instances, catalog, jobs, health checks, settings, and audit
  -> local public/admin surface such as `https://learning.pbb.ph`
  -> PBB Account SSO/app-admin identity hooks
  -> provider/catalog/instance APIs, admin diagnostics/settings, emergency-state APIs
  -> PBB Library is a reference provider, not an LMS provider; full Learning-side adapter was not confirmed in the focused scan

PBB Account
  -> central canonical `pbb_user_id`
  -> local `pbb_accounts` database
  -> `/oauth/authorize`, `/oauth/token`, `/oauth/logout`
  -> trusted clients seeded for Chat, Hotline, Landing
  -> app-local sessions and app-local users linked by returned `pbb_user_id`
  -> session identity APIs for allowed `.pbb.ph` origins
  -> optional Realtime admission and Account event publishing

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
| PBB Natalium | PBB Account | Browser/server SSO | Authorize/callback/logout flow and Account claims | User login/logout | `natalium\routes\web.php`; `natalium\.env.example`; `natalium\tests\Feature\AccountSsoAndAdminSyncTest.php` |
| PBB Account admin client | PBB Natalium | HTTP bearer/client guarded API | User sync/provisioning payload | Account-admin provisioning/sync | `natalium\routes\api.php`; `natalium\.env.example`; `natalium\tests\Feature\AccountSsoAndAdminSyncTest.php` |
| PBB Natalium | Relay hub JSON | HTTP URL config | Node identity/context | Node context API | `natalium\.env.example`; `natalium\routes\api.php` |
| PBB Salus | PBB Account | Browser/server SSO | Authorize/callback/logout flow and Account claims; local `pbb_user_id` link | User login and express registration context | `salus\docs\integration-design.md`; `salus\docs\v1-scope.md` |
| PBB Account admin client | PBB Salus | HTTP bearer/client guarded API | App-local user provision, role/status update, access removal | Account-admin provisioning/sync | `salus\docs\integration-design.md`; `salus\docs\api-contract-design.md` |
| PBB Salus | PBB Realtime | Admission + backend event publish | Privacy-safe change notifications for center/public/operations/command rooms | Live UI update followed by REST refetch | `salus\docs\integration-design.md`; `salus\docs\resilience-design.md` |
| Hotline/Vox | PBB Salus | HTTP bearer-protected internal API | Aggregate evacuation/relief summary, freshness/completeness metadata | Hotline SITREP/support decision support | `salus\docs\integration-design.md`; `salus\docs\operational-summary-design.md`; `salus\docs\openapi\salus-v1.yaml` |
| Kit Setup | PBB Salus | App installer/Data Prep/health checks | Install config, Account/Realtime/Hotline summary settings, Landing metadata | Node installation/update/repair | `salus\docs\integration-design.md`; `salus\docs\pbb-app-standards-compliance.md` |
| Landing | PBB Salus | Local launcher registry | Local `https://salus.pbb.ph` launch and health metadata; public gateway disabled | User launcher visibility | `salus\docs\integration-design.md` |
| PBB Library Cloud | PBB Library Node | HTTP node APIs | Profile/offers/release manifests/object downloads/trust keys/withdrawals/rollback/deployment reports/inventory | Content release and node update | `library\cloud\routes\web.php`; `library\node\database\migrations`; Cloud/Node env examples |
| PBB Library Node | PBB Library Cloud | HTTP node reporting APIs | Inventory, deployment events/completion, rollback/reporting state | Release install/update reporting | `library\cloud\routes\web.php`; `library\node\database\migrations` |
| PBB Learning / Lumaria | PBB Library Node | Documented local reference-provider API; complete adapter not confirmed in focused Learning code | Search/resource/collections/citation/related calls | Learning contextual research and reference launch | `learning\docs\PBB_LEARNING_PBB_LIBRARY_INTEGRATION_ADDENDUM.md`; `library\node\routes\api.php` |
| Kit Setup | PBB Library Node | Installer/Data Prep/smoke checks not confirmed in focused scan | Library HDD/storage, trusted Cloud release settings, package staging, health/readiness | Node commissioning and content preload | `library\node\docs\PBB_LIBRARY_NODE_DEVELOPMENT_PROPOSAL.md`; current Kit package evidence not confirmed here |
| Kit Setup | PBB Learning / Lumaria | Installer/config wiring not confirmed in focused scan | LMS adapter settings, Library base URL/token, Landing registration | Node commissioning | `learning\docs\PBB_LEARNING_V1_IMPLEMENTATION_STATUS.md`; Kit evidence not confirmed here |
| Landing | PBB Library Node | Local launcher/public metadata expected; focused scan did not confirm current registry package | Local launch URL and release gateway metadata | App launch and Library release operations | `library\node\routes\web.php`; Landing package evidence not confirmed here |
| Landing | PBB Learning / Lumaria | Local launcher registration expected; focused scan did not confirm current registry package | Local `https://learning.pbb.ph` launch and health metadata | User launcher visibility | `learning\routes\web.php`; Landing package evidence not confirmed here |
| PBB Account | PBB Learning / Lumaria | Browser SSO and app-admin API | `pbb_user_id` link, learner/admin identity, role/status sync | Login and admin operation | `learning\routes\web.php`; `learning\routes\api.php`; `learning\.env.example` |
| PBB Account | PBB Library Cloud / Node | Browser SSO and app-admin/admin identity hooks | Account-linked Library admins; public reading/search does not require login | Library administration | `library\cloud\routes\web.php`; `library\node\routes\web.php`; `library\node\routes\api.php`; `.env.example` files |
| PBB Chat / Hotline / Landing trusted clients | PBB Account | Browser redirect + server-side token exchange | SSO authorization code and identity payload | App login | `account\routes\web.php`; `account\app\Http\Controllers\OAuthController.php`; `account\database\seeders\DatabaseSeeder.php`; `account\tests\Feature\OAuthClientUsageTest.php` |
| Hotline / Relay / Chat / Support / Realtime / Maestro | PBB Account | Browser redirect + callback + token exchange | Account SSO login and app-local user/session linked by `pbb_user_id` | App login | app `routes\web.php`; app `AccountSsoController`; app Account SSO tests |
| PBB Account | Hotline / Relay / Chat / Support / Realtime / Maestro app-admin APIs | HTTP bearer token + `X-PBB-Account-Client` | App-local user provision, lookup, role/status update, access removal | app `AccountAdminController`; app `VerifyAccountAdminService`; app Account admin tests |
| Trusted PBB app backends | PBB Account | HTTP headers `X-PBB-Account-Client-Id` / `X-PBB-Account-Client-Secret` | Resolve/update Account-owned identity fields | App profile sync | `account\app\Http\Controllers\Api\AccountIdentityController.php`; `account\tests\Feature\AccountIdentityApiTest.php` |
| PBB Account | App admin endpoints | HTTP bearer token configured per trusted client | App metadata, user provisioning, role/status update | Account admin operation | `account\app\Http\Controllers\Api\AdminController.php`; `account\app\Services\AppAdminApiClient.php`; `account\tests\Feature\AdminSurfaceApiTest.php` |
| PBB Account | PBB Realtime | Realtime backend SDK / admission token | Account login/logout/profile events and session/browser admission | Optional realtime account session updates | `account\app\Http\Controllers\Api\RealtimeAdmissionController.php`; `account\app\Services\AccountRealtimeEventPublisher.php`; `account\config\account.php` |
| Relay | PBB Utility / Vena | HTTP `POST /api/relay/incidents` | `hotline.incident.upserted` incident snapshot targeted to `utility.vena` | Relay handler delivery | `utility\routes\api.php`; `utility\app\Http\Controllers\Api\RelayIncidentHandlerController.php`; `utility\config\vena.php` |
| PBB Hotline | PBB Utility / Vena through Relay | Relay envelope | Hotline incident current-state snapshot with source identity, details, resources, media refs metadata | Validated utility handoff | Chatviewer Utility/Hotline contract messages; `utility\tests\Feature\VenaRelayIncidentIntakeTest.php` |
| PBB Utility / Vena | PBB MapServer | Browser HTTP tile/style URL config | MapLibre vector/terrain/glyph/POI URLs | Utility map display | `utility\routes\web.php`; `utility\.env.example` |
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
| `pbb_natalium` MySQL database | PBB Natalium | Users/sessions, patient registry, program enrollments, care workflows, maternal/child health, birth defects, referrals, prescriptions, reports, audit/settings | `natalium\.env.example`; `natalium\database\migrations`; `natalium\routes\api.php` |
| Natalium document and backup paths | PBB Natalium | Local document uploads and operations backup/export storage | `natalium\.env.example`; `natalium\routes\api.php` |
| Relay hub JSON URL | PBB Natalium | Local node context source | `natalium\.env.example`; `natalium\routes\api.php` |
| `pbb_utility` MySQL database | PBB Utility / Vena | Users, settings, assets, teams, Relay inbound incidents, normalized incidents, missions | `utility\.env.example`; `utility\database\migrations` |
| `vena_relay_inbound_incidents` raw payload table | PBB Utility / Vena | Raw Relay envelope/payload retention and quarantine/stale tracking | `utility\database\migrations`; `utility\app\Http\Controllers\Api\RelayIncidentHandlerController.php` |
| `pbb_accounts` MySQL database | PBB Account | Accounts, trusted clients, SSO codes, audit events, settings, sessions/cache/queue | `account\.env.example`; `account\database\migrations` |
| `account\sdk\php` | Consuming PBB apps | PHP SDK for SSO/account client integration | `account\sdk\php\README.md`; `account\sdk\php\src` |
| `PBB_ACCOUNT_SERVICE_PROPOSAL.md` | PBB Account, Chat, Hotline, future apps | Historical proposal and migration rationale; implementation now exists in `account` | `documentations\PBB_ACCOUNT_SERVICE_PROPOSAL.md`; `account\routes` |
| `pbb_library_cloud` MySQL database | PBB Library Cloud | Source systems, import jobs, canonical content, governance, signing/releases, nodes, fleet reports, runtime settings | `library\cloud\.env.example`; `library\cloud\database\migrations`; `library\cloud\routes\web.php` |
| Library Cloud object/quarantine/build roots | PBB Library Cloud | Content objects, quarantined inputs, release build artifacts | `library\cloud\.env.example` |
| `pbb_library` MySQL database | PBB Library Node | Catalog/resources/files/collections/search history, storage inventory, trust/release/deployment state, Account/local users, SDK origins | `library\node\.env.example`; `library\node\database\migrations`; `library\node\routes\api.php` |
| Library Node content/storage/object roots | PBB Library Node | Offline reference content packages, object storage, release staging/activation/rollback state | `library\node\.env.example`; `library\node\database\migrations` |
| Library shared packages | PBB Library Cloud, PBB Library Node | Shared PHP domain logic and JS UI package | `library\shared\php\library-core\composer.json`; `library\shared\js\library-ui\package.json` |
| `pbb_learning` MySQL database | PBB Learning / Lumaria | Providers, platform types, servers, instances, catalog items, packages, jobs, health checks, settings, audit, users | `learning\.env.example`; `learning\database\migrations`; `learning\routes\web.php` |
| Learning proposal/status docs | PBB Learning / Lumaria, PBB Library, PBB Account, Landing, Kit Setup | V1 scope/status, Library integration direction, remaining installer/adapter/admin work | `learning\docs\PBB_LEARNING_V1_IMPLEMENTATION_STATUS.md`; `learning\docs\PBB_LEARNING_PBB_LIBRARY_INTEGRATION_ADDENDUM.md` |

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
- PBB Natalium can run local database/document/backup-backed health workflows on the LAN after installation; new SSO login depends on the configured PBB Account service.
- PBB Utility / Vena can run local operator/responder workflows on the LAN after Relay has delivered incidents; it has no confirmed outbound sync dependency.
- PBB Account runs locally with MySQL and DB sessions. New SSO login for consuming apps requires Account availability; no durable offline identity cache in consuming apps was confirmed from Account code.
- PBB Library Node is implemented as a local/offline Library app intended to serve catalog/search/resource content from node storage after content has been installed. New releases/offers require Library Cloud reachability.
- PBB Learning / Lumaria is implemented as a local learning gateway that can continue local registry/catalog/admin flows where local services exist. External LMS links, cloud LMSs, or remote institutional systems require their own connectivity. Complete Learning-to-Library adapter behavior was not confirmed in the focused scan.
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
- PBB Natalium SSO and Account-admin sync require the configured PBB Account endpoint. No cloud dependency was confirmed beyond that configured endpoint.
- PBB Utility / Vena depends on Relay reachability for inbound cross-node/cloud incident delivery and MapServer/tile URL reachability for map display.
- PBB Account external dependency is not required for local login/SSO when deployed locally; optional Realtime admission/events require PBB Realtime and configured signing secret.
- PBB Library Cloud is required for new governed source ingestion, normalization, release construction, node assignment, release offers, and fleet/deployment reporting. Library Node routine reading/searching should not require Cloud after content is installed.
- PBB Learning external provider launches depend on the selected LMS/provider. Moodle/provider adapter depth and full external sync behavior remain not confirmed.
- Kit Setup validates local WampServer and Technitium DNS prerequisites. DNS apply/verify can use Technitium locally; SSL/remote-check/provider-specific steps may still need network depending on configuration.

Confirmed retry/queue behavior:

- Relay delivery and local handler retry/backoff.
- Realtime backend event and media chunk pending/failed/published or forwarded state.
- Hotline has Laravel queue/scheduler, stale media finalization, confirmed SITREP Relay delivery/outbox support, and confirmed Support Request relay submission/lifecycle services. A durable browser-side citizen outbox was still not confirmed.
- Support System has database queue usage for SITREP relay delivery and support request lifecycle delivery jobs. Landing has no queue/retry layer; Relay handles store-and-forward behind it.
- Relay source-heartbeat operational webhooks are queued/tracked in `relay_webhook_deliveries`; Support accepts duplicate protection by `event_id`.
- PBB Chat has local DB persistence and Realtime publish skipping/logging when backend ingress is not configured; durable browser-side offline compose queues and Relay sync were not confirmed.
- PBB Games has no retry/queue layer because it does not manage operational sync data.
- PBB Natalium has no confirmed queue/retry layer or Relay outbox.
- PBB Utility / Vena has Laravel database queue tables/config, but its confirmed Relay incident intake is synchronous HTTP handler logic with quarantine/stale handling; app-specific queued jobs were not confirmed.
- PBB Account has Laravel database queue tables/config and Composer dev script queue listener, but app-specific queued jobs were not confirmed.
- PBB Library Node has migrations/routes for package verification, staging, activation, rollback, deployment state, cloud sync/download/outbox, and reporting. End-to-end operational retry/failure behavior was not fully traced in this focused scan.
- PBB Learning uses Laravel database queue configuration and job/health-check tables, but complete provider/LMS sync retry behavior is Unknown / Not confirmed from code.

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
- Utility / Vena receives `hotline.incident.upserted` snapshots from Relay targeted to `utility.vena`. Code confirms inbound-only behavior, raw inbound retention, normalized incident upsert, stale update rejection, bad message/target quarantine, and media URL/path quarantine.
- Relay emits `source.heartbeat.updated` operational webhooks outside the normal `hub_relay_messages` app-to-app path.
- Landing can expose a public `/relay/api/v1/*` gateway to the registered local Relay target, but it does not implement the store-and-forward queue itself.
- PBB Library code/docs explicitly separate routine multi-terabyte Library release transfer from PBB Relay. Library Node should pull large content releases through Library-specific release/object download flows, not Relay messages.
- PBB Learning docs and the focused route scan do not put Learning in the Library content-update path. Learning consumes Library Node as a local reference provider after Library content is installed.

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
- PBB Natalium has no realtime functionality confirmed.
- PBB Utility / Vena has no realtime functionality confirmed.
- PBB Account has optional Realtime admission and event publishing for account browser/session login, logout, and profile update behavior; Realtime is disabled by default in `.env.example`.
- PBB Library has no mandatory Realtime dependency confirmed in the focused code/docs scan. Library Node should operate without Realtime after commissioning.
- PBB Learning config includes a Realtime admission URL, but no mandatory WebSocket event flow was confirmed in the focused scan.

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

PBB Utility / Vena exposes `GET /vena-map.json` with MapLibre configuration and env-driven MapServer/vector/terrain/glyph/POI tile URLs. Natalium stores patient addresses, but no MapServer integration, map UI, geocoding, or routing was confirmed from reviewed code.

PBB Library and PBB Learning do not define MapServer integration in the focused code/docs reviewed. Library has offline content packages, but those are reference-library packages, not map tile packages.

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

Proposal-only Library/Learning commissioning expectations:

```text
Kit Setup
-> configure Library Node storage/package trust/release source
-> preload or verify Library content packages
-> register Library and Learning with Landing
-> configure Learning provider/adapters
-> configure Learning-to-Library URL/token where needed
-> run smoke checks for local launch/search/resource access
```

No implemented Library/Learning installer code was found in the current local scan.

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
| Health registry privacy | Natalium stores patient, clinical workflow, referral, prescription, document, reporting, access, and audit records locally | PBB Natalium | High | `natalium\database\migrations`; `natalium\routes\api.php`; `natalium\.env.example` | Define retention, backup, encryption-at-rest, document access, and audit policy before live health deployment |
| Account-admin sync token | Account app-admin endpoints are controlled by per-app token/client settings and are disabled or app-configured depending on app | Hotline, Relay, Chat, Support, Realtime, Maestro, Natalium, PBB Account | High | app `VerifyAccountAdminService`; app settings/config/tests | Keep app-scoped tokens, never use one shared fallback token, rotate per app |
| Utility incident raw payload retention | Vena stores raw Relay inbound envelopes/payloads, including incident/location/report details | PBB Utility / Vena, Relay, Hotline | High | `utility\app\Http\Controllers\Api\RelayIncidentHandlerController.php`; `utility\database\migrations` | Define raw payload retention, audit access, purge policy, and local backup encryption |
| Utility media refs must stay metadata-only | Vena quarantines direct URL/path media refs | PBB Utility / Vena, Hotline, Relay | High | `utility\tests\Feature\VenaRelayIncidentIntakeTest.php`; `RelayIncidentHandlerController.php` | Keep media payload contract metadata-only and use backend media access contracts for retrieval |
| Utility Relay handler token controls incident ingestion | Vena accepts Relay incident intake only with configured bearer token | PBB Utility / Vena, Relay | High | `utility\.env.example`; `RelayIncidentHandlerController.php` | Generate per-node/per-source token and rotate on compromise |
| Salus evacuee records are sensitive local disaster-response data | Salus stores proposed person, photo, movement, registration, missing-person, and relief receipt records locally | PBB Salus, PBB Account, Hotline | High | `salus\docs\privacy-access-design.md`; `salus\docs\domain-database-design.md` | Keep person-level records local, store photos outside public web root, enforce role/capability checks, and test summary privacy exclusions |
| Salus citizen QR possession grants limited local access | Issued evacuee QR tokens expose limited applicable relief schedules, received marks, and announcements | PBB Salus | High | `salus\docs\tagging-design.md`; `salus\docs\privacy-access-design.md` | Use opaque high-entropy hashed tokens and avoid embedding personal data in QR payloads |
| Salus Hotline summary shared secret controls aggregate access | Hotline/Vox pulls Salus aggregate summaries with a static per-node shared bearer secret | PBB Salus, Hotline | Medium | `salus\docs\integration-design.md`; `salus\docs\operational-summary-design.md` | Generate per-node server-side secret through Kit/Data Prep and rotate on compromise |
| Account credential store is high-value target | PBB Account, Chat, Hotline, Landing, Natalium, future apps | High | `account\database\migrations`; `account\app\Http\Controllers\OAuthController.php`; `account\app\Models\TrustedClient.php` | Rotate seeded dev credentials, restrict admin access, keep hashed secrets/codes, and define backup/encryption/retention policy |
| Account seeded development credentials exist | PBB Account | High if reused outside dev | `account\README.md`; `account\database\seeders\DatabaseSeeder.php` | Rotate/remove seeded test account and `*-dev-secret` clients before shared deployment |
| Library package trust/release operations need hardening | PBB Library Cloud/Node | High | `library\cloud\routes\web.php`; `library\node\database\migrations`; Library release/trust migrations | Keep manifest/signature/rollback contracts explicit and add end-to-end release verification tests/runbooks |
| Library public APIs must not expose local paths or credentials | PBB Library Node, Learning | High | `library\node\routes\api.php`; `library\node\routes\web.php` | Keep public search/resource APIs path-opaque and server-side; restrict admin/package endpoints with machine credentials |
| Learning identity/provider boundaries are implemented but V1 remains incomplete | PBB Learning, PBB Account, LMS providers | High | `learning\routes\web.php`; `learning\routes\api.php`; `learning\docs\PBB_LEARNING_V1_IMPLEMENTATION_STATUS.md` | Finish admin workflows, provider/LMS adapter decisions, installer wiring, and privacy/security review before deployment |
| Library search privacy | PBB Library Cloud/Node | Medium | `library\node\database\migrations`; search history/saved search routes | Do not upload raw citizen search queries to Cloud in V1 unless a privacy policy and consent model exist |
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
| Natalium sync/offline identity rules are not fully specified | PBB Natalium, PBB Account | Health registry workflows are local, but new SSO login and any future health-data sync need explicit offline behavior | High |
| Utility outbound lifecycle contract is not defined | PBB Utility / Vena, Hotline, Relay, Support System | Vena consumes Hotline incidents but no outbound utility status/mission update sync was confirmed | Medium |
| Utility mobile/offline responder workflow is not implemented in code | PBB Utility / Vena, planned responder app, Relay, Realtime | Vena has web responder mission endpoints, but mobile-first offline helper workflow remains design/planning | High |
| Account consuming-app adoption is active but uneven | Account, Chat, Hotline, Landing, Natalium, Relay, Support, Realtime, Maestro | Several apps have Account SSO/app-admin code, but defaults, enablement, trusted-client registration, callback URLs, and app-admin tokens vary by app | High |
| Salus release/installation is not started | PBB Salus, Hotline, Account, Realtime, Landing, Kit Setup | Runtime Laravel implementation has advanced, but release metadata, installer, package, Data Prep, and Kit Setup integration remain open | High |
| Salus V1 relief operations are batch/receipt-only | PBB Salus, Hotline, Support/Imperium | Product interest includes relief receiving/inventory, but V1 explicitly defers inventory, storage, stock lots, transfers, spoilage, and loss | Medium |
| Hotline-side Salus summary behavior is not defined here | Hotline, PBB Salus | Salus exposes an aggregate summary API, but Hotline's display/storage/SITREP usage remains a Hotline-owned design item | High |
| Library release operations need deployment runbook | PBB Library Cloud, PBB Library Node, Kit Setup, Landing, Learning | Implementation exists, but field packaging, installer/Data Prep, release transfer, rollback, and failure handling need operator docs | High |
| Learning / Lumaria V1 remains incomplete | PBB Learning, PBB Account, PBB Library, Landing, Kit Setup | Implementation exists, but status docs list remaining admin workflows, adapter decisions, emergency transitions, Kit/Data Prep, and release/security review | High |
| Library/Learning integration contract needs verification | PBB Library, PBB Learning, Kit Setup, Landing, Account | Library Node APIs exist and Learning addendum exists, but full Learning-side adapter calls were not confirmed in the focused scan | High |
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
| 13 | Define Natalium live-health privacy, Account dependency, and local/offline operating rules | Natalium stores patient/document/access data and currently depends on Account for new SSO login | PBB Natalium, PBB Account |
| 14 | Decide Vena outbound lifecycle and responder mobile sync scope | Vena inbound Relay intake is implemented, but outbound utility statuses and mobile offline sync are not confirmed | PBB Utility / Vena, Hotline, Relay, Realtime |
| 15 | Complete and document Account consuming-app integration status | Account now exists and older apps are adopting it; the next risk is app-by-app enablement, callback behavior, seeded secret rotation, and app-admin token lifecycle | PBB Account, Chat, Hotline, Landing, Natalium, Relay, Support, Realtime, Maestro |
| 16 | Finish Salus release packaging and Kit Setup integration | Salus runtime implementation is in progress, but release/installation remains not started in the checklist | PBB Salus, Account, Realtime, Hotline, Landing, Kit Setup |
| 17 | Define Hotline's Salus aggregate-summary ingestion and SITREP behavior | Salus V1 intentionally makes Hotline the active caller and downstream owner of usage | Hotline, PBB Salus |
| 18 | Decide whether full relief inventory should remain deferred or become a phased Salus slice | V1 relief is scheduled batches and receipts only, while operational planning may require receiving/storage/lots later | PBB Salus |
| 19 | Publish Library Cloud/Node release, trust, storage, and installer contracts | Library is now implemented and introduces high-impact signed releases, large local storage, public search, and Learning dependency | PBB Library, Kit Setup, Landing, Learning |
| 20 | Finish Learning V1 adapter/admin/installer hardening | Learning now has a Laravel implementation, but V1 status docs still leave provider/LMS adapter depth, admin workflows, emergency transitions, Kit/Data Prep, and security review open | PBB Learning, PBB Account, PBB Library, Landing |

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
| Which apps are required to integrate with Account in the next release, and which remain app-local for now? | Account implementation exists, but app-by-app adoption status determines deployment risk. |
| What local/offline behavior should consuming app login have when Account Service is unavailable? | Account runs locally, but durable fallback behavior belongs to consuming apps. |
| Should Account Service remain PBB-internal OAuth-like SSO for V1 or evolve toward full OIDC later? | Current code implements an OAuth-style authorization-code exchange, not a full OIDC provider. |
| Should Natalium health data ever sync upstream, or remain strictly local to a health/barangay node? | Code confirms local registry/documents but no Relay health-data sync. |
| Should Vena emit utility mission/status updates back to Hotline/Relay/Support, or remain inbound-only in V1? | Code confirms inbound-only incident intake and local mission handling. |
| Should Vena web responder endpoints become the basis of the planned mobile helper workflow, or stay as a separate utility-operator surface? | Code has responder mission endpoints, while owner-described mobile workflow is broader and offline-capable. |
| Should Salus V1 remain relief-batch/receipt-only, or should receiving/inventory be pulled into the first implemented release? | Current Salus docs explicitly defer full inventory, but the product need may require revisiting scope before implementation. |
| How should Hotline store, refresh, display, revise, and submit Salus aggregate summaries in its SITREP flow? | Salus owns the summary API; Hotline owns downstream operational use. |
| What pilot privacy/retention policy should govern Salus evacuee photos, movement records, QR access, backups, and purge approvals? | Salus stores sensitive local disaster-response identity data. |
| Should Library Cloud and Library Node remain subfolders under `library` or become separately released deployables? | Current local implementation is split into `library\cloud` and `library\node`; packaging affects Kit Setup and Landing registration. |
| What exact signed Library package manifest fields, hash/signature algorithms, and rollback semantics should V1 use? | Library depends on package trust and large offline content updates. |
| Which Library content sources, licenses, and provenance fields are mandatory for V1? | Library Cloud is proposed to govern ingestion, licensing, provenance, and release construction. |
| Should Learning V1 launch only local Moodle first, or include remote/external LMS provider links in the first release? | Proposal docs are LMS-agnostic but Moodle-first; deployment and offline behavior differ by provider. |
| Which Learning records are owned locally versus by external LMS providers? | Avoiding merged institutional LMS databases is a stated proposal boundary. |
| What minimum Learning-to-Library API should be implemented first: search only, search/resource, or search/resource/citation/related? | The addendum defines a broader adapter shape, but the first build can be smaller if documented. |

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
- `C:\wamp64\www\pbb\documentations\PBB_ACCOUNT_SERVICE_PROPOSAL.md`
- `C:\wamp64\www\pbb\account\README.md`
- `C:\wamp64\www\pbb\account\composer.json`
- `C:\wamp64\www\pbb\account\.env.example`
- `C:\wamp64\www\pbb\account\config\account.php`
- `C:\wamp64\www\pbb\account\routes\web.php`
- `C:\wamp64\www\pbb\account\routes\api.php`
- `C:\wamp64\www\pbb\account\app\Http\Controllers\OAuthController.php`
- `C:\wamp64\www\pbb\account\app\Http\Controllers\Api\AdminController.php`
- `C:\wamp64\www\pbb\account\app\Http\Controllers\Api\AccountIdentityController.php`
- `C:\wamp64\www\pbb\account\database\migrations`
- `C:\wamp64\www\pbb\account\database\seeders\DatabaseSeeder.php`
- `C:\wamp64\www\pbb\hotline\routes\web.php`
- `C:\wamp64\www\pbb\hotline\routes\api\account_admin.php`
- `C:\wamp64\www\pbb\relay\routes\web.php`
- `C:\wamp64\www\pbb\relay\routes\api.php`
- `C:\wamp64\www\pbb\chat\routes\web.php`
- `C:\wamp64\www\pbb\support\routes\web.php`
- `C:\wamp64\www\pbb\realtime\docs\pbb-realtime-account-integration.md`
- `C:\wamp64\www\pbb\maestro\routes\web.php`
- `C:\wamp64\www\pbb\maestro\routes\api.php`
- `C:\wamp64\www\pbb\kit-setup\docs\app-installer-template.md`
- `C:\wamp64\www\pbb\natalium\composer.json`
- `C:\wamp64\www\pbb\natalium\config\app.php`
- `C:\wamp64\www\pbb\natalium\routes\api.php`
- `C:\wamp64\www\pbb\natalium\routes\web.php`
- `C:\wamp64\www\pbb\natalium\routes\api.php`
- `C:\wamp64\www\pbb\natalium\database\migrations`
- `C:\wamp64\www\pbb\natalium\docs\PBB_NATALIUM_IMPLEMENTATION_CHECKLIST.md`
- `C:\wamp64\www\pbb\utility\README.md`
- `C:\wamp64\www\pbb\utility\.env.example`
- `C:\wamp64\www\pbb\utility\config\vena.php`
- `C:\wamp64\www\pbb\utility\routes\web.php`
- `C:\wamp64\www\pbb\utility\routes\api.php`
- `C:\wamp64\www\pbb\utility\app\Http\Controllers\Api\RelayIncidentHandlerController.php`
- `C:\wamp64\www\pbb\utility\database\migrations`
- `C:\wamp64\www\pbb\utility\tests\Feature\VenaRelayIncidentIntakeTest.php`
- `C:\wamp64\www\pbb\salus\docs\development-proposal.md`
- `C:\wamp64\www\pbb\salus\docs\v1-scope.md`
- `C:\wamp64\www\pbb\salus\docs\domain-database-design.md`
- `C:\wamp64\www\pbb\salus\docs\api-contract-design.md`
- `C:\wamp64\www\pbb\salus\docs\integration-design.md`
- `C:\wamp64\www\pbb\salus\docs\operational-summary-design.md`
- `C:\wamp64\www\pbb\salus\docs\privacy-access-design.md`
- `C:\wamp64\www\pbb\salus\docs\resilience-design.md`
- `C:\wamp64\www\pbb\salus\docs\retention-backup-design.md`
- `C:\wamp64\www\pbb\salus\docs\implementation-checklist.md`
- `C:\wamp64\www\pbb\salus\docs\openapi\salus-v1.yaml`
