# PBB Selected Apps Technical Briefing

Scope: `C:\wamp64\www\pbb\hotline`, `C:\wamp64\www\hotline-helpers`, `C:\wamp64\www\pbb\relay`, `C:\wamp64\www\pbb\hub.ph`, `C:\wamp64\www\pbb\maestro`, `C:\wamp64\www\pbb\realtime`, `C:\wamp64\www\mapserver`, `C:\wamp64\www\pbb\chatviewer`, `C:\wamp64\www\pbb\support`, `C:\wamp64\www\pbb\landing`, `C:\wamp64\www\pbb\chat`, `C:\wamp64\www\pbb\games`, `C:\wamp64\www\pbb\library`, `C:\wamp64\www\pbb\learning`, `C:\wamp64\www\pbb\natalium`, `C:\wamp64\www\pbb\utility`, `C:\wamp64\www\pbb\salus`, `C:\wamp64\www\pbb\kit-setup`.

Important evidence rule: code and config were inspected locally. PBB Salus now has a Laravel 12 scaffold and implemented routes/controllers/models/migrations under `C:\wamp64\www\pbb\salus`, plus proposal/design/OpenAPI/checklist documents under `docs`. Salus claims below are limited to the inspected local files. Where a detail was not confirmed from code, it is marked `Unknown / Not confirmed from code`.

Owner clarification added after the local code review: Hub HQ provides node identity details, with `https://relay.pbb.ph/hub.json` identified by the project owner as a sample hub information endpoint; Kit Setup validates installation from an admin-provided hub ID and hub token; production topology is currently barangay -> city/municipality -> province -> cloud PBB HQ; incidents are not intended to sync as editable records across different nodes, and nodes instead send periodic consolidated SITREPs upstream; Realtime is expected to run as one shared gateway instance per node; Setup Data Prep populates MapServer tiles for the Hub HQ-defined boundary; Maestro is strictly observer-only while Windows services registered by Kit Setup own process lifecycle; Chatviewer is personal development coordination tooling; Kit Setup requires WampServer and Technitium DNS for node installation; PBB-managed FRP is the planned tunnel model, with Hub HQ as control plane, FRPS as tunnel server, and FRPC on each local node to expose each Relay node without static IPs, port forwarding, Cloudflare, or an external tunnel provider; the planned responder/helper mobile workflow is a field-ready, offline-capable companion app for responders/helpers, but implementation was not found in the reviewed repositories.

Fresh-scan update source: local code under `C:\wamp64\www\pbb`, plus earlier `C:\wamp64\www\pbb\chat_log.md` context where relevant. The main changes since the original briefing are: Hotline now has SITREP Relay outbox/delivery code, Support Request persistence and relay lifecycle services, and a backend-token-protected SITREP media manifest/download API plus SDK; Relay now has a backend-only relationship resolver at `POST /api/v1/relationships/resolve`; Kit Setup now documents/enforces additive app database migration policy; Helper UI bundle updates have continued through active loader cache revision `0.21.117`; PBB Support System at `C:\wamp64\www\pbb\support` is now reviewed as a Laravel SITREP/support operations app; and PBB Landing at `C:\wamp64\www\pbb\landing` is now reviewed as a lightweight PHP local launcher/public hub metadata/gateway surface.

Current-state alignment note, 2026-06-22: local code and DB-backed Chatviewer updates confirmed additional changes after the prior briefing. Kit Setup first had the bundled package manifest with `pbb-landing`, `pbb-mapserver`, `pbb-maestro`, `pbb-realtime`, `pbb-relay`, `pbb-hotline`, and `pbb-support`, plus the Cebu MapServer boundary pack; current 2026-06-29 local Kit version is `0.1.164`. The finalized Hotline/Relay/Support Data Prep model uses separate Support role identities: `sitrep.ingestor` for `sitrep.record` and `support.dispatch` for `support.request` / `support.request.cancelled`. Relay now implements operational `source.heartbeat.updated` webhooks through `relay_webhook_subscribers` and `relay_webhook_deliveries`; Kit seeds a Support Source Heartbeats subscriber, and Support receives it at `POST /api/relay/source-heartbeats`, validates a dedicated token, deduplicates by `event_id`, and publishes accepted snapshots to Realtime. Chatviewer now has a DB-backed agent chat API with token-authenticated posting/claiming and `GET /api/chat-entries.php` list queries defaulting newest-first while `order=asc` is available for chronological API reads.

Current-state alignment note, 2026-06-29 / corrected 2026-07-07: local code and recent DB-backed Chatviewer entries confirm PBB Chat, PBB Games, and PBB Account as local projects. PBB Chat at `C:\wamp64\www\pbb\chat` is a Laravel 12 local barangay chat app with rooms, direct messages, message requests, badges, reports/blocks/moderation, Realtime admission/publishing, and a Hotline escalation handoff stub. PBB Games at `C:\wamp64\www\pbb\games` is a plain PHP optional local engagement/learning app with no database and no operational API integration in version 1. PBB Account at `C:\wamp64\www\pbb\account` is now confirmed as a Laravel 12 local node identity and SSO service with canonical `accounts`, trusted OAuth-style clients, hashed one-time authorization codes, admin surfaces, app-admin provisioning hooks, session identity APIs, optional Realtime admission, and a PHP SDK. Kit Setup local `package.json` is now `0.1.164`; the bundled package manifest still lists Landing, MapServer, Maestro, Realtime, Relay, Hotline, Support, and the Cebu MapServer boundary pack. Helper `package.json` remains `0.21.83`, but active loader cache revisions in `js\ui\ui.loader.js` are `0.21.117` and recent Helper changes add Chat envelope/block/mute icons, sender presence indicators, `ui.file.input`, login-form media branding options, paste attachments in `ui.chat.composer`, and improved `ui.game.state.chrome.showMilestone(...)`.

Current-state alignment note, 2026-07-07: local code and DB-backed Chatviewer updates confirm two additional projects. PBB Natalium at `C:\wamp64\www\pbb\natalium` is a custom PHP/Composer local health-center app for patient registry, practitioner/capability management, PBB Account SSO, document intake/review, patient access grants, and audit events. PBB Utility at `C:\wamp64\www\pbb\utility` is the Laravel 12 Vena utility-operator app with local roles (`admin`, `operator`, `command`, `responder`), assets/teams/settings, MapLibre map config, inbound-only Relay incident intake for `hotline.incident.upserted` targeted to `utility.vena`, quarantine/stale handling, normalized Vena incidents, and operator/responder missions. Chatviewer context confirms Natalium's planning around barangay continuity-of-care health workflows and Utility/Vena's Hotline incident Relay alignment.

Current-state Account integration note, 2026-07-07: a targeted rescan of older apps confirms Account integration in more apps than the prior briefing stated. Hotline, Relay, Chat, Support, Realtime, and Maestro now have Account SSO redirect/callback/logout code and/or Account app-admin APIs using `users.pbb_user_id` links. Landing has Account launcher/session UI behavior and a registry entry for `pbb-account`. Kit Setup app-installer docs now define Account SSO/app-admin environment naming, `X-PBB-Account-Client: pbb-account`, app-admin token requirements, and `users.pbb_user_id` migration expectations. Hub/HQ and MapServer did not show PBB Account SSO/app-admin integration in the targeted scan beyond generic account UI/helper references.

Current-state Salus implementation note, 2026-07-12: PBB Salus at `C:\wamp64\www\pbb\salus` now has a Laravel 12 implementation foundation, not only design docs. Local files confirm `artisan`, `composer.json`, `package.json`, `app`, `routes`, `database`, `public`, `resources`, `tests`, and `vendor` are present. Routes/controllers/models/migrations cover Account SSO/app-admin, bootstrap/session, centers, operations, activations, registration, tags/printing, missing reports, zones, movement/occupancy, relief batches/receipts/corrections, citizen QR sessions, diagnostics, maintenance, Realtime admission/publish support, and Hotline/Vox aggregate summary access. `docs\implementation-checklist.md` reports implementation through milestone 19, 87 implemented API paths documented, Laravel foundation complete, operational modules implemented, Account/Realtime/Hotline contracts implemented, and release/installation not started. Salus V1 still explicitly defers full relief inventory/receiving/storage/lots, direct Relay/Support/MapServer/Maestro integration, native mobile apps, continuous geofencing, and cross-node private evacuee sync.

---

Current-state Library/Learning note, 2026-07-17: `C:\wamp64\www\pbb\library` and `C:\wamp64\www\pbb\learning` are proposal/specification folders only in the local scan. No executable app code, package files, routes, migrations, or runtime config were found. Library docs define PBB Library Cloud and PBB Library Node as separate proposed deployables for centrally governed general-reference content, signed releases, offline node search, and Learning integration. Learning docs define PBB Learning/Lumaria as an LMS-agnostic local education gateway and control plane with Moodle as the first adapter, PBB Account as preferred identity, PBB Landing registration, emergency-safe behavior, and a dedicated reference-provider adapter to PBB Library.

## App: PBB Hotline

### 1. Executive Technical Summary

PBB Hotline is a Laravel 12 emergency-call, incident intake, operator dispatch, command, admin, messaging, media, mapping, SITREP, and support-request application. It has citizen, operator, command, and admin surfaces. It runs as a PHP/Laravel web app under WAMP/Apache or another PHP web server pointing at `public/`, with MySQL for production and Vite-built frontend assets. It fits the PBB ecosystem as the local emergency reporting and handling app that uses PBB Realtime for call/presence/media events, PBB MapServer for map tiles/boundaries, Relay-facing services for SITREP/support-request handoff, and a relay hub status JSON URL for hub heartbeat checks.

### 2. Repository Overview

| Area | Details |
|---|---|
| App Name | PBB Hotline |
| Local Path | `C:\wamp64\www\pbb\hotline` |
| Main Language | PHP, JavaScript |
| Main Framework | Laravel 12 |
| Frontend Framework | Vite, plain JavaScript modules, Helper UI library, MapLibre GL |
| Backend Framework | Laravel |
| Database | MySQL in `.env.example`; SQLite in tests |
| Realtime Technology | PBB Realtime backend SDK plus browser WebSocket client flow |
| Queue / Worker System | Laravel queue; installer creates queue worker and scheduler service artifacts |
| Package Manager | Composer, npm |
| Runtime Requirements | PHP `^8.2`, Composer, Node/npm for builds, MySQL, FFmpeg for media finalization |
| Main Entry Points | `public/index.php`, `artisan`, `routes/web.php`, `routes/api.php`, `resources/js/*` |
| Important Config Files | `.env.example`, `config/services.php`, `routes/*.php`, `vite.config.js`, `release.json`, `installer/schema/install.schema.json` |
| Important Environment Variables | `DB_*`, `SESSION_LIFETIME`, `HOTLINE_CITIZEN_SESSION_LIFETIME`, `MEDIA_ASSEMBLY_TOKEN`, `HOTLINE_FFMPEG_BINARY`, `HOTLINE_FFPROBE_BINARY`, `HOTLINE_REALTIME_CA_BUNDLE`, `RELAY_HUB_JSON_URL`, `RELAY_HUB_JSON_TIMEOUT`, SITREP/support-request relay settings, `sitrep_media_access_token` setting |
| Deployment Target | WAMP/Apache or PHP web server; installer also emits Windows Scheduled Task and Linux systemd artifacts |

### 3. App Purpose and PBB Role

Hotline solves citizen emergency reporting, live call coordination, incident workbench, team assignment, command alerts/broadcasts, SITREP creation, SITREP Relay handoff, and Support Request creation/lifecycle ingress. It is operator-facing, citizen-facing, command-facing, and admin-facing. It depends on its local database, PBB Realtime for socket admission/events, vendored Helper UI components, FFmpeg for media assembly, MapServer tile endpoints configured in map style assets, and Relay-facing client settings/services for upstream SITREP/support flows. The reviewed PBB Support System depends on Hotline support-request and SITREP media contracts. If Hotline is unavailable, citizen call reporting, operator workbench, command SITREP, support request creation/updates, and admin reference-data management are unavailable.

### 4. User Roles and Permissions

| Role | Purpose | Capabilities | Code Evidence |
|---|---|---|---|
| citizen | Emergency user | Citizen home, call attempts, reconnects, current/history incident views, realtime admission | `app\Domain\Shared\Enums\UserRole.php`; `routes\web\citizen.php`; `routes\api\citizen.php`; `routes\api\realtime.php` |
| caller | Deprecated citizen alias | Accepted as citizen-compatible legacy role | `app\Domain\Shared\Enums\UserRole.php` |
| operator | Triage/dispatch user | Operator dashboard, incident workbench, status updates, call handling, transfers, team assignments, media | `routes\web\operator.php`; `routes\api\operator.php` |
| command | Command user | Alert level updates, broadcasts, incident list, SITREP CRUD, public/preview/download SITREPs | `routes\web\command.php`; `routes\api\command.php` |
| admin | System/reference-data manager | Users, settings, incident categories/types/fields, resources, teams, inventories | `routes\web\admin.php`; `routes\api\admin.php` |

### 5. Main Features and Modules

### Citizen Emergency Call

Purpose: Citizen starts call attempts, reconnects, views active/current incidents.
Main Code: `app\Http\Controllers\Api\Citizen\*`, `app\Support\Calls\CallRoutingService.php`, `resources/js` citizen files.
Database Tables: `incidents`, `call_attempts`, `call_attempt_operator_attempts`, `call_sessions`, `call_participants`, `incident_citizen_locations`.
APIs / Routes: `POST /api/citizen/call-attempts`, `POST /api/citizen/call-attempts/{attempt}/cancel`, `GET /api/citizen/incidents/current`, `POST /api/citizen/incidents/{incident}/reconnect`.
Offline Behavior: Citizen offline page exists and JS tests assert browser offline handling; no durable offline submission queue was confirmed.
Sync Behavior: Realtime admission and socket event flow. No browser-side durable citizen submission queue was confirmed; server-side SITREP/support Relay flows are documented separately below.
Related PBB Apps: PBB Realtime, PBB MapServer.
Evidence: `routes\api\citizen.php`; `routes\web\citizen.php`; `tests\js\citizenSurfaceContracts.test.mjs`.

### Operator Incident Workbench

Purpose: Operator triage, incident status, intake details, location, incident types, resources, transfers, and assignments.
Main Code: `app\Http\Controllers\Api\Operator\IncidentController.php`, `TeamAssignmentController.php`, `TransferController.php`; `app\Support\Incidents\*`; `app\Support\Teams\TeamAssignmentService.php`.
Database Tables: `incidents`, `incident_type_details`, `incident_resources_needed`, `incident_transfers`, `team_assignments`, `team_assignment_notes`.
APIs / Routes: `GET /api/operator/dashboard`, `GET /api/operator/incidents`, `POST /api/operator/incidents/{incident}/status`, `POST /api/operator/incidents/{incident}/team-assignments`, transfer endpoints.
Offline Behavior: LAN/local operation likely when server and database are local; no operator offline queue confirmed.
Sync Behavior: Local incident workbench changes are not confirmed as editable cross-node incident sync. Post-briefing updates confirm separate SITREP Relay and Support Request relay flows.
Related PBB Apps: PBB Realtime, PBB MapServer, Helper UI.
Evidence: `routes\api\operator.php`; `README.md`; migrations under `database\migrations`.

### Command / SITREP

Purpose: Command-level alert, broadcast, and SITREP generation/download.
Main Code: `app\Http\Controllers\Api\Command\*`, `app\Support\Sitreps\SitrepGenerationService.php`, `bin\render-sitrep-pdf.mjs`.
Database Tables: `sitrep_reports`, `command_broadcasts`, `incidents`.
APIs / Routes: `POST /api/command/alert-level`, `POST /api/command/broadcasts`, `GET/POST/PATCH /api/command/sitreps`, `GET /command/sitreps/{sitrep}/download/{format}`.
Offline Behavior: Local generation can work if PHP/Node and local DB are available; public upstream publishing is not confirmed.
Sync Behavior: Post-briefing updates confirm latest-SITREP Relay handoff through `SitrepRelayOutboxService`, `SubmitSitrepRelayDelivery`, and `SubmitLatestSitrepToRelay`.
Related PBB Apps: PBB Realtime for broadcast status metadata.
Evidence: `routes\api\command.php`; `routes\web\command.php`; `database\migrations\2026_04_29_000001_create_sitrep_reports_table.php`.

### SITREP Relay Outbox

Purpose: Persist and submit the latest Hotline SITREP upstream through Relay-facing delivery code.
Main Code: `app\Support\Sitreps\SitrepRelayOutboxService.php`, `app\Jobs\SubmitSitrepRelayDelivery.php`, `app\Console\Commands\SubmitLatestSitrepToRelay.php`.
Database Tables: `sitrep_relay_deliveries`, `sitrep_reports`.
APIs / Routes: CLI command `app:submit-latest-sitrep-to-relay`; queue job `SubmitSitrepRelayDelivery`.
Offline Behavior: Delivery records persist locally and can be retried by the existing Hotline queue/scheduler path.
Sync Behavior: Latest SITREP handoff is confirmed; full production SITREP envelope/consolidation policy still needs explicit documentation.
Related PBB Apps: PBB Relay, Hub/HQ, Kit Setup service registration.
Evidence: `app\Support\Sitreps\SitrepRelayOutboxService.php`; `app\Jobs\SubmitSitrepRelayDelivery.php`; `app\Console\Commands\SubmitLatestSitrepToRelay.php`; `database\migrations`; `release.json`.

### Support Requests

Purpose: Create support requests from Hotline/SITREP context, submit `support.request` envelopes, and receive lifecycle updates from downstream support operations.
Main Code: `app\Support\SupportRequests\SupportRequestCreationService.php`, `SupportRequestRelaySubmissionService.php`, `SupportRequestLifecycleUpdateService.php`.
Database Tables: `support_requests`, `support_request_histories`.
APIs / Routes: internal support-request update ingress; support-request relay contract docs.
Offline Behavior: Local support request/history persistence is confirmed; upstream relay depends on queue/connectivity.
Sync Behavior: Confirmed lifecycle events are `support.request`, `support.request.received`, `support.request.under_review`, `support.request.accepted`, `support.request.rejected`, `support.request.assigned`, `support.request.en_route`, `support.request.fulfilled`, and `support.request.closed`. `support.request.completed` should not be emitted.
Related PBB Apps: PBB Relay, PBB Support System.
Evidence: `docs\support-request-relay-contract-proposal.md`; `app\Support\SupportRequests`; `tests\Feature\Internal\SupportRequestUpdateIngressTest.php`.

### SITREP Media Access

Purpose: Let authorized backend consumers request SITREP media manifests and downloads without exposing filesystem paths or public `/storage` URLs.
Main Code: `docs\sitrep-media-access-contract.md`, `packages\pbb-hotline-media-sdk`, internal SITREP media controllers/tests.
Database Tables: `media`, `message_attachments`, incident/SITREP tables as context.
APIs / Routes: `POST /api/internal/sitrep/media/manifest`, `GET /api/internal/sitrep/media/{kind}/{id}`.
Offline Behavior: Works on LAN/local Hotline when media files exist locally and backend token is configured.
Sync Behavior: Not a sync mechanism; supports optional evidence drill-down for downstream apps.
Related PBB Apps: PBB Support System, PBB Relay relationship resolver for backend credential context.
Evidence: `docs\sitrep-media-access-contract.md`; `packages\pbb-hotline-media-sdk`; `tests\Feature\Internal\SitrepMediaAccessTest.php`; `tests\Unit\HotlineMediaSdkTest.php`.

### Realtime Admission and Media

Purpose: Issue Realtime admission payloads and accept internal media chunks/product-query callbacks.
Main Code: `app\Support\Realtime\*`, `app\Http\Controllers\Api\Realtime\AdmissionController.php`, `app\Http\Controllers\Api\Internal\MediaChunkIngressController.php`.
Database Tables: `media`, `incident_messages`, `message_attachments`, `call_sessions`.
APIs / Routes: `POST /api/realtime/admission/{citizen|operator|command}`, `POST /api/internal/media/chunks`, `POST /api/internal/realtime/product-query`.
Offline Behavior: Requires local Realtime service for live socket features.
Sync Behavior: Internal callbacks from Realtime; no conflict handling found.
Related PBB Apps: PBB Realtime.
Evidence: `routes\api\realtime.php`; `routes\api\internal.php`; `app\Support\Realtime\Sdk\src\RealtimeConfig.php`.

### Admin Reference Data

Purpose: Manage users, incident categories/types/fields, resource types, teams, team inventory, settings.
Main Code: `app\Http\Controllers\Api\Admin\*`, `tools\populate-initial-data.php`.
Database Tables: `users`, `settings`, `incident_categories`, `incident_types`, `incident_type_fields`, `resource_types`, `teams`.
APIs / Routes: `/api/admin/*` routes in `routes\api\admin.php`.
Offline Behavior: Local-only admin works on LAN if app and DB are running.
Sync Behavior: Unknown / Not confirmed from code.
Related PBB Apps: Helper UI.
Evidence: `routes\api\admin.php`; `tools\populate-initial-data.php`.

### 6. Database Schema Summary

| Table | Purpose | Important Columns | Relationships / Notes |
|---|---|---|---|
| users | App accounts | name, mobile, email, password, role, status, last_login_at | Role enum includes citizen/caller/operator/command/admin |
| settings | Runtime/reference settings | key, value JSON | Used by app settings services |
| incident_categories | Incident category lookup | name, description | Parent for incident types |
| incident_types | Incident type lookup | incident_category_id, name, description | Has fields and default resources |
| incident_type_fields | Dynamic incident fields | field_key, field_label, input_type, options_json, config_json | Copied to incident details |
| resource_type_categories / resource_types | Resource taxonomy | name, unit_label, category_id | Used in resource needs and inventories |
| team_categories / teams | Responder/team taxonomy | team_category_id, name, status | Used in assignments |
| team_resource_inventories | Team resources | team_id, resource_type_id | Inventory reference |
| incidents | Core incident record | caller_id, operator_id, status, alert_level, lat/lng, location, details, called/resolved timestamps | Central record |
| call_attempts / call_attempt_operator_attempts | Call routing attempts | caller/operator/status/outcome/timestamps | Used for operator discovery and answer flow |
| call_sessions / call_participants | Live call sessions | incident_id, caller_id, status, outcome, participant_role | Used with media and realtime |
| incident_messages / message_attachments | Incident chat/files | sender fields, body, stored_path, mime_type | Attachments are local files |
| media | Finalized call media | incident_id, call_session_id, type, path, metadata_json | FFmpeg/media assembly related |
| incident_type_details / incident_resources_needed | Incident-specific details | incident_id, incident_type_id, resource_type_id | Operator workbench |
| team_assignments / team_assignment_notes / allocated_resources | Team dispatch lifecycle | status, assigned/enroute/arrived/completed timestamps | Status enum in code |
| incident_transfers | Operator-to-operator transfer | from/to operator, reason, status timestamps | Transfer workflow |
| activity_logs | Audit/activity feed | actor_id, action_type, message | Local activity logging |
| incident_citizen_locations | Live location history | latitude, longitude, accuracy, heading, captured_at | Citizen location tracking |
| sitrep_reports | SITREP data | status, visibility, multiple JSON sections | Command reports |
| sitrep_relay_deliveries | SITREP Relay delivery tracking | sitrep/report reference, relay state, attempts/timestamps | Post-briefing upstream handoff table |
| support_requests | Support request records | incident/SITREP context, justification fields, status/lifecycle fields | Used for support.request relay flow |
| support_request_histories | Support request lifecycle history | support_request_id, event/status, notes, timestamps | Audit/history for downstream lifecycle updates |
| command_broadcasts | Command broadcast | title, message, tone, audience, realtime_status | Realtime status metadata |

Relationship map:

```text
users
  -> incidents
       -> call_attempts -> call_attempt_operator_attempts
       -> call_sessions -> call_participants
       -> incident_messages -> message_attachments
       -> media
       -> incident_type_details
       -> incident_resources_needed
       -> team_assignments -> team_assignment_notes
       -> incident_transfers
       -> incident_citizen_locations
       -> support_requests -> support_request_histories
sitrep_reports -> sitrep_relay_deliveries
incident_categories -> incident_types -> incident_type_fields
resource_type_categories -> resource_types
team_categories -> teams -> team_resource_inventories
```

### 7. API and Route Inventory

| Method | Path / Endpoint | Purpose | Auth | Handler / File | Notes |
|---|---|---|---|---|---|
| GET | `/` | Public home | none | `routes\web\public.php` | SurfaceController |
| GET | `/hotline.json` | Map config | none | `HotlineMapConfigController` | Used by frontend map |
| GET | `/citizen`, `/citizen/offline` | Citizen surface/offline page | role citizen for `/citizen` | `routes\web\citizen.php` | Offline page unauthenticated |
| GET | `/operator` | Operator surface | role operator | `routes\web\operator.php` | |
| GET | `/command` | Command surface | role command | `routes\web\command.php` | |
| GET | `/admin` | Admin surface | role admin | `routes\web\admin.php` | |
| GET/POST | `/api/csrf-token`, `/api/login`, `/api/logout`, `/api/user`, `/api/session/ping` | Session/account | web/auth depending route | `routes\api\session.php` | Login throttled |
| GET | `/api/bootstrap`, `/api/public/alert-level` | Public bootstrap/alert | none | `routes\api\public.php` | |
| POST/GET | `/api/citizen/*` | Citizen call and incident flow | auth + role citizen | `routes\api\citizen.php` | |
| GET/POST/DELETE | `/api/operator/*` | Operator dispatch and call flow | auth + role operator | `routes\api\operator.php` | |
| POST/GET/PATCH | `/api/command/*` | Alert, broadcasts, SITREPs | auth + role command | `routes\api\command.php` | |
| CRUD | `/api/admin/*` | Admin reference data | auth + role admin | `routes\api\admin.php` | |
| GET/POST | `/api/incidents/{incident}/messages`, `/media` | Shared incident chat/media | auth | `routes\api\incidents.php` | |
| POST | `/api/media/assembly/complete` | Media assembly callback | token expected in service config | `routes\api\media.php` | |
| POST | `/api/realtime/admission/{role}` | Realtime admission | auth + role | `routes\api\realtime.php` | |
| POST | `/api/internal/media/chunks`, `/api/internal/realtime/product-query` | Realtime internal callbacks | CSRF disabled | `routes\api\internal.php` | Auth mechanism in controller, not route middleware |
| POST | `/api/internal/sitrep/media/manifest` | SITREP media manifest for backend consumers | token/header setting | SITREP media access controller | Returns metadata/download URLs only |
| GET | `/api/internal/sitrep/media/{kind}/{id}` | Authorized SITREP media download | token/header setting | SITREP media access controller | Kinds include `incident_media`, `message_attachment` |
| POST | internal support request update ingress | Support Request lifecycle updates | internal token/relay context | `SupportRequestLifecycleUpdateService`; tests | Handles downstream support lifecycle updates |
| Console | `app:submit-latest-sitrep-to-relay` | Submit latest SITREP to Relay | CLI | `app\Console\Commands\SubmitLatestSitrepToRelay.php` | Scheduler fallback / queue-supported handoff |
| Console | `app:prune-data-api-cache`, `app:check-hub-heartbeats`, `app:finalize-stale-call-media` | Scheduled maintenance | CLI | `routes\console.php` | Scheduled daily/minutely |

### 8. Data Flow and Operational Flow

```text
Citizen browser
-> Hotline `/api/citizen/call-attempts`
-> Hotline DB: incidents, call_attempts
-> Hotline `/api/realtime/admission/citizen`
-> PBB Realtime WebSocket rooms
-> Operator browser responds/answers
-> Hotline DB: call_sessions, participants, incident updates, media/messages
```

```text
Operator workbench
-> `/api/operator/incidents/{incident}/...`
-> Hotline DB incident/team/resource tables
-> optional Realtime event publish for live UI updates
```

```text
Command user
-> `/api/command/sitreps`
-> sitrep_reports JSON sections
-> `bin/render-sitrep-pdf.mjs` for PDF rendering
-> `/command/sitreps/{id}/download/{pdf|json|zip}`
```

### 9. Offline-First Behavior

Hotline is local/LAN capable when served by local WAMP and local MySQL. It has a citizen offline page and frontend offline detection. It uses local file storage for message attachments/media. Realtime live features require the Realtime server. Map display requires local/vendored map assets and MapServer endpoints for offline tile behavior. No durable browser-side outbox was confirmed. Post-briefing updates confirm server-side SITREP Relay delivery tracking and support-request relay/lifecycle flows. Owner clarification: incidents are not intended to sync as editable records across different nodes; the upstream flow is periodic consolidated SITREP reporting plus support-request workflows where applicable.

### 10. Integration with Other PBB Apps

| Integration | Direction | Protocol / Method | Purpose | Code Evidence |
|---|---|---|---|---|
| PBB Realtime | Hotline -> Realtime | HTTP admission + WebSocket | Presence, calls, chat, media events | `routes\api\realtime.php`; `app\Support\Realtime\Sdk`; `README.md` |
| PBB MapServer | Hotline -> MapServer | HTTP tile/style URLs | Operator map tiles, glyphs, terrain, POI | `README.md`; `public\hotline.json`; `public\maps\operator-vector-style.json` |
| Helper UI | Hotline embeds Helper | Vendored JS/CSS | UI components | `public\vendor\helpers.pbb.ph`; `README.md` |
| Relay hub JSON | Hotline -> Relay URL | HTTP JSON | Hub heartbeat/uplink checks | `config\services.php`; `app\Services\HubHeartbeatChecker.php`; `.env.example` |
| PBB Relay SITREP handoff | Hotline -> Relay | Queue job / HTTP relay client path | Latest SITREP upstream handoff | `app\Support\Sitreps\SitrepRelayOutboxService.php`; `app\Jobs\SubmitSitrepRelayDelivery.php` |
| PBB Relay Support Request flow | Hotline -> Relay -> Support System | Relay envelope lifecycle | `support.request` and support lifecycle updates | `app\Support\SupportRequests`; `docs\support-request-relay-contract-proposal.md` |
| Backend media consumers | Support System/backend -> Hotline | Internal HTTP API + SDK | SITREP media manifests/downloads | `docs\sitrep-media-access-contract.md`; `packages\pbb-hotline-media-sdk` |
| FFmpeg | Hotline -> binary | Local process | Media finalization | `README.md`; `app\Support\Media\MediaAssemblyService.php`; `installer\install-run.php` |

### 11. Deployment and Runtime Architecture

Expected OS includes Windows/WAMP and Linux-like PHP hosts. `release.json` says web server should point at `public/`. `.env.example` confirms MySQL config. Installer code emits Windows Scheduled Tasks for queue worker and scheduler and Linux systemd service/timer files. `composer.json` defines `composer setup`, `composer dev`, and `composer test`; `package.json` defines `npm run build` and `npm run dev`. FFmpeg is required and preferentially resolved from `bin/ffmpeg`.

### 12. Security and Privacy Notes

| Risk | Severity | Evidence | Suggested Fix |
|---|---|---|---|
| Internal routes bypass CSRF at route level | Medium | `routes\api\internal.php` removes `VerifyCsrfToken` for media chunks/product query | Ensure controllers enforce signed secret/HMAC and add tests for rejection paths |
| Citizen sessions intentionally long-lived | Medium | `README.md`; `.env.example` `HOTLINE_CITIZEN_SESSION_LIFETIME` | Keep separate from operator/admin session lifetime; document emergency-device logout policy |
| Sensitive incident, media, location data stored locally | High | migrations for `incidents`, `media`, `message_attachments`, `incident_citizen_locations` | Define retention, backup encryption, disk access controls |
| SITREP media token protects evidence downloads | High | `docs\sitrep-media-access-contract.md`; `sitrep_media_access_token` setting | Keep backend-only, rotate token, audit downloads, prevent browser exposure |
| Support Request lifecycle carries incident/support details | High | `support_requests`, `support_request_histories`; support request services | Define retention and authorization for support agencies and relay payloads |
| Uploaded file cleanup incomplete | Medium | `README.md` notes media/message files may remain when cleanup is unsupported | Implement file cleanup and audit delete behavior |

### 13. Realtime Communication

Realtime is central. Hotline uses rooms such as `presence.global.hotline` and `hotline.media.incident.{incidentId}` per README, and admission routes for citizen/operator/command. Events include citizen/operator discovery, call request, location updates, media processing/available, and presence state events. Authentication is through Hotline-authenticated admission routes and Realtime signed payloads. Fallback behavior is limited to frontend offline detection; no full fallback live-call transport was confirmed.

### 14. Mapping and Geolocation

Hotline uses MapLibre GL (`package.json` dependency and vendored public MapLibre files), `public/hotline.json`, and `public/maps/operator-vector-style.json`. Incident tables contain latitude/longitude and live citizen location history. The README identifies MapServer vector, glyph, terrain, and POI tile sources. Geolocation and heading are captured for active citizen calls. Offline maps depend on MapServer/cache availability.

### 15. Background Jobs, Schedulers, and Maintenance Tasks

| Task | Schedule / Trigger | Purpose | Code Evidence |
|---|---|---|---|
| `app:prune-data-api-cache --hours=168` | Daily 02:30 | Prune cached API data | `routes\console.php` |
| `app:check-hub-heartbeats` | Every minute | Check relay/hub heartbeat status | `routes\console.php`; `app\Services\HubHeartbeatChecker.php` |
| `app:finalize-stale-call-media --grace-seconds=30` | Every minute | Finalize stale media | `routes\console.php`; `app\Console\Commands\FinalizeStaleCallMedia.php` |
| Queue worker | Installer service artifact | Laravel queued work | `installer\install-run.php` |

### 16. Configuration and Environment Variables

| Variable / Config | Purpose | Required | Default / Example | Related Module |
|---|---|---|---|---|
| `DB_CONNECTION`, `DB_HOST`, `DB_DATABASE`, `DB_USERNAME`, `DB_PASSWORD` | Database | Yes | MySQL defaults in `.env.example` | Laravel DB |
| `SESSION_LIFETIME` | Normal session lifetime | Yes | masked | Auth |
| `HOTLINE_CITIZEN_SESSION_LIFETIME` | Citizen emergency session lifetime | Yes | 43200 documented | Citizen auth |
| `MEDIA_ASSEMBLY_TOKEN` | Media assembly auth token | Yes for media callbacks | masked | Media |
| `HOTLINE_FFMPEG_BINARY`, `HOTLINE_FFPROBE_BINARY` | Media binaries | FFmpeg required | `bin\ffmpeg\ffmpeg.exe` documented | Media |
| `HOTLINE_REALTIME_CA_BUNDLE` | TLS CA bundle for Realtime publishing | Optional | masked | Realtime |
| `RELAY_HUB_JSON_URL`, `RELAY_HUB_JSON_TIMEOUT` | Hub heartbeat JSON URL | Optional | `https://relay.pbb.ph/hub.json`, 5 | Hub heartbeat |

### 17. Known Technical Debt and Gaps

| Area | Issue | Evidence | Recommended Next Step |
|---|---|---|---|
| Offline sync | No confirmed durable browser-side incident outbox; server-side SITREP/support relay exists | Citizen offline page/tests plus post-briefing SITREP/support relay services | Document that editable incident sync is out of scope; define browser offline expectations separately |
| App endpoint metadata | Remote Hotline media base URL is not resolved by Relay relationship resolver | Relay resolver returns topology `domain`, not app URL | Define `source_hotline_url` or `apps.hotline.base_url` metadata before cross-node media consumers depend on it |
| File lifecycle | README notes cleanup may leave files | `README.md` cleanup note | Add storage cleanup commands/tests |
| Internal API auth | CSRF disabled; route middleware does not show auth | `routes\api\internal.php` | Confirm controller-level token validation and document it |
| Legacy role | `caller` retained as deprecated alias | `UserRole.php` | Complete migration to `citizen` |

### 18. Testing Status

PHPUnit is configured with Unit and Feature suites using in-memory SQLite and sync queues. Feature tests cover auth/session, citizen call/reconnect, admin CRUD, operator workbench/assignments/transfers, command alerts/SITREPs, realtime admission, internal media/product-query callbacks, and post-briefing support-request/SITREP media access contracts. JS contract tests cover citizen surface/offline/realtime expectations. Run with `composer test`; frontend contract tests appear as Node `.mjs` tests but no single npm script for all JS tests was confirmed.

### 19. Evidence Summary

Evidence:
- `C:\wamp64\www\pbb\hotline\README.md`
- `C:\wamp64\www\pbb\hotline\composer.json`
- `C:\wamp64\www\pbb\hotline\package.json`
- `C:\wamp64\www\pbb\hotline\routes\api.php`
- `C:\wamp64\www\pbb\hotline\routes\web.php`
- `C:\wamp64\www\pbb\hotline\routes\console.php`
- `C:\wamp64\www\pbb\hotline\database\migrations`
- `C:\wamp64\www\pbb\hotline\app\Domain\Shared\Enums\UserRole.php`
- `C:\wamp64\www\pbb\hotline\config\services.php`
- `C:\wamp64\www\pbb\hotline\installer\install-run.php`
- `C:\wamp64\www\pbb\hotline\app\Support\Sitreps\SitrepRelayOutboxService.php`
- `C:\wamp64\www\pbb\hotline\app\Support\SupportRequests`
- `C:\wamp64\www\pbb\hotline\docs\sitrep-media-access-contract.md`
- `C:\wamp64\www\pbb\hotline\packages\pbb-hotline-media-sdk`

---

## App: PBB Helper

### 1. Executive Technical Summary

PBB Helper at `C:\wamp64\www\hotline-helpers` is not a responder account app in the inspected code. It is a static JavaScript/CSS UI helper library and demo site used by Hotline and other PBB apps. It contains incident UI components, general UI controls, bundled/minified build output, boot JSON reference samples, demos, and documentation. It runs as static frontend assets and uses Node/esbuild only to build the UI bundle. Post-briefing updates confirm active loader cache revisions at `0.21.117`, including shared password fields, number-stepper form fields, row `className` alias support, `ui.navigation.stack`, Chat envelope/block/mute icons, sender presence indicators, `ui.file.input`, login-form media branding options, paste attachments in `ui.chat.composer`, and improved `ui.game.state.chrome.showMilestone(...)`. Owner clarification: a separate responder/helper mobile workflow is planned as a field-ready, offline-capable companion app for barangay responders, rescue teams, medical responders, and utility helpers.

### 2. Repository Overview

| Area | Details |
|---|---|
| App Name | PBB Helper / helpers.pbb.ph |
| Local Path | `C:\wamp64\www\hotline-helpers` |
| Main Language | JavaScript, CSS, HTML |
| Main Framework | Plain JS component library |
| Frontend Framework | None confirmed; custom helper components |
| Backend Framework | No backend found |
| Database | No database usage found / Not confirmed from code |
| Realtime Technology | UI components/spec docs mention realtime, but no server runtime found |
| Queue / Worker System | None found |
| Package Manager | npm |
| Runtime Requirements | Static web server for demos; Node/npm/esbuild for bundle generation |
| Main Entry Points | `index.html`, `demos/*.html`, `js/ui/*.js`, `js/incident/*.js`, `dist/helpers.ui.bundle.min.js` |
| Important Config Files | `package.json`, boot `*.json` files |
| Important Environment Variables | None found |
| Deployment Target | Static assets vendored into PBB apps |

### 3. App Purpose and PBB Role

Helper solves reusable UI rendering for incident forms, teams, incident types, chat, media, maps controls, forms, grids, timeline, navigation, and other PBB interfaces. Other PBB apps depend on it by vendoring `helpers.pbb.ph` assets. It does not itself support dispatch, sync, messaging persistence, roles, or database-backed responder workflows. Owner clarification: the planned responder/helper workflow should handle validated incident assignments, assignment acknowledgement, navigation, status stages, field notes/photos/outcome reports, local PBB network operation, and Relay-backed upstream sync after connectivity returns.

### 4. User Roles and Permissions

| Role | Purpose | Capabilities | Code Evidence |
|---|---|---|---|
| None formal | Static library/demo user | View demos and use frontend components | `README.md`; absence of backend/auth files |

### 5. Main Features and Modules

### Incident Components

Purpose: Render incident base, teams assignments, incident types, details editors/viewers.
Main Code: `js\incident\*.js`, `css\incident\*.css`.
Database Tables: No database usage found.
APIs / Routes: Static demos only.
Offline Behavior: Static assets can run locally if served or opened where browser module rules permit.
Sync Behavior: None.
Related PBB Apps: Hotline, Hub, MapServer, Chatviewer vendored copies.
Evidence: `README.md`; `js\incident`.

### General UI Components

Purpose: Shared UI widgets for modals, forms, chat, media, grids, maps controls, audio, navigation.
Main Code: `js\ui\*.js`, `css\ui\*.css`.
Database Tables: None.
APIs / Routes: None.
Offline Behavior: Static.
Sync Behavior: None.
Related PBB Apps: Multiple PBB apps via vendored assets.
Evidence: `README.md`; file inventory under `js\ui` and `css\ui`.

Post-briefing component updates:

- `ui.password` / `createPasswordField`
- hosted `number-stepper` / `number_stepper` fields in `createFormModal`
- row item `className` alias for `rowClassName`
- `ui.navigation.stack` / `createNavigationStack`, including `chrome:false`

Evidence: `js\ui\ui.loader.js`; `js\ui`; chat-log update entries for Helper bundle revisions.

### Bundle Builder

Purpose: Produce minified helper UI bundle.
Main Code: `scripts\build.ui.bundle.mjs`.
Database Tables: None.
APIs / Routes: None.
Offline Behavior: Local build only.
Sync Behavior: None.
Related PBB Apps: Apps consume `dist\helpers.ui.bundle.min.js/css`.
Evidence: `package.json`.

### 6. Database Schema Summary

No database usage found / Not confirmed from code

Relationship map:

```text
No database relationships found.
```

### 7. API and Route Inventory

| Method | Path / Endpoint | Purpose | Auth | Handler / File | Notes |
|---|---|---|---|---|---|
| Static | `index.html` | Demo/landing | none | `index.html` | Static |
| Static | `demos/*.html` | Component demos | none | `demos` | Static |
| Build | `npm run build:ui-bundle` | Build minified assets | local CLI | `scripts/build.ui.bundle.mjs` | Uses esbuild |

### 8. Data Flow and Operational Flow

```text
PBB app frontend
-> vendored Helper JS/CSS
-> Helper component renders UI from app-provided data
-> app backend remains owner of persistence and API calls
```

### 9. Offline-First Behavior

Helper is static and can be bundled for offline/LAN use. It has no confirmed service worker, local queue, conflict handling, or persistence.

### 10. Integration with Other PBB Apps

| Integration | Direction | Protocol / Method | Purpose | Code Evidence |
|---|---|---|---|---|
| PBB apps embed Helper | Helper -> apps as vendored assets | Static JS/CSS import | Shared UI | `hotline\public\vendor\helpers.pbb.ph`; `hub.ph\public\vendor\helpers.pbb.ph`; `mapserver\vendor\helpers.pbb.ph`; `chatviewer\vendor\pbb-helper` |

### 11. Deployment and Runtime Architecture

Static assets and demos can be served by Apache. `package.json` uses `esbuild` and exposes `npm run build:ui-bundle`. No server process or database runtime was found.

### 12. Security and Privacy Notes

| Risk | Severity | Evidence | Suggested Fix |
|---|---|---|---|
| Demo/sample data may resemble operational data | Low | boot JSON sample files and demos | Keep sample data clearly synthetic |
| No auth because static library | Low | no backend found | Ensure consuming apps enforce auth/authorization |

### 13. Realtime Communication

No realtime functionality found / Not confirmed from code. Documentation includes realtime proposals/specs, but this repo does not implement a Realtime server.

### 14. Mapping and Geolocation

Helper includes `ui.map.controls` and hierarchy map helpers; no tile service or geolocation persistence was found.

### 15. Background Jobs, Schedulers, and Maintenance Tasks

| Task | Schedule / Trigger | Purpose | Code Evidence |
|---|---|---|---|
| Bundle build | Manual `npm run build:ui-bundle` | Build dist assets | `package.json` |

### 16. Configuration and Environment Variables

| Variable / Config | Purpose | Required | Default / Example | Related Module |
|---|---|---|---|---|
| `package.json` version | Package metadata version | Yes | `0.21.83`; active loader cache revisions are `0.21.117` | Build/release |
| boot JSON files | Demo/reference bootstrap data | Optional | incident/status/team/resource JSON | Demos/components |

### 17. Known Technical Debt and Gaps

| Area | Issue | Evidence | Recommended Next Step |
|---|---|---|---|
| Role/responder app expectation | No responder account/backend functions found in this repo; owner clarified responder/helper mobile app is planned separately | Static-only files, no routes/db; owner clarification | Treat `hotline-helpers` as UI library and document the planned responder/helper app API separately |
| Runtime docs vs implementation | Many docs are proposals/checklists | `docs\*.md` | Separate implemented API from proposals |

### 18. Testing Status

No package test script was found. Demos and generated images exist. Build can be run with `npm run build:ui-bundle`.

### 19. Evidence Summary

Evidence:
- `C:\wamp64\www\hotline-helpers\README.md`
- `C:\wamp64\www\hotline-helpers\package.json`
- `C:\wamp64\www\hotline-helpers\js`
- `C:\wamp64\www\hotline-helpers\css`
- `C:\wamp64\www\hotline-helpers\dist`
- `C:\wamp64\www\hotline-helpers\demos`

---

## App: PBB Relay

### 1. Executive Technical Summary

PBB Relay is a Laravel shared relay service for store-and-forward hub-to-hub and local app-to-relay messaging. It accepts local client messages, queues deliveries, receives hub messages idempotently, manages delivery state, supports chunked uploads/attachments, local handler dispatch, HQ registry sync/heartbeat, and admin monitoring UI. It runs as a local infrastructure service with a Laravel queue worker.

Owner clarification: node identity details are provided by Hub HQ, and Kit Setup validates node installation from an admin-provided hub ID and hub token. A sample hub information endpoint was identified by the project owner at `https://relay.pbb.ph/hub.json`.

Owner clarification: Relay nodes are intended to become cloud-visible through PBB-managed FRP rather than an external tunnel provider. Hub HQ acts as the control plane, FRPS runs as the tunnel server, and each local node runs FRPC as the tunnel client.

### 2. Repository Overview

| Area | Details |
|---|---|
| App Name | PBB Relay |
| Local Path | `C:\wamp64\www\pbb\relay` |
| Main Language | PHP |
| Main Framework | Laravel |
| Frontend Framework | Blade/Helper UI assets; Vite present |
| Backend Framework | Laravel |
| Database | MySQL production; SQLite tests |
| Realtime Technology | None found; HTTP relay service |
| Queue / Worker System | Laravel queues; jobs `ProcessRelayDelivery`, `DispatchRelayToLocalHandler`, `RelayMaestroProbeJob` |
| Package Manager | Composer, npm |
| Runtime Requirements | PHP, Composer, MySQL, queue worker |
| Main Entry Points | `public/index.php`, `artisan`, `routes/api.php`, `routes/web.php` |
| Important Config Files | `.env.example`, `.env.relay_a`, `.env.relay_b`, `config/relay.php`, `release.json`, `installer/schema/install.schema.json` |
| Important Environment Variables | `RELAY_LOCAL_HUB_ID`, `RELAY_TARGETS`, `RELAY_HUBS`, `RELAY_HUB_CREDENTIALS`, `RELAY_HUB_AUTH_MODE`, `RELAY_DELIVERY_*`, `RELAY_HQ_*`, `RELAY_MAESTRO_*` |
| Deployment Target | Local shared service under WAMP/Apache or PHP host plus queue worker |

### 3. App Purpose and PBB Role

Relay solves store-and-forward synchronization between local applications and upstream/downstream hubs. It is infrastructure-level. Local apps submit envelopes through authenticated local client API; remote hubs send messages through hub-authenticated receive APIs. Current code also confirms a backend-only relationship resolver for hub credential lookup and operational `source.heartbeat.updated` webhooks for apps such as Support that need near-real-time source heartbeat updates without polling. It depends on MySQL, Laravel queue workers, optional HQ API, and optional Maestro telemetry. If Relay is unavailable, queued sync, upstream/downstream delivery, inbox, handler dispatches, relationship resolution, webhook dispatches, and hub registry/heartbeat updates stop.

### 4. User Roles and Permissions

| Role | Purpose | Capabilities | Code Evidence |
|---|---|---|---|
| relay admin | Administer Relay users/clients and UI operations | Admin UI, users, clients, reset/rotate/toggle operations | `app\Models\User.php`; `routes\web.php` |
| relay operator | Monitor and operate relay queues | Dashboard, retry/cancel deliveries, handler retries | `routes\web.php`; `app\Http\Middleware\EnsureRelayOperatorIsActive.php` |
| local relay client | Local app API identity | Submit messages, view inbox/deliveries, manage handlers/uploads | `routes\api.php`; `app\Http\Middleware\AuthenticateRelayClient.php` |
| hub peer | Hub-to-hub identity | Receive batches/uploads | `routes\api.php`; `app\Http\Middleware\AuthenticateRelayHub.php`; `config\relay.php` |
| backend relationship resolver client | Trusted backend app | Resolve source/target hub relationship credentials for a purpose | `routes\api.php`; `RelationshipController.php`; `RelayRelationshipResolver.php` |
| webhook subscriber | Trusted local operational receiver | Receive Relay-owned operational events such as `source.heartbeat.updated` | `relay_webhook_subscribers`; `RelayWebhookDispatcher`; `tools\data-prep\apply-settings.php` |

### 5. Main Features and Modules

### Local Message Submission

Purpose: Authenticated local apps submit messages for relay.
Main Code: `app\Http\Controllers\Api\Relay\Messages\MessageController.php`, `app\Relay\Outbound\RelaySubmissionService.php`.
Database Tables: `hub_relay_messages`, `hub_relay_deliveries`, `hub_relay_clients`.
APIs / Routes: `POST /api/v1/messages`, `GET /api/v1/messages`, `GET /api/v1/deliveries`.
Offline Behavior: Local submission stores messages in DB; upstream can be unavailable until worker retries.
Sync Behavior: Delivery rows per target/hop, retry policy.
Related PBB Apps: Candidate integration point for Hotline and other product apps.
Evidence: `routes\api.php`; `README.md`; migrations.

### Hub-to-Hub Receive

Purpose: Receive messages from peer hubs with idempotent handling.
Main Code: `ReceiveController`, `RelayReceiveService`, `RelayIdempotencyService`.
Database Tables: `hub_relay_messages`, `hub_relay_receipts`, `hub_relay_handler_dispatches`.
APIs / Routes: `POST /api/v1/receive`, `POST /api/v1/receive-batch`.
Offline Behavior: Requires HTTP reachability from peer; once received, local dispatch can occur later.

### Source Heartbeat Operational Webhooks

Purpose: Notify trusted local apps when Relay source heartbeat status changes, without routing heartbeat telemetry through normal app-to-app Relay messages.
Main Code: `RelaySourceHeartbeatRecorder`, `RelayWebhookDispatcher`, `DispatchRelayWebhook`, Relay admin webhook screens.
Database Tables: `relay_source_heartbeats`, `relay_source_heartbeat_rollups`, `relay_webhook_subscribers`, `relay_webhook_deliveries`.
APIs / Routes: Data Prep accepts `relay.data_prep.apply_settings.webhooks[]`; admin UI manages webhooks; jobs dispatch to subscriber endpoint URLs.
Offline Behavior: Heartbeat state and webhook deliveries are stored locally; delivery retries depend on queue worker and subscriber reachability.
Sync Behavior: Operational webhook event `source.heartbeat.updated` is queued/tracked separately from `hub_relay_messages`.
Related PBB Apps: Support System, Kit Setup.
Evidence: `C:\wamp64\www\pbb\relay\docs\relay-source-heartbeat-webhooks-implementation-checklist.md`; `C:\wamp64\www\pbb\relay\tools\data-prep\apply-settings.php`; `C:\wamp64\www\pbb\relay\tests\Feature\Relay\RelayHeartbeatTest.php`.
Sync Behavior: Idempotency and receipts confirmed.
Related PBB Apps: Upstream/downstream Relay nodes, local handlers.
Evidence: `routes\api.php`; `app\Relay\Inbound`.

### Attachments and Chunked Uploads

Purpose: Attach large files to relay messages with upload sessions/chunks.
Main Code: `UploadController`, `AttachmentController`, `RelayUploadService`.
Database Tables: `hub_relay_attachments`, `hub_relay_upload_sessions`.
APIs / Routes: `/api/v1/messages/{message}/attachments/init`, `/api/v1/uploads/{session}/chunk`, hub upload routes.
Offline Behavior: Stored locally once uploaded; transport to remote follows delivery worker.
Sync Behavior: Tracks transfer status/progress.
Related PBB Apps: Any app sending attachments.
Evidence: `routes\api.php`; migrations.

### Local Handlers

Purpose: Dispatch received messages to registered local webhooks/handlers.
Main Code: `HandlerController`, `HandlerDispatchController`, `LocalHandlerDispatchService`, `DispatchRelayToLocalHandler`.
Database Tables: `hub_relay_handlers`, `hub_relay_handler_dispatches`.
APIs / Routes: `/api/v1/handlers`, `/api/v1/handler-dispatches`, retry endpoint.
Offline Behavior: Failed handlers can retry by backoff.
Sync Behavior: Local delivery tracking and retry.
Related PBB Apps: Product apps with webhook endpoints.
Evidence: `routes\api.php`; `config\relay.php`.

### HQ Registry and Heartbeat

Purpose: Sync hub topology/identity and send Relay install heartbeat to Hub HQ.
Main Code: `RelayHqSyncCommand`, `RelayHqHeartbeatCommand`, `HqHubRegistrySyncService`, `RelayHqHeartbeatService`.
Database Tables: `hub_registry_hubs`, `hub_registry_links`, `relay_node_settings`.
APIs / Routes: console commands; config-driven HQ URLs.
Offline Behavior: Requires HQ connectivity; local relay can still run manually configured.
Sync Behavior: Registry sync and heartbeat status persisted.
Related PBB Apps: PBB Hub/HQ.
Evidence: `config\relay.php`; `app\Console\Commands`.

### Relationship Resolver

Purpose: Resolve backend-only relationship credentials between source and target hubs for a named purpose such as Hotline media access.
Main Code: `app\Http\Controllers\Api\Relay\Credentials\RelationshipController.php`, `app\Relay\Credentials\RelayRelationshipResolver.php`, `app\Relay\Credentials\RelayHubCredentialStore.php`.
Database Tables: Reads a Relay-private credential snapshot rather than a normal application table.
APIs / Routes: `POST /api/v1/relationships/resolve`.
Offline Behavior: Can resolve from local Relay-private `storage/app/relay/hub-credentials.json` and public topology snapshot if present.
Sync Behavior: Not a sync path; it supports backend-to-backend credential discovery.
Related PBB Apps: Hotline, Support System, Hub/HQ topology.
Evidence: `routes\api.php`; `app\Http\Controllers\Api\Relay\Credentials\RelationshipController.php`; `app\Relay\Credentials\RelayRelationshipResolver.php`; `docs\hub-relay-api-reference.md`; `tests\Feature\Relay\Api\RelationshipResolutionTest.php`.

### 6. Database Schema Summary

| Table | Purpose | Important Columns | Relationships / Notes |
|---|---|---|---|
| hub_relay_messages | Envelope store | source_hub_id, source_system, target_hub_ids, message_type, payload, content_hash | Core outbox/inbox message |
| hub_relay_deliveries | Per-target delivery state | target_hub_id, status, attempt_count, next_retry_at, last_error | Worker retry state |
| hub_relay_receipts | Inbound receipt/idempotency | source_hub_id, message_type, status, content_hash | Prevent duplicate handling |
| hub_relay_attachments | Attachment metadata | mime_type, size_bytes, storage_path, checksum | Linked to messages |
| hub_relay_clients | Local client credentials | system_code, api_key, is_active | Local app auth |
| hub_relay_upload_sessions | Chunked upload state | direction, source/target hub, chunks, progress, temp/assembled path | Attachment transfer |
| hub_relay_handlers | Local handler/webhook registry | endpoint_url, message_type_pattern, source filters, auth_token | Local dispatch |
| hub_relay_handler_dispatches | Handler dispatch state | status, attempt timestamps, retry, errors | Local handler retry |
| hub_registry_hubs | HQ registry snapshot | relay_hub_id, domain, deployment, PSGC codes, token status | Topology |
| hub_registry_links | Hub topology links | linked hub, relationship_type, uplink_type | Topology |
| relay_node_settings | Local relay settings | local_relay_hub_id, hq_sync_enabled, topology modes | Node identity/settings |
| `storage/app/relay/hub-credentials.json` | Relay-private credential snapshot | relationship credentials by source/target/purpose | Used by relationship resolver; not a database table |

Relationship map:

```text
hub_relay_clients
  -> hub_relay_messages
       -> hub_relay_deliveries
       -> hub_relay_attachments
       -> hub_relay_handler_dispatches
hub_registry_hubs
  -> hub_registry_links
relay_node_settings
```

### 7. API and Route Inventory

| Method | Path / Endpoint | Purpose | Auth | Handler / File | Notes |
|---|---|---|---|---|---|
| GET | `/api/health`, `/api/status` | Health/status | none | `routes\api.php` | Status controller for `/status` |
| GET | `/api/v1/diagnostics`, `/api/v1/compatibility` | Diagnostics/protocol | protocol middleware | `DiagnosticsController` | No local auth |
| POST/GET | `/api/v1/messages` | Submit/list messages | relay client | `MessageController` | Local app API |
| GET/POST | `/api/v1/deliveries/*` | Delivery status/retry/cancel | relay client | `DeliveryController` | |
| GET | `/api/v1/inbox/*` | Local inbox | relay client | `InboxController` | |
| POST | `/api/v1/messages/{message}/attachments/init` | Init attachment | relay client | `AttachmentController` | |
| POST/GET | `/api/v1/uploads/*` | Local upload chunks/status | relay client | `UploadController` | |
| CRUD | `/api/v1/handlers` | Register local handlers | relay client | `HandlerController` | |
| POST | `/api/v1/receive`, `/api/v1/receive-batch` | Hub inbound messages | relay hub | `ReceiveController` | |
| POST/GET | `/api/v1/upload/*` | Hub upload receive | relay hub | `UploadController` | |
| POST | `/api/v1/relationships/resolve` | Resolve backend-only relationship credentials | relay protocol + client middleware | `RelationshipController` | Request includes `source_hub_id`, `target_hub_id`, `purpose`; returned shared key must not reach browsers |
| GET/POST | `/install/*` | Browser installer | installer mode | `InstallerController` | |
| GET/POST | `/relay/*`, `/api/login`, `/api/user` | Admin UI/session/actions | auth + relay.operator | `routes\web.php` | |
| Console | `relay:hq-sync`, `relay:hq-heartbeat`, `relay:user:create`, `relay:installer:build`, `relay:maestro-probe` | CLI operations | local CLI | `app\Console\Commands` | |

### 8. Data Flow and Operational Flow

```text
Local app
-> Relay `/api/v1/messages` with client API key
-> hub_relay_messages + hub_relay_deliveries
-> Laravel queue `ProcessRelayDelivery`
-> configured target Relay `/api/v1/receive`
-> remote Relay stores receipt/message
-> optional local handler dispatch
```

```text
Relay
-> `relay:hq-sync` / `relay:hq-heartbeat`
-> Hub HQ API
-> hub_registry_hubs / hub_registry_links / relay_node_settings
```

### 9. Offline-First Behavior

Relay is explicitly store-and-forward. Local submissions persist while upstream is unavailable, and delivery retries use `RELAY_DELIVERY_BACKOFF_MINUTES`. Local handler dispatch also has retry/backoff settings. Conflict handling beyond message idempotency/content hash/receipts was not confirmed in code. Owner clarification: minimal incident conflict resolution is expected because incidents are not synced as editable records across nodes; nodes consolidate SITREPs from sources plus their own SITREP and send periodic SITREP upstream.

Planned responder/helper mobile updates are intended to sync upward through Relay when connectivity returns. The exact assignment/status/field-report envelope was not confirmed in the reviewed code.

### 10. Integration with Other PBB Apps

| Integration | Direction | Protocol / Method | Purpose | Code Evidence |
|---|---|---|---|---|
| Local PBB apps -> Relay | inbound to Relay | HTTP API + API key | Submit messages, inbox, handlers | `routes\api.php`; `AuthenticateRelayClient.php` |
| Backend apps -> Relay relationship resolver | inbound to Relay | HTTP API + Relay client auth | Resolve hub relationship credentials | `routes\api.php`; `RelationshipController.php`; `RelayRelationshipResolver.php` |
| Relay -> Relay peers | outbound/inbound | HTTP API, shared key/HMAC/mtls modes | Hub-to-hub sync | `config\relay.php`; `AuthenticateRelayHub.php` |
| Relay -> Hub HQ | outbound | HTTP API token | Registry sync/heartbeat | `RelayHqSyncCommand.php`; `RelayHqHeartbeatCommand.php`; `config\relay.php` |
| Relay -> Maestro | outbound | HTTP telemetry token | Worker heartbeat/events | `config\relay.php`; `app\Relay\Maestro` |

### 11. Deployment and Runtime Architecture

Relay is a Laravel app with `public/index.php`, Composer/npm, MySQL, and queue workers. `config/relay.php` defines delivery queue names, retry backoff, hub auth modes, HQ API, upload storage, and Maestro telemetry. Installer/browser setup files exist under `installer` and `.installer`. Admin UI is served under `/relay`.

### 12. Security and Privacy Notes

| Risk | Severity | Evidence | Suggested Fix |
|---|---|---|---|
| API keys and handler auth tokens exist in DB | High | `hub_relay_clients.api_key`, `hub_relay_handlers.auth_token` migrations/models | Store only hashed/digested secrets where possible; rotate and mask in UI |
| Hub auth modes include shared key | Medium | `config\relay.php` `RELAY_HUB_AUTH_MODE` | Prefer HMAC or certificate-bound auth for production |
| Payloads and attachments may contain sensitive incident data | High | `hub_relay_messages.payload`, attachments tables | Encrypt storage/backup; define retention and access controls |
| Relationship resolver returns shared credentials | High | `POST /api/v1/relationships/resolve`; `RelayRelationshipResolver.php` | Keep backend-only, require Relay client auth, and prevent browser/front-end callers |
| Resolver topology `domain` can be misused as app URL | Medium | Chat-log/code review clarified `domain` comes from `hub.json` topology, not source Hotline media URL | Add explicit app endpoint metadata such as `source_hotline_url` or `apps.hotline.base_url` |
| Diagnostics unauthenticated for phase 1 | Medium | `routes\api.php` comments no auth for diagnostics | Limit diagnostics details or require local-network/admin auth |

### 13. Realtime Communication

No realtime functionality found / Not confirmed from code. Relay is HTTP/queue based.

### 14. Mapping and Geolocation

Relay stores hub PSGC/location hierarchy fields in registry tables (`country_code`, `reg_code`, `prov_code`, `citymun_code`, `brgy_code`) but no map rendering or geospatial service was found.

### 15. Background Jobs, Schedulers, and Maintenance Tasks

| Task | Schedule / Trigger | Purpose | Code Evidence |
|---|---|---|---|
| `ProcessRelayDelivery` | Queue worker | Deliver messages to next hop | `app\Jobs\ProcessRelayDelivery.php` |
| `DispatchRelayToLocalHandler` | Queue worker | Send messages to local handlers | `app\Jobs\DispatchRelayToLocalHandler.php` |
| `relay:hq-sync` | CLI/manual/scheduler external | Sync HQ registry | `app\Console\Commands\RelayHqSyncCommand.php` |
| `relay:hq-heartbeat --once` | CLI/long-running external | Send HQ heartbeat | `app\Console\Commands\RelayHqHeartbeatCommand.php` |
| `relay:maestro-probe` | Manual | Test Maestro telemetry | `routes\console.php` |

### 16. Configuration and Environment Variables

| Variable / Config | Purpose | Required | Default / Example | Related Module |
|---|---|---|---|---|
| `RELAY_LOCAL_HUB_ID` | Local relay identity | Yes | none | Registry/delivery |
| `RELAY_TARGETS`, `RELAY_HUBS`, `RELAY_TARGET_OVERRIDES`, `RELAY_HUB_CREDENTIALS` | Target topology | Conditional | JSON | Delivery |
| `RELAY_HUB_AUTH_MODE` | Peer auth mode | Yes | `shared_key` | Hub auth |
| `RELAY_DELIVERY_MAX_ATTEMPTS`, `RELAY_DELIVERY_BACKOFF_MINUTES` | Retry policy | Yes | `5`, `1,5,15,60,360` | Delivery |
| `RELAY_LOCAL_HANDLER_*` | Handler dispatch retry/timeouts | Yes | queue/backoff defaults | Handlers |
| `RELAY_HQ_API_*`, `RELAY_HQ_HEARTBEAT_*` | HQ registry/heartbeat | Optional | disabled by default | Hub HQ |
| `RELAY_MAESTRO_*` | Worker telemetry | Optional | disabled by default | Maestro |
| `relay.credentials.snapshot_path` / `storage/app/relay/hub-credentials.json` | Relationship resolver credential snapshot | Required for resolver use | local storage path | Credentials resolver |

### 17. Known Technical Debt and Gaps

| Area | Issue | Evidence | Recommended Next Step |
|---|---|---|---|
| SITREP envelope policy | Idempotency exists, but the periodic upstream SITREP envelope/idempotency policy is not confirmed in code | `RelayIdempotencyService`; owner clarification about SITREP upstream flow | Define SITREP envelope, schedule, source attribution, and idempotency keys |
| Responder/helper envelopes | Planned responder/helper field updates should sync through Relay, but envelope/API contract was not found | Owner clarification; no reviewed implementation | Define assignment, status, notes, photo, and outcome-report envelope contracts |
| FRP tunnel lifecycle | Owner clarified PBB-managed FRP tunnel topology, but code-level FRPS/FRPC provisioning was not found in reviewed Relay code | Owner clarification | Document Hub HQ control-plane, Relay endpoint, FRPC credential, and rotation lifecycle |
| Diagnostics auth | Phase comment says diagnostics no auth | `routes\api.php` | Harden diagnostics before field deployment |
| App endpoint metadata | Relationship resolver returns credentials/topology but not a source Hotline media base URL | Resolver implementation and post-briefing chat-log guidance | Add app endpoint metadata contract before Support/Hotline media integrations depend on remote URL discovery |

### 18. Testing Status

PHPUnit tests cover envelope validation, message submission, inbound receive, delivery processing, upload flow, handlers, admin screens, HQ heartbeat/sync, dashboard, and installer flow. Run with `composer test` or `php artisan test`.

### 19. Evidence Summary

Evidence:
- `C:\wamp64\www\pbb\relay\README.md`
- `C:\wamp64\www\pbb\relay\routes\api.php`
- `C:\wamp64\www\pbb\relay\routes\web.php`
- `C:\wamp64\www\pbb\relay\config\relay.php`
- `C:\wamp64\www\pbb\relay\database\migrations`
- `C:\wamp64\www\pbb\relay\app\Jobs`
- `C:\wamp64\www\pbb\relay\app\Relay`
- `C:\wamp64\www\pbb\relay\app\Http\Controllers\Api\Relay\Credentials\RelationshipController.php`
- `C:\wamp64\www\pbb\relay\app\Relay\Credentials\RelayRelationshipResolver.php`
- `C:\wamp64\www\pbb\relay\docs\hub-relay-api-reference.md`

---

## App: PBB Hub / Hub HQ

### 1. Executive Technical Summary

PBB Hub / Hub HQ is a Laravel app for hub registry, hub heartbeat/token management, geodata, users, settings, and recovery backups. It appears HQ/admin-facing and coordinates registered hubs rather than directly handling incidents. It runs as a Laravel web app under WAMP/Apache or a PHP host with MySQL.

### 2. Repository Overview

| Area | Details |
|---|---|
| App Name | PBB Hub / Hub HQ |
| Local Path | `C:\wamp64\www\pbb\hub.ph` |
| Main Language | PHP, JavaScript |
| Main Framework | Laravel 12 |
| Frontend Framework | Vite, app shell, vendored Helper UI |
| Backend Framework | Laravel |
| Database | MySQL production; SQLite tests |
| Realtime Technology | None confirmed |
| Queue / Worker System | Laravel scheduler; queue tables present |
| Package Manager | Composer, npm |
| Runtime Requirements | PHP `^8.2`, MySQL, Composer, Node/npm for build |
| Main Entry Points | `public/index.php`, `routes/web.php`, `routes/api.php`, `artisan` |
| Important Config Files | `.env.example`, `routes/api.php`, `geodata/ph.json`, `composer.json`, `package.json` |
| Important Environment Variables | Standard Laravel `APP_*`, `DB_*`, session/cache/queue variables |
| Deployment Target | HQ/cloud or local HQ node web app |

### 3. App Purpose and PBB Role

Hub solves hub/node registration and management, hub token lifecycle, heartbeat ingestion/history, geodata lookup/editing, settings, users, and recovery bundle generation. Relay depends on Hub/HQ APIs for registry sync/heartbeat when configured. If Hub is unavailable, local Relay may continue manually, but HQ registry, token issuance, heartbeat visibility, and recovery UI stop.

### 4. User Roles and Permissions

| Role | Purpose | Capabilities | Code Evidence |
|---|---|---|---|
| authenticated user | Basic app access | Bootstrap, session, hub list, geodata routes | `routes\api.php` auth group |
| admin | Administration | Hub token management, users, settings, recovery backups | `routes\api.php` `role:admin`; `EnsureRole.php` |
| hub token client | Hub/Relay machine client | Submit heartbeat, read hubs | `routes\api.php` `hub.token`; `AuthenticateHubToken.php` |

### 5. Main Features and Modules

### Hub Registry

Purpose: Create/update/list hubs and uplink relationships.
Main Code: `HubController`, `Hub.php`, `HubUplink.php`.
Database Tables: `hubs`, `hub_uplinks`.
APIs / Routes: `/api/admin/hubs`, `/api/hubs`, `/api/hubs/{hub}`.
Offline Behavior: Local HQ DB works without internet; remote heartbeat requires network.
Sync Behavior: Relay can sync registry from Hub.
Related PBB Apps: Relay.
Evidence: `routes\api.php`; migrations.

### Hub Tokens and Heartbeats

Purpose: Issue/revoke hub tokens and accept heartbeat reports.
Main Code: `HubTokenController`, `HubHeartbeatController`, `AuthenticateHubToken`.
Database Tables: `hub_tokens`, `hub_heartbeat_checks`, heartbeat fields on `hubs`.
APIs / Routes: `/api/admin/hubs/{hub}/token`, `POST /api/hubs/heartbeat`.
Offline Behavior: Token admin local; heartbeat requires hub-to-Hub connectivity.
Sync Behavior: Heartbeat state stored.
Related PBB Apps: Relay.
Evidence: `routes\api.php`; migrations.

### Geodata

Purpose: Region/province/city/barangay lookup and coordinate edits.
Main Code: `GeodataController`, `GeoRegion/Province/City/Barangay` models, `geodata/ph.json`.
Database Tables: geodata models exist; migrations not fully confirmed in generated list.
APIs / Routes: `/api/geodata/regions`, `/provinces`, `/cities`, `/barangays`, coordinate update routes; `/geodata/ph.json`.
Offline Behavior: Uses local geodata file/database.
Sync Behavior: Unknown / Not confirmed from code.
Related PBB Apps: MapServer, Relay, Hotline may use PSGC codes.
Evidence: `routes\api.php`; `routes\web.php`; `geodata`.

### Recovery Bundles

Purpose: Create/list/download/delete backups and restore scripts.
Main Code: `RecoveryBundleController`, `RecoveryBundleService`, `MySqlDatabaseDumper`, `RestoreScriptFactory`.
Database Tables: likely file storage plus database dump; specific backup table not confirmed from migrations output.
APIs / Routes: `/api/admin/recovery/backups`.
Offline Behavior: Local backup generation.
Sync Behavior: Unknown / Not confirmed from code.
Related PBB Apps: Hub itself.
Evidence: `routes\api.php`; `app\Services\Recovery`.

### 6. Database Schema Summary

| Table | Purpose | Important Columns | Relationships / Notes |
|---|---|---|---|
| users | Hub users | name, email, password, role | Admin role enforced in routes |
| settings | Config/settings | key, value JSON, description | Admin settings |
| hubs | Registered hubs | name, code, deployment, PSGC codes, domain, status, last_seen_at | Central registry |
| hub_uplinks | Hub hierarchy/links | hub_id, uplink_hub_id, uplink_domain, uplink_type, is_primary | Hub topology |
| hub_tokens | Machine tokens | hub_id, token_hash, last_used_at, revoked_at | Hub heartbeat/API auth |
| hub_heartbeat_checks | Heartbeat history | hub_id, request_url, outcome, health_status, app/protocol version, payload_json | Scheduled checks/history |

Relationship map:

```text
users
hubs
  -> hub_uplinks
  -> hub_tokens
  -> hub_heartbeat_checks
settings
```

### 7. API and Route Inventory

| Method | Path / Endpoint | Purpose | Auth | Handler / File | Notes |
|---|---|---|---|---|---|
| POST | `/api/workspace/user-access` | Workspace access check | none in route | `WorkspaceController` | |
| GET/POST | `/api/bootstrap`, `/api/csrf-token`, `/api/login`, `/api/logout`, `/api/user` | Session/bootstrap | web/auth | `AuthController`, `BootstrapController` | |
| GET/POST/PUT/DELETE | `/api/admin/hubs` | Hub CRUD | auth | `HubController` | |
| GET/POST/DELETE | `/api/admin/hubs/{hub}/token` | Hub token lifecycle | auth + admin | `HubTokenController` | |
| GET/POST/PUT/DELETE | `/api/users` | User admin | auth + admin | `UserController` | |
| GET/POST | `/api/geodata/*` | Geodata lookup/coords | auth | `GeodataController` | |
| GET/POST | `/api/settings` | Settings | auth + admin | `SettingsController` | |
| CRUD | `/api/admin/recovery/backups` | Recovery bundles | auth + admin | `RecoveryBundleController` | |
| POST | `/api/hubs/heartbeat` | Hub heartbeat | hub.token | `HubHeartbeatController` | Machine endpoint |
| GET | `/api/hubs`, `/api/hubs/{hub}` | Hub registry read | hub.token | `HubController` | Machine endpoint |
| Web | `/`, `/geodata`, `/hubs`, `/users`, `/recovery`, `/api/v1` | App shell/docs | none route-level | `routes\web.php` | SPA-style views |
| Console | `app:prune-data-api-cache`, `app:check-hub-heartbeats` | Maintenance | CLI | `routes\console.php` | |

### 8. Data Flow and Operational Flow

```text
Relay/node
-> `POST /api/hubs/heartbeat` with hub token
-> Hub validates token hash
-> hubs.last_seen/status and hub_heartbeat_checks
-> admin dashboard views status/history
```

```text
Admin
-> `/api/admin/hubs`
-> hubs + hub_uplinks
-> `/api/admin/hubs/{hub}/token`
-> hub_tokens
-> token returned/rotated for node use
```

### 9. Offline-First Behavior

Hub can run on a local PHP/MySQL host, but owner clarification says PBB HQ is planned as a cloud app in production. Its HQ role implies remote nodes need network access to send heartbeats and fetch registry data. No local outbox or retry queue was confirmed in Hub itself; scheduled heartbeat checks exist. Owner clarification: production topology follows Philippine administrative levels from barangay to city/municipality to province to cloud HQ.

### 10. Integration with Other PBB Apps

| Integration | Direction | Protocol / Method | Purpose | Code Evidence |
|---|---|---|---|---|
| Relay -> Hub | HTTP + hub token | Heartbeat and registry fetch | Node/HQ coordination | Hub `routes\api.php`; Relay `config\relay.php` |
| Hub -> Relay/node | HTTP scheduled checks | Check hub heartbeat endpoints | Monitoring status | `routes\console.php`; `HubHeartbeatChecker.php` |
| Helper UI | Hub embeds Helper | Static JS/CSS | UI components | `public\vendor\helpers.pbb.ph` |
| Map/geodata consumers | Hub -> clients | JSON/API | PSGC/geodata | `routes\web.php`; `GeodataController` |

### 11. Deployment and Runtime Architecture

Laravel 12 app with Composer/npm. WAMP-compatible local deployment is present. `.env.example` exists. Public root is `public/`. Scheduler defines cache pruning and heartbeat checks. No Docker file was found in the top-level inventory.

### 12. Security and Privacy Notes

| Risk | Severity | Evidence | Suggested Fix |
|---|---|---|---|
| Hub tokens are powerful machine credentials | High | `hub_tokens.token_hash`; `hub.token` middleware routes | Rotate tokens, restrict scopes/IPs where possible |
| Geodata coordinate edit routes are auth-only, not role-limited | Medium | `routes\api.php` geodata coordinate updates inside auth group only | Confirm intended permission; add admin role if needed |
| Recovery bundles may include database secrets/data | High | `RecoveryBundleService`, MySQL dumper | Encrypt bundles and restrict download/delete to audited admins |

### 13. Realtime Communication

No realtime functionality found / Not confirmed from code.

### 14. Mapping and Geolocation

Hub includes geodata APIs and `geodata/ph.json`; hub records include PSGC fields and domain/deployment status. No map tile serving was found in Hub.

### 15. Background Jobs, Schedulers, and Maintenance Tasks

| Task | Schedule / Trigger | Purpose | Code Evidence |
|---|---|---|---|
| `app:prune-data-api-cache --hours=168` | Daily 02:30 | Prune cached API data | `routes\console.php` |
| `app:check-hub-heartbeats` | Every minute | Check hub heartbeats | `routes\console.php`; `HubHeartbeatChecker.php` |

### 16. Configuration and Environment Variables

| Variable / Config | Purpose | Required | Default / Example | Related Module |
|---|---|---|---|---|
| Laravel `APP_*`, `DB_*`, session/cache/queue variables | Core app | Yes | `.env.example` masked | Laravel |
| `geodata/ph.json` | Static geodata payload | Yes for web geodata route | local file | Geodata |

### 17. Known Technical Debt and Gaps

| Area | Issue | Evidence | Recommended Next Step |
|---|---|---|---|
| Topology semantics | Barangay/city/municipality/province hierarchy meaning is present as codes; owner clarified production hierarchy, but field-level deployment workflow is not fully documented in code | `hubs`/`hub_uplinks` migrations; owner clarification | Add deployment topology guide with Hub HQ identity fields |
| Recovery security | Bundle encryption/retention not confirmed | Recovery service files | Define recovery bundle security policy |
| Role granularity | Only `admin` role was confirmed; non-admin capabilities broad in auth group | `routes\api.php` | Add explicit roles/permissions if needed |

### 18. Testing Status

PHPUnit tests exist for recovery bundle service, hub uplink, hub heartbeat, and heartbeat history. Run with `composer test` or `php artisan test`.

### 19. Evidence Summary

Evidence:
- `C:\wamp64\www\pbb\hub.ph\README.md`
- `C:\wamp64\www\pbb\hub.ph\routes\api.php`
- `C:\wamp64\www\pbb\hub.ph\routes\web.php`
- `C:\wamp64\www\pbb\hub.ph\routes\console.php`
- `C:\wamp64\www\pbb\hub.ph\database\migrations`
- `C:\wamp64\www\pbb\hub.ph\app\Http\Middleware\AuthenticateHubToken.php`
- `C:\wamp64\www\pbb\hub.ph\app\Services\Recovery`

---

## App: PBB Maestro

### 1. Executive Technical Summary

PBB Maestro is a Laravel monitoring/telemetry app for PBB worker processes. It records application registrations, hashed telemetry tokens, worker heartbeats, worker events, and derived worker status. It is operator/admin-facing and infrastructure-level, intended to observe services such as Relay and Realtime. It does not itself restart processes in the inspected code. Owner clarification: this is intentional; Maestro is strictly observer-only, and Windows services registered during Kit Setup own process lifecycle/recovery.

### 2. Repository Overview

| Area | Details |
|---|---|
| App Name | PBB Maestro |
| Local Path | `C:\wamp64\www\pbb\maestro` |
| Main Language | PHP, JavaScript |
| Main Framework | Laravel 12 |
| Frontend Framework | Vite, Helper UI |
| Backend Framework | Laravel |
| Database | MySQL production; SQLite tests |
| Realtime Technology | None confirmed |
| Queue / Worker System | Laravel scheduler; telemetry receiver |
| Package Manager | Composer, npm |
| Runtime Requirements | PHP `^8.2`, MySQL |
| Main Entry Points | `public/index.php`, `routes/api.php`, `routes/web.php`, `artisan` |
| Important Config Files | `.env.example`, `config/maestro.php`, `routes/api.php`, `release.json` |
| Important Environment Variables | `MAESTRO_*`, `DB_*`, Laravel app/session variables |
| Deployment Target | Local node monitoring service |

### 3. App Purpose and PBB Role

Maestro solves process health visibility: apps register, issue telemetry tokens, workers send heartbeats/events, and Maestro computes worker state. Relay and Realtime have code/config to send telemetry to Maestro. If Maestro is unavailable, monitored apps can continue running, but central worker health visibility and stale detection UI are unavailable.

### 4. User Roles and Permissions

| Role | Purpose | Capabilities | Code Evidence |
|---|---|---|---|
| authenticated operator/admin user | Uses Maestro UI/API | Applications, workers, worker events, token creation | `routes\api.php` auth group |
| telemetry token client | Machine telemetry sender | Post worker heartbeats/events for an app_code | `EnsureTelemetryToken.php`; `routes\api.php` |

No formal role column was confirmed on `users`; access appears auth-based plus telemetry-token middleware.

### 5. Main Features and Modules

### Application Registry

Purpose: Define monitored apps.
Main Code: `ApplicationIndexController`, `ApplicationStoreController`, `MaestroApplication`.
Database Tables: `maestro_applications`.
APIs / Routes: `GET/POST /api/v1/applications`.
Offline Behavior: Local.
Sync Behavior: None.
Related PBB Apps: Relay, Realtime.
Evidence: `routes\api.php`; migrations.

### Telemetry Tokens

Purpose: Issue hashed ingestion tokens per app.
Main Code: `ApplicationTokenStoreController`, `MaestroTelemetryToken`, `EnsureTelemetryToken`.
Database Tables: `maestro_telemetry_tokens`.
APIs / Routes: `POST /api/v1/applications/{app_code}/tokens`; telemetry middleware accepts bearer or configured header.
Offline Behavior: Local.
Sync Behavior: None.
Related PBB Apps: Relay, Realtime.
Evidence: `README.md`; `routes\api.php`; `app\Http\Middleware\EnsureTelemetryToken.php`.

### Worker Heartbeats and Events

Purpose: Ingest worker status and job events.
Main Code: `WorkerHeartbeatController`, `WorkerEventController`, `WorkerStatusResolver`.
Database Tables: `maestro_workers`, `maestro_worker_events`.
APIs / Routes: `POST /api/v1/telemetry/workers/heartbeat`, `POST /api/v1/telemetry/worker-events`.
Offline Behavior: Requires app-to-Maestro LAN/HTTP connectivity.
Sync Behavior: None; telemetry is local monitoring data.
Related PBB Apps: Relay, Realtime.
Evidence: `routes\api.php`; `README.md`; `config\maestro.php`.

### Stale Worker Reconciliation

Purpose: Recompute stale worker statuses every minute.
Main Code: `StaleWorkerReconciler`, `routes\console.php`.
Database Tables: `maestro_workers`.
APIs / Routes: Console `maestro:reconcile-stale-workers`.
Offline Behavior: Local.
Sync Behavior: None.
Related PBB Apps: Monitored apps.
Evidence: `routes\console.php`; `WorkerStatusResolver.php`.

### 6. Database Schema Summary

| Table | Purpose | Important Columns | Relationships / Notes |
|---|---|---|---|
| users | Maestro UI users | name, email, password | No role column confirmed |
| maestro_applications | Monitored apps | app_code, display_name, environment, base_url, is_active, meta_json | Parent for tokens/workers |
| maestro_telemetry_tokens | Ingestion auth tokens | maestro_application_id, label, token_hash, last_used_at, revoked_at | Hashed tokens |
| maestro_workers | Current worker state | app id, worker_id, host, queue, status, heartbeat/job timestamps, meta_json | Current snapshot |
| maestro_worker_events | Worker/job event log | event_id, worker_id, event_type, queue, job_type, outcome, payload_json | Idempotent event ID |

Relationship map:

```text
maestro_applications
  -> maestro_telemetry_tokens
  -> maestro_workers
       -> maestro_worker_events
users
```

### 7. API and Route Inventory

| Method | Path / Endpoint | Purpose | Auth | Handler / File | Notes |
|---|---|---|---|---|---|
| GET | `/up` | Health page/JSON | none | `routes\web.php` | |
| GET | `/` | Maestro UI shell | none route-level | `routes\web.php` | Bootstrap passed |
| GET/POST | `/api/bootstrap`, `/api/csrf-token`, `/api/login`, `/api/user`, `/api/logout` | Session/account | web/auth | `routes\api.php` | |
| GET/POST | `/api/v1/applications` | App registry | web + auth | V1 controllers | |
| POST | `/api/v1/applications/{app_code}/tokens` | Create telemetry token | web + auth | `ApplicationTokenStoreController` | |
| GET | `/api/v1/workers`, `/api/v1/worker-events` | Worker views | web + auth | Worker index controllers | |
| POST | `/api/v1/telemetry/workers/heartbeat` | Ingest heartbeat | telemetry token | `WorkerHeartbeatController` | |
| POST | `/api/v1/telemetry/worker-events` | Ingest event | telemetry token | `WorkerEventController` | |
| Console | `maestro:reconcile-stale-workers` | Mark stale workers | CLI/scheduled | `routes\console.php` | Every minute |

### 8. Data Flow and Operational Flow

```text
Monitored app worker
-> POST heartbeat/event with `app_code` and telemetry token
-> Maestro middleware validates token belongs to app
-> maestro_workers upsert / maestro_worker_events append
-> WorkerStatusResolver computes starting/idle/busy/stale/stopped
-> UI reads `/api/v1/workers`
```

### 9. Offline-First Behavior

Maestro is LAN/local capable. It does not require internet unless monitored apps are remote. No sync/outbox/conflict logic was found.

### 10. Integration with Other PBB Apps

| Integration | Direction | Protocol / Method | Purpose | Code Evidence |
|---|---|---|---|---|
| Relay -> Maestro | HTTP telemetry token | Worker heartbeat/events | Relay worker monitoring | Relay `config\relay.php`; Maestro `routes\api.php` |
| Realtime -> Maestro | HTTP telemetry token | WebSocket gateway heartbeat/events | Realtime monitoring | Realtime `config\realtime.php`; `RealtimeServeCommand.php` |

### 11. Deployment and Runtime Architecture

Laravel web app with `public/`, Composer/npm, MySQL, scheduler. `config/maestro.php` controls stale thresholds, telemetry token header, trace settings, and slow request threshold. Installer docs exist, and `release.json` declares bundle metadata.

### 12. Security and Privacy Notes

| Risk | Severity | Evidence | Suggested Fix |
|---|---|---|---|
| Telemetry tokens grant ingest capability | Medium | `maestro_telemetry_tokens.token_hash`; `EnsureTelemetryToken.php` | Rotate/revoke tokens; keep tokens masked after creation |
| No explicit UI role model confirmed | Medium | `users` model lacks role; routes use `auth` only for management APIs | Add role/permission model if multiple operators are expected |
| Worker metadata may leak host/process information | Medium | `maestro_workers` host_name, process_id/meta | Treat telemetry as operationally sensitive |

### 13. Realtime Communication

No realtime functionality found / Not confirmed from code.

### 14. Mapping and Geolocation

No mapping/geolocation functionality found / Not confirmed from code.

### 15. Background Jobs, Schedulers, and Maintenance Tasks

| Task | Schedule / Trigger | Purpose | Code Evidence |
|---|---|---|---|
| `maestro:reconcile-stale-workers` | Every minute | Persist stale worker states | `routes\console.php` |

### 16. Configuration and Environment Variables

| Variable / Config | Purpose | Required | Default / Example | Related Module |
|---|---|---|---|---|
| `MAESTRO_STARTING_THRESHOLD_SECONDS` | Starting state window | No | 15 | Worker status |
| `MAESTRO_STALE_THRESHOLD_SECONDS` | Stale heartbeat threshold | No | 45 | Worker status |
| `MAESTRO_CLOCK_SKEW_THRESHOLD_SECONDS` | Clock skew metadata | No | 60 | Telemetry |
| `MAESTRO_TELEMETRY_TOKEN_HEADER` | Token header name | No | `X-Telemetry-Token` | Middleware |
| `MAESTRO_TELEMETRY_TRACE` | Trace logging | No | false | Middleware |

### 17. Known Technical Debt and Gaps

| Area | Issue | Evidence | Recommended Next Step |
|---|---|---|---|
| Process control | Maestro is observer-only by design | Routes only show telemetry/app CRUD; owner clarification confirms observer-only scope | Document Kit Setup/Windows service recovery responsibility |
| UI authorization | No role column confirmed | `User.php`; routes auth-only | Add operator/admin role model if required |
| Alerting | No notification/alert delivery found | No mail/SMS/alert routes found | Add alerting if monitoring must notify humans |

### 18. Testing Status

Feature tests exist for telemetry ingestion and operator API. Run with `composer test` or `php artisan test`.

### 19. Evidence Summary

Evidence:
- `C:\wamp64\www\pbb\maestro\README.md`
- `C:\wamp64\www\pbb\maestro\routes\api.php`
- `C:\wamp64\www\pbb\maestro\routes\console.php`
- `C:\wamp64\www\pbb\maestro\config\maestro.php`
- `C:\wamp64\www\pbb\maestro\database\migrations`
- `C:\wamp64\www\pbb\maestro\app\Services\Maestro\WorkerStatusResolver.php`

---

## App: PBB Realtime Server

### 1. Executive Technical Summary

PBB Realtime is a Laravel 10 plus Ratchet WebSocket gateway. It provides token admission, WebSocket room/presence/chat/call/app-event/media transport, backend event ingress, media chunk forwarding, admin console, policy/client/project/user management, usage telemetry, and Maestro process telemetry. It is core realtime infrastructure for product apps such as Hotline.

### 2. Repository Overview

| Area | Details |
|---|---|
| App Name | PBB Realtime |
| Local Path | `C:\wamp64\www\pbb\realtime` |
| Main Language | PHP, JavaScript |
| Main Framework | Laravel 10 + Ratchet |
| Frontend Framework | Vite admin UI |
| Backend Framework | Laravel |
| Database | MySQL production; SQLite tests |
| Realtime Technology | Ratchet WebSocket server at `/realtime` |
| Queue / Worker System | Gateway event loop drains DB queues; console media dispatcher exists |
| Package Manager | Composer, npm |
| Runtime Requirements | PHP `^8.1`, MySQL, long-running `php artisan realtime:serve`, Node/npm for assets |
| Main Entry Points | `routes/api.php`, `routes/web.php`, `app\Console\Commands\RealtimeServeCommand.php`, `app\Realtime\WebSocket\RealtimeGateway.php` |
| Important Config Files | `.env.example`, `config/realtime.php`, `release.json`, `installer/*` |
| Important Environment Variables | `REALTIME_TOKEN_SIGNING_SECRET`, `REALTIME_TRUSTED_ISSUERS`, `REALTIME_PUBLIC_WEBSOCKET_URL`, `REALTIME_WS_BIND_ADDRESS`, `REALTIME_WS_PUBLIC_HOST`, `REALTIME_WS_PORT`, `MAESTRO_TELEMETRY_*` |
| Deployment Target | Local Realtime gateway service plus Laravel HTTP/admin app |

### 3. App Purpose and PBB Role

Realtime owns the shared live communication layer between product backends and browser clients. Product apps authenticate users and issue signed short-lived tokens. Realtime validates tokens, capabilities, room policy, publishes events, tracks presence/sessions/audit/usage, and drains backend-published events/media chunks to rooms. If Realtime is unavailable, live calls, presence, chat, app events, and media chunk transport stop; HTTP product apps may still store local data.

### 4. User Roles and Permissions

| Role | Purpose | Capabilities | Code Evidence |
|---|---|---|---|
| admin user | Full admin surface | Can access all clients; manages clients/projects/policies/users | `app\Models\User.php` `isAdmin`; `routes\web.php` |
| operator user | Admin console operator | Access admin surface if `is_operator`; client access limited by assignments | `EnsureOperator.php`; `User.php` |
| regular assigned user | Client-scoped user | Can access assigned clients if allowed by admin controllers | `realtime_client_user` migration; `User.php` |
| realtime token subject | Browser realtime session | Capabilities from signed token: `session.connect`, `room.join`, `presence.publish`, etc. | `RealtimeGateway.php`; `RealtimeTokenValidator.php` |
| trusted backend client | Product backend | Publish server events using backend ingress secret | `EventPublishController.php`; `BackendIngressSecret.php` |

### 5. Main Features and Modules

### Session Admission

Purpose: Validate short-lived realtime tokens and create session metadata.
Main Code: `RealtimeSessionController`, `RealtimeTokenValidator`, PHP SDK.
Database Tables: `realtime_sessions`, `realtime_clients`, `realtime_projects`.
APIs / Routes: `POST /api/realtime/session`.
Offline Behavior: Works on LAN if local Realtime HTTP app and DB are running.
Sync Behavior: None.
Related PBB Apps: Hotline.
Evidence: `routes\api.php`; `README.md`.

### WebSocket Gateway

Purpose: Room joins/leaves, presence, chat, app events, call signaling, media chunks.
Main Code: `RealtimeGateway`, `RealtimeServeCommand`.
Database Tables: `realtime_sessions`, `realtime_usage_buckets`, `realtime_media_chunks`, `realtime_server_events`.
APIs / Routes: `php artisan realtime:serve`; Ratchet route `/realtime`.
Offline Behavior: LAN-only possible. Room/presence state is in memory per process.
Sync Behavior: Backend events/media chunks are persisted then drained by gateway.
Related PBB Apps: Hotline, any product app using SDK.
Evidence: `RealtimeGateway.php`; `RealtimeServeCommand.php`.

### Backend Event Ingress

Purpose: Trusted product backends queue server-originated events.
Main Code: `EventPublishController`, `RealtimeEventIngressGate`, `RealtimeEventPublishQueue`, `RealtimeEventPublishDispatcher`.
Database Tables: `realtime_server_events`.
APIs / Routes: `POST /api/v1/events/publish`.
Offline Behavior: HTTP can queue while gateway is down; delivery waits for gateway drain.
Sync Behavior: Queue status pending/published/failed.
Related PBB Apps: Hotline.
Evidence: `routes\api.php`; `config\realtime.php`; `README.md`.

### Media Chunk Forwarding

Purpose: Accept base64/binary media chunks, spool/queue, forward to product backend ingest.
Main Code: `RealtimeMediaChunkQueue`, `RealtimeMediaChunkDispatcher`, `RealtimeMediaChunkForwarder`, `RealtimeGateway`.
Database Tables: `realtime_media_chunks`.
APIs / Routes: WebSocket `media.chunk.prepare`, `media.chunk.publish`, binary frames; console `realtime:dispatch-media-chunks`.
Offline Behavior: Queue/spool local; forwarding requires configured product backend.
Sync Behavior: status pending/forwarded/failed.
Related PBB Apps: Hotline media ingest endpoints.
Evidence: `RealtimeGateway.php`; `config\realtime.php`; migrations.

### Admin Console

Purpose: Manage clients, projects, policies, sessions, audit, users, operations, telemetry.
Main Code: `app\Http\Controllers\Admin\*`.
Database Tables: `realtime_clients`, `realtime_projects`, `realtime_policies`, `users`, `realtime_audit_events`, usage tables.
APIs / Routes: `/admin`, `/api/admin/*`.
Offline Behavior: Local.
Sync Behavior: None.
Related PBB Apps: Maestro telemetry integration.
Evidence: `routes\web.php`; migrations.

### 6. Database Schema Summary

| Table | Purpose | Important Columns | Relationships / Notes |
|---|---|---|---|
| users | Admin/operator users | is_operator, user_type | Admin surface access |
| realtime_clients | Product client registry | client_code, project_code, status, issuer_identity, origin/policy profiles, backend secret hashes | Parent for projects/users |
| realtime_projects | Project scopes | client_id, project_code, allowed_origins, media_ingest_settings, product_query_forwarding_settings | Project-level policy |
| realtime_policies | Policy profiles | capability_profile, room_policy_profile, rate/session profiles, client_id | Authorization settings |
| realtime_client_user | User-client assignments | client_id, user_id, assignment_role | Non-admin access scope |
| realtime_sessions | Session records | session_id, client_code, project_code, app_code, user_identity, status | Runtime session history |
| realtime_server_events | Backend publish queue | publish_id, room, event_type, status, payload, queued/published/failed timestamps | Drained by gateway |
| realtime_media_chunks | Media forwarding queue | chunk_id, client/project, room, status, payload, queued/forwarded/failed timestamps | Product backend forwarding |
| realtime_audit_events | Admin/audit events | audit_id, actor, action, target, before/after state | Admin audit |
| realtime_usage_buckets | Usage counters | bucket_start, granularity, client/project, event_type | Metrics |
| realtime_runtime_settings | Runtime settings | setting_key, setting_value | Includes Maestro telemetry settings |

Relationship map:

```text
users
  -> realtime_client_user -> realtime_clients
realtime_clients
  -> realtime_projects
  -> realtime_policies
realtime_sessions
realtime_server_events
realtime_media_chunks
realtime_audit_events
realtime_usage_buckets
```

### 7. API and Route Inventory

| Method | Path / Endpoint | Purpose | Auth | Handler / File | Notes |
|---|---|---|---|---|---|
| GET | `/api/health`, `/api/ready`, `/api/metrics` | Status/readiness/metrics | none | `routes\api.php` | |
| POST | `/api/realtime/session` | Validate admission token | token in payload | `RealtimeSessionController` | |
| POST | `/api/v1/events/publish` | Backend event publish | ingress gate/secret | `EventPublishController` | Trace middleware |
| WS | `/realtime` | WebSocket gateway | signed realtime token/capabilities | `RealtimeServeCommand`, `RealtimeGateway` | Ratchet |
| GET | `/`, `/api/docs`, `/sdk-docs*` | Public status/docs | none | `routes\web.php` | |
| POST/GET | `/admin/login`, `/api/admin/login`, `/api/admin/bootstrap` | Admin session | web | `AdminAuthController`, `SessionStateController` | |
| CRUD | `/api/admin/clients`, `/policies`, `/projects`, `/users` | Admin management | auth + operator | `routes\web.php` | |
| GET | `/api/admin/sessions`, `/audit`, `/operations`, `/telemetry` | Admin views | auth + operator | `BrowserDataController` | |
| PATCH | `/api/admin/runtime-settings/maestro-telemetry` | Configure telemetry | auth + operator | `RuntimeSettingsController` | |
| Console | `realtime:serve` | Start WebSocket server | CLI | `RealtimeServeCommand` | |
| Console | `realtime:dispatch-media-chunks` | Dispatch media chunks | CLI | `RealtimeDispatchMediaChunksCommand` | |
| Console | `realtime:prune-usage-telemetry` | Prune usage telemetry | CLI | `PruneRealtimeUsageTelemetryCommand` | In tests |

### 8. Data Flow and Operational Flow

```text
Product backend (Hotline)
-> signs realtime token
-> Browser calls `/api/realtime/session`
-> Browser opens WebSocket `/realtime`
-> `room.join.request`
-> `presence.publish` / `chat.message.publish` / `call.signal.publish`
-> in-memory room broadcast
-> session/audit/usage DB side effects
```

```text
Product backend
-> `POST /api/v1/events/publish`
-> realtime_server_events status=pending
-> running `realtime:serve` periodic drain
-> room broadcast
-> status published/failed
```

```text
Browser media chunk
-> WebSocket `media.chunk.prepare` or `media.chunk.publish`
-> realtime_media_chunks + spool files
-> dispatcher/embedded drain
-> product backend media ingest endpoint
-> forwarded/failed outcome event
```

### 9. Offline-First Behavior

Realtime can run LAN-only. It does not require internet for local WebSocket communication if clients can reach the local host/port. Room membership and presence are in memory and not shared across multiple gateway processes. Owner clarification: this matches the intended topology of one shared Realtime gateway instance per node. HTTP event publish and media chunks can persist pending rows while the gateway or downstream backend is unavailable, but live clients need the gateway process. No cross-node sync or conflict handling was found.

### 10. Integration with Other PBB Apps

| Integration | Direction | Protocol / Method | Purpose | Code Evidence |
|---|---|---|---|---|
| Product apps -> Realtime | HTTP + WebSocket | Token admission, live events | Shared realtime | `routes\api.php`; `RealtimeGateway.php` |
| Realtime -> product backend | HTTP forwarding | Media chunks/product query forwarding | Backend-owned data persistence | `RealtimeMediaChunkForwarder.php`; `RealtimeProductQueryForwarder.php` |
| Realtime -> Maestro | HTTP telemetry token | Process heartbeat/events | Monitoring | `config\realtime.php`; `RealtimeServeCommand.php` |
| Admin/browser -> Realtime | HTTP web/API | Manage clients/projects/policies | Operations | `routes\web.php` |

### 11. Deployment and Runtime Architecture

Laravel HTTP app plus a long-running Ratchet process. `php artisan realtime:serve` binds `REALTIME_WS_BIND_ADDRESS`/`REALTIME_WS_PORT` and routes `/realtime`. Installer scaffolds `.env`, migrations, operator bootstrap, service artifacts, and acceptance validation. README states reverse proxy/TLS/firewall/DNS/service registration may require operator or Kit Setup completion.

### 12. Security and Privacy Notes

| Risk | Severity | Evidence | Suggested Fix |
|---|---|---|---|
| Token signing secret controls session admission | High | `REALTIME_TOKEN_SIGNING_SECRET` in `config\realtime.php` | Strong secret rotation and per-client issuer policy |
| Route-level origin allowlist intentionally disabled | Medium | `RealtimeServeCommand.php` route `['*']`; README | Ensure token, room, and capability policies are tested for every client |
| Room/presence state is in-memory | Medium | `RealtimeGateway.php` arrays; README | Avoid multi-process deployment until shared fanout/state exists |
| Media chunks may contain sensitive audio/video | High | `realtime_media_chunks`, spool path config | Encrypt at rest, limit retention, validate downstream endpoints |
| Admin access combines admin/operator logic | Medium | `EnsureOperator.php`; `User.php` | Review client assignment authorization coverage |

### 13. Realtime Communication

WebSocket events confirmed in `RealtimeGateway.php`: `session.auth.request`, `room.join.request`, `room.leave.request`, `session.health.request`, `presence.subscribe`, `presence.publish`, `app.event.publish`, `media.chunk.prepare`, `media.chunk.publish`, `chat.message.publish`, `sandbox.attachment.chunk.publish`, `call.signal.publish`, and `ping`. Binary media uses `PBBM` magic and version 1. Authentication uses signed tokens with capabilities and allowed rooms. LAN-only behavior is supported by local bind/public URL config.

### 14. Mapping and Geolocation

No mapping/geolocation functionality found / Not confirmed from code. Realtime can carry location payloads as app events, but geolocation semantics are product-owned.

### 15. Background Jobs, Schedulers, and Maintenance Tasks

| Task | Schedule / Trigger | Purpose | Code Evidence |
|---|---|---|---|
| `realtime:serve` | Long-running service | WebSocket gateway and periodic drain | `RealtimeServeCommand.php` |
| Event publish drain | Periodic timer in gateway | Drain `realtime_server_events` | `RealtimeServeCommand.php` |
| Media chunk drain | Periodic timer or CLI | Forward media chunks | `RealtimeServeCommand.php`; `RealtimeDispatchMediaChunksCommand.php` |
| Maestro heartbeat | Periodic timer | Report gateway process health | `RealtimeServeCommand.php` |
| Usage telemetry prune | CLI command | Cleanup metrics | `PruneRealtimeUsageTelemetryCommand.php` |

### 16. Configuration and Environment Variables

| Variable / Config | Purpose | Required | Default / Example | Related Module |
|---|---|---|---|---|
| `REALTIME_TOKEN_SIGNING_SECRET` | Validate JWT/admission tokens | Yes | blank default | Auth |
| `REALTIME_TRUSTED_ISSUERS` | Token issuer allowlist | Yes for production | blank | Auth |
| `REALTIME_PUBLIC_WEBSOCKET_URL` | Browser socket URL | Yes for clients | env value | Frontend |
| `REALTIME_WS_BIND_ADDRESS`, `REALTIME_WS_PUBLIC_HOST`, `REALTIME_WS_PORT` | Ratchet bind/public config | Yes | `127.0.0.1`, host, `8080` | Gateway |
| `REALTIME_*_RATE_LIMIT*` | Message/join/publish limits | No | defaults | Gateway |
| `REALTIME_MEDIA_CHUNK_*` | Media chunk spool/limits/dispatch | No | storage path, limits | Media |
| `MAESTRO_TELEMETRY_*` | Process telemetry | Optional | disabled | Maestro |

### 17. Known Technical Debt and Gaps

| Area | Issue | Evidence | Recommended Next Step |
|---|---|---|---|
| Multi-process scaling | Presence/rooms are in-memory and not shared | README; `RealtimeGateway.php` arrays | Add Redis/shared state before horizontal scaling |
| Fallback transport | No non-WebSocket fallback found | Gateway-only live transport | Define degraded call/chat behavior |
| Reverse proxy/TLS/firewall | Installer cannot always apply host policy | README installer limitation | Document Kit Setup responsibility per deployment |

### 18. Testing Status

Tests cover token validation, room policy, gateway behavior, media dispatcher, backend SDK, Realtime session, event publish, admin auth/client/project/policy/user flows, operations telemetry, and a Node sandbox smoke script. Run with `php artisan test`; `npm run test:sandbox-smoke` exists for sandbox shell smoke.

### 19. Evidence Summary

Evidence:
- `C:\wamp64\www\pbb\realtime\README.md`
- `C:\wamp64\www\pbb\realtime\routes\api.php`
- `C:\wamp64\www\pbb\realtime\routes\web.php`
- `C:\wamp64\www\pbb\realtime\config\realtime.php`
- `C:\wamp64\www\pbb\realtime\app\Realtime\WebSocket\RealtimeGateway.php`
- `C:\wamp64\www\pbb\realtime\app\Console\Commands\RealtimeServeCommand.php`
- `C:\wamp64\www\pbb\realtime\database\migrations`

---

## App: PBB MapServer

### 1. Executive Technical Summary

PBB MapServer is a plain PHP tile proxy/cache and boundary overlay server. It serves raster, vector, terrain, glyph, POI tiles, health/status, purge endpoints, and PSGC boundary GeoJSON overlays. It stores cached tiles and generated boundaries locally under `storage`. It depends on upstream tile providers unless cache/prepared tiles are available. Owner clarification: during node installation, Kit Setup Data Prep auto-populates MapServer with tiles for the boundary dictated by Hub HQ hub information.

### 2. Repository Overview

| Area | Details |
|---|---|
| App Name | PBB MapServer |
| Local Path | `C:\wamp64\www\mapserver` |
| Main Language | PHP, JavaScript |
| Main Framework | Plain PHP |
| Frontend Framework | Helper UI on homepage |
| Backend Framework | None; procedural PHP |
| Database | No database usage found / Not confirmed from code |
| Realtime Technology | None |
| Queue / Worker System | None; CLI data prep/populator scripts |
| Package Manager | None for runtime; vendored assets included |
| Runtime Requirements | PHP with cURL; writable storage/cache; Apache rewrite |
| Main Entry Points | `index.php`, `config.php`, `.htaccess`, `tools/*.php`, `installer/index.php` |
| Important Config Files | `.env`, `config.php`, `.htaccess`, `release.json`, `installer/schema/install.schema.json` |
| Important Environment Variables | `TILES_CACHE_ROOT`, `OSM_TILE_BASE_URL`, `VECTOR_TILE_BASE_URL`, `GLYPHS_BASE_URL`, `TERRAIN_TILE_BASE_URL`, `POI_BASE_URL`, `TILES_PURGE_TOKEN`, `STADIAMAPS_API_KEY`, `MAPTILER_API_KEY`, `TILES_CURL_SSL_VERIFY`, `TILES_CURL_CA_BUNDLE` |
| Deployment Target | WAMP/Apache PHP app |

### 3. App Purpose and PBB Role

MapServer provides local map tile caching and boundary overlays for apps such as Hotline. It supports offline map behavior after tiles/boundaries are cached or pre-populated. It depends on upstream OSM/Stadia/MapTiler or configured providers for cache misses. If unavailable, map display in apps that use local tile sources degrades or fails.

### 4. User Roles and Permissions

| Role | Purpose | Capabilities | Code Evidence |
|---|---|---|---|
| public tile client | Map consumers | GET tiles/boundaries/health | `README.md`; `index.php` |
| purge operator | Cache maintenance | POST purge endpoints with token | `README.md`; `config.php` `purge_token` |

### 5. Main Features and Modules

### Tile Proxy/Cache

Purpose: Serve and cache raster/vector/terrain/glyph/POI tiles.
Main Code: `index.php`, `config.php`.
Database Tables: None.
APIs / Routes: `/tiles/raster/{z}/{x}/{y}.png`, `/tiles/vector/{z}/{x}/{y}.pbf`, `/tiles/terrain/*`, `/tiles/glyphs/*`, `/tiles/poi/*`.
Offline Behavior: Cache hits work offline; cache misses need upstream.
Sync Behavior: None.
Related PBB Apps: Hotline.
Evidence: `README.md`; `index.php`; `config.php`.

### Boundary Overlays

Purpose: Serve GeoJSON overlays for barangay/city/province/region.
Main Code: `index.php`, `tools\prepare-boundaries.php`, boundary resources.
Database Tables: None.
APIs / Routes: `/boundaries/{scope}/{code}.geojson`, `/boundaries.geojson?scope=...&relay_hub_id=...`.
Offline Behavior: Uses vendored boundary resources and caches generated outputs locally.
Sync Behavior: None.
Related PBB Apps: Hotline, Relay/Hub PSGC mapping.
Evidence: `README.md`; `resources\boundaries`; docs.

### Tile Population / Data Prep

Purpose: Pre-populate local tile coverage and prepare boundary data for deployment.
Main Code: `tools\populate-tiles.php`, `tools\data-prep\prepare.php`, `tools\data-prep\verify.php`.
Database Tables: None.
APIs / Routes: CLI scripts.
Offline Behavior: Prepares for offline map use; upstream required during population unless source is local.
Sync Behavior: None.
Related PBB Apps: Kit Setup.
Evidence: `README.md`; `tools`. Owner clarification: Kit Setup Data Prep auto-populates tiles for the boundary dictated by Hub HQ hub information during node installation.

### 6. Database Schema Summary

No database usage found / Not confirmed from code

Relationship map:

```text
storage/tiles
storage/boundaries
resources/boundaries
```

### 7. API and Route Inventory

| Method | Path / Endpoint | Purpose | Auth | Handler / File | Notes |
|---|---|---|---|---|---|
| GET | `/tiles/raster/{z}/{x}/{y}.png` | Raster tile | none | `index.php` | Cached |
| GET | `/tiles/vector/{z}/{x}/{y}.pbf` | Vector tile | none | `index.php` | Cached |
| GET | `/tiles/terrain/{z}/{x}/{y}.png` | Terrain tile | none | `index.php` | Cached |
| GET | `/tiles/glyphs/{fontstack}/{range}.pbf` | Fonts/glyphs | none | `index.php` | Cached |
| GET | `/tiles/poi/{z}/{x}/{y}.pbf` | POI vector tile | none | `index.php` | Cached |
| GET | `/boundaries/{scope}/{code}.geojson` | Boundary overlay | none | `index.php` | Cached GeoJSON |
| GET | `/boundaries.geojson?...` | Boundary overlay by query | none | `index.php` | Relay hub mapping contract |
| GET | `/tiles/health` | Health | none | `index.php` | |
| POST | `/tiles/purge/...` | Purge cached tile | purge token | `index.php` | Query/header token |
| CLI | `tools\data-prep\prepare.php`, `verify.php`, `apply-settings.php` | Data prep | local CLI | tools | |

### 8. Data Flow and Operational Flow

```text
Map client (Hotline)
-> MapServer tile URL
-> local cache lookup under storage/tiles
-> HIT: return local file
-> MISS: fetch upstream provider
-> write cache
-> return tile
```

```text
Client boundary request
-> MapServer boundary endpoint
-> resources/boundaries source
-> generated GeoJSON under storage/boundaries/http
-> return with ETag/Last-Modified/CORS/cache headers
```

### 9. Offline-First Behavior

MapServer supports offline map access only for cached/pre-populated tiles and generated boundary outputs. Cache misses require upstream/internet/provider connectivity. Purge is local. Boundary generation uses local vendored boundary resources. Owner clarification: Setup Data Prep is expected to populate the required Hub HQ-defined boundary coverage during installation.

### 10. Integration with Other PBB Apps

| Integration | Direction | Protocol / Method | Purpose | Code Evidence |
|---|---|---|---|---|
| Hotline -> MapServer | HTTP tile/style sources | Map display | `hotline\README.md`; MapServer endpoints |
| Kit Setup -> MapServer | CLI/installer config | Install/default credentials/data prep | `README.md`; `installer`; `tools\data-prep` |
| Hub/Relay codes -> MapServer | Query contract | Boundary by relay hub/PSGC | `docs\boundary-overlay-contract.md`; README |

### 11. Deployment and Runtime Architecture

Plain PHP under Apache/WAMP with `.htaccess` rewrite to `index.php`. PHP cURL is required. Cache root must be writable. Upstream provider API keys are environment-driven. Installer and release metadata exist.

### 12. Security and Privacy Notes

| Risk | Severity | Evidence | Suggested Fix |
|---|---|---|---|
| API keys for tile providers are environment secrets | Medium | `config.php` requires `STADIAMAPS_API_KEY`, `MAPTILER_API_KEY` | Keep out of source and mask in diagnostics |
| Purge token controls cache deletion | Medium | `TILES_PURGE_TOKEN`, purge endpoints | Require strong token and local/admin network restrictions |
| SSL verification can be disabled | Medium | `TILES_CURL_SSL_VERIFY` | Only disable for controlled local setup; log when disabled |

### 13. Realtime Communication

No realtime functionality found / Not confirmed from code.

### 14. Mapping and Geolocation

MapServer is the mapping service. It serves raster/vector/terrain/glyph/POI tiles and boundary GeoJSON. It uses OSM/Stadia/MapTiler defaults when custom base URLs are not set. It caches files locally under `storage/tiles` and generated boundaries under `storage/boundaries`. Owner clarification: Setup Data Prep should populate required tile coverage from the Hub HQ-defined boundary during installation.

### 15. Background Jobs, Schedulers, and Maintenance Tasks

| Task | Schedule / Trigger | Purpose | Code Evidence |
|---|---|---|---|
| `tools\populate-tiles.php` | Manual/data prep | Pre-populate tile cache | `tools`; README |
| `tools\prepare-boundaries.php` | Manual/data prep | Prepare GeoJSON boundary data | `tools`; README |
| Data prep `prepare/verify/apply-settings.php` | Kit Setup/data prep | Configure and validate maps | `tools\data-prep` |

### 16. Configuration and Environment Variables

| Variable / Config | Purpose | Required | Default / Example | Related Module |
|---|---|---|---|---|
| `TILES_CACHE_ROOT` | Cache directory | No | `storage/tiles` | Cache |
| `OSM_TILE_BASE_URL` | Raster upstream | No | OSM URL | Raster |
| `VECTOR_TILE_BASE_URL`, `GLYPHS_BASE_URL` | Vector/glyph upstream | Required unless Stadia key default used | Stadia templates | Vector/glyph |
| `TERRAIN_TILE_BASE_URL`, `POI_BASE_URL` | Terrain/POI upstream | Required unless MapTiler key default used | MapTiler templates | Terrain/POI |
| `STADIAMAPS_API_KEY`, `MAPTILER_API_KEY` | Provider keys | Conditional | secret | Upstream |
| `TILES_PURGE_TOKEN` | Purge auth | Required for purge | secret | Maintenance |
| `TILES_CURL_SSL_VERIFY`, `TILES_CURL_CA_BUNDLE` | TLS behavior | Optional | verify true | Upstream fetch |

### 17. Known Technical Debt and Gaps

| Area | Issue | Evidence | Recommended Next Step |
|---|---|---|---|
| Offline completeness | Offline only works for cached/prepared tiles, so Setup Data Prep must be verified | README cache/populator behavior; owner clarification about Hub HQ boundary-driven Data Prep | Add pass/fail preflight for required tile and boundary coverage |
| Provider dependency | Default cache misses depend on internet/API keys | `config.php` upstream templates | Bundle required tiles for field kits |
| No auth on public tiles | Public tile endpoints unauthenticated | README endpoints | Keep service LAN-scoped or add access controls if exposed |

### 18. Testing Status

No PHPUnit/package test framework was found. Manual curl cache tests are documented in README. `test-results` directory exists but no test runner was confirmed.

### 19. Evidence Summary

Evidence:
- `C:\wamp64\www\mapserver\README.md`
- `C:\wamp64\www\mapserver\index.php`
- `C:\wamp64\www\mapserver\config.php`
- `C:\wamp64\www\mapserver\.htaccess`
- `C:\wamp64\www\mapserver\tools`
- `C:\wamp64\www\mapserver\resources\boundaries`

---

## App: PBB Chatviewer

### 1. Executive Technical Summary

PBB Chatviewer is a plain PHP/JS development coordination app for PBB agent chat. It can read from the DB-backed `pbb_agentchat` schema and falls back to parsing `C:\wamp64\www\pbb\chat_log.md` when the database schema is unavailable. It exposes read APIs for messages/agents/topics, token-authenticated claim/post/edit/delete behavior, and renders a searchable timeline UI with vendored Helper components. It is development coordination tooling, not an operational citizen/responder app.

### 2. Repository Overview

| Area | Details |
|---|---|
| App Name | PBB Chatviewer |
| Local Path | `C:\wamp64\www\pbb\chatviewer` |
| Main Language | PHP, JavaScript |
| Main Framework | Plain PHP |
| Frontend Framework | Plain JS modules, Helper UI bundle |
| Backend Framework | None |
| Database | MySQL `pbb_agentchat` by default, with Markdown fallback |
| Realtime Technology | None |
| Queue / Worker System | None |
| Package Manager | None found |
| Runtime Requirements | PHP web server; MySQL for DB-backed chat; readable `..\chat_log.md` fallback |
| Main Entry Points | `index.php`, `api/chat-log.php`, `api/chat-entries.php`, `api/claim.php`, `assets/app.mjs`, `src/ChatRepository.php`, `src/ChatLogParser.php` |
| Important Config Files | None found |
| Important Environment Variables | `PBB_AGENTCHAT_DB_HOST`, `PBB_AGENTCHAT_DB_NAME`, `PBB_AGENTCHAT_DB_USER`, `PBB_AGENTCHAT_DB_PASS`, `PBB_AGENTCHAT_SECRET` |
| Deployment Target | Local WAMP/Apache PHP app |

### 3. App Purpose and PBB Role

Chatviewer stores and displays development coordination messages between PBB project agents. It depends on MySQL when the DB schema exists, falls back to `chat_log.md`, and uses vendored Helper UI assets. If unavailable, PBB operational apps are not affected; only agent coordination/review is affected.

### 4. User Roles and Permissions

| Role | Purpose | Capabilities | Code Evidence |
|---|---|---|---|
| viewer | Review user | Search/filter/render chat log | `index.php`; `assets\app.mjs`; `api\chat-log.php` |
| claimed agent | Authenticated project identity | Post entries, derive sender from token, edit/delete own entries where API supports it | `api\chat-entries.php`; `ChatRepository.php`; `api\claim.php` |
| admin agent | Administrative chat identity | Edit/delete entries beyond sender ownership where repository role permits | `ChatRepository.php` |

Read APIs are unauthenticated. Write/claim/edit/delete APIs use bearer or `X-Agent-Token` project tokens.

### 5. Main Features and Modules

### Chat Log API

Purpose: Return metadata/projects/topics/messages from MySQL when available, otherwise parse `chat_log.md`.
Main Code: `api\chat-log.php`, `src\ChatRepository.php`, `src\ChatLogParser.php`.
Database Tables: `chat_entries`, `chat_agents`, `chat_entry_recipients`, `chat_entry_revisions`, `chat_topics`, `chat_audit_logs`.
APIs / Routes: `GET api/chat-log.php`.
Offline Behavior: Fully local if file exists.
Sync Behavior: None.
Related PBB Apps: Shared PBB workspace only.
Evidence: `api\chat-log.php`; `src\ChatRepository.php`; `src\ChatLogParser.php`.

### Agent Chat API

Purpose: Let PBB project agents claim identities, post direct/broadcast messages, and query messages by sender/target/search.
Main Code: `api\chat-entries.php`, `api\claim.php`, `src\ChatRepository.php`.
Database Tables: `chat_entries`, `chat_agents`, `chat_entry_recipients`, `chat_entry_revisions`, `chat_audit_logs`.
APIs / Routes: `GET/POST api/chat-entries.php`, claim API, edit/delete endpoints where present.
Offline Behavior: Works on LAN/local PHP and MySQL.
Sync Behavior: None.
Related PBB Apps: All PBB project agents.
Evidence: `api\chat-entries.php`; `api\claim.php`; `ChatRepository::messages()`, `ChatRepository::createEntry()`.

### Chat Timeline UI

Purpose: Render search, summary, timeline, direct-only filter.
Main Code: `index.php`, `assets\app.mjs`, `assets\app.css`.
Database Tables: None.
APIs / Routes: Static page plus API fetch.
Offline Behavior: Fully local.
Sync Behavior: None.
Related PBB Apps: Helper UI.
Evidence: `index.php`; file inventory.

### 6. Database Schema Summary

Chatviewer uses MySQL when the schema exists; otherwise `api/chat-log.php` falls back to parsing the Markdown file.

| Table | Purpose | Important Columns | Relationships / Notes |
|---|---|---|---|
| chat_agents | Project identities | project_name, role, token_hash, claim_hash, is_active | Sender identity and token ownership |
| chat_entries | Messages | sender_agent_id, message_timestamp, body, deleted_at | Sender FK to chat_agents |
| chat_entry_recipients | Direct/multi-target recipients | entry_id, target_agent_id | Many-to-many recipients |
| chat_entry_revisions | Edit history | entry_id, body, edited_by_agent_id | Revisions for edited entries |
| chat_topics | Active topics | body, is_active | Topic list |
| chat_audit_logs | Security/action audit | agent_id, action, success, details | Claim/post/edit/delete audit |

Relationship map:

```text
pbb_agentchat
  ├── chat_agents
  │     └── chat_entries
  │           ├── chat_entry_recipients
  │           └── chat_entry_revisions
  └── chat_topics

fallback:
chat_log.md -> ChatLogParser -> JSON API -> browser timeline UI
```

### 7. API and Route Inventory

| Method | Path / Endpoint | Purpose | Auth | Handler / File | Notes |
|---|---|---|---|---|---|
| GET | `/chatviewer/` | Viewer UI | none | `index.php` | Depending Apache path |
| GET | `/chatviewer/api/chat-log.php` | DB-backed/fallback parsed log JSON | none | `api\chat-log.php` | Emits ETag/Last-Modified |
| GET | `/chatviewer/api/chat-entries.php` | Query DB-backed chat entries | none | `api\chat-entries.php`; `ChatRepository::messages()` | Supports `sender`, `target`, `q`, `direct`, `order`; default `order=desc`, `order=asc` gives chronological |
| POST | `/chatviewer/api/chat-entries.php` | Post broadcast/direct message | bearer or `X-Agent-Token` | `api\chat-entries.php`; `ChatRepository::createEntry()` | Sender is derived from token |
| POST | `/chatviewer/api/claim.php` | Claim agent token with claim code | claim code | `api\claim.php`; `ChatRepository::claimAgent()` | Token returned once |

### 8. Data Flow and Operational Flow

```text
Browser/agent
-> `api/chat-entries.php?sender=...`
-> `ChatRepository::messages(order=desc by default)`
-> recent-first filtered messages

Browser UI
-> `api/chat-log.php`
-> `ChatRepository::payload()` if DB schema exists
-> fallback `ChatLogParser(dirname(__DIR__) . '/../chat_log.md')`
-> JSON metadata/messages
-> `assets/app.mjs` renders summary/timeline
```

### 9. Offline-First Behavior

Fully local; depends only on local PHP and `chat_log.md`. No service worker or queue.

### 10. Integration with Other PBB Apps

| Integration | Direction | Protocol / Method | Purpose | Code Evidence |
|---|---|---|---|---|
| Chatviewer -> MySQL `pbb_agentchat` | PDO | Local database | DB-backed agent chat | `Db.php`; `ChatRepository.php` |
| Chatviewer -> PBB root chat log | File read | Local filesystem | Fallback parse of shared chat log | `api\chat-log.php` |
| Chatviewer -> Helper UI | Static assets | JS/CSS | UI components | `index.php`; `vendor\pbb-helper` |

### 11. Deployment and Runtime Architecture

Plain PHP app under WAMP/Apache. No build step or package manager found. DB configuration comes from `PBB_AGENTCHAT_DB_*` env vars with defaults in `Db.php`; `api/chat-log.php` falls back to `..\chat_log.md` relative to app directory if DB access/schema is unavailable.

### 12. Security and Privacy Notes

| Risk | Severity | Evidence | Suggested Fix |
|---|---|---|---|
| Read APIs expose coordination messages without auth | Medium | `api\chat-log.php`; `api\chat-entries.php` GET path | Keep local/private or add read auth before broader exposure |
| Project chat tokens control sender identity | High | `ChatRepository::authenticate()`; `ChatRepository::createEntry()` | Store `pbb-chat-token.local.json` outside git and rotate/reset lost tokens |
| Source path exposed in fallback JSON metadata | Low | `ChatLogParser.php` returns `source_path` | Remove absolute path from API if exposed beyond local machine |

### 13. Realtime Communication

No realtime functionality found / Not confirmed from code.

### 14. Mapping and Geolocation

No mapping/geolocation functionality found / Not confirmed from code.

### 15. Background Jobs, Schedulers, and Maintenance Tasks

| Task | Schedule / Trigger | Purpose | Code Evidence |
|---|---|---|---|
| None found | N/A | N/A | File inventory |

### 16. Configuration and Environment Variables

| Variable / Config | Purpose | Required | Default / Example | Related Module |
|---|---|---|---|---|
| `PBB_AGENTCHAT_DB_HOST` | DB host | Optional | `127.0.0.1` | DB-backed chat |
| `PBB_AGENTCHAT_DB_NAME` | DB name | Optional | `pbb_agentchat` | DB-backed chat |
| `PBB_AGENTCHAT_DB_USER` | DB user | Optional | `root` | DB-backed chat |
| `PBB_AGENTCHAT_DB_PASS` | DB password | Optional | empty | DB-backed chat |
| `PBB_AGENTCHAT_SECRET` | HMAC secret for tokens/claim codes | Optional but security-sensitive | `pbb-chatviewer-local-secret` default | Token auth |
| `../chat_log.md` path | Fallback source chat log | Yes for fallback | hardcoded relative path | Parser |

### 17. Known Technical Debt and Gaps

| Area | Issue | Evidence | Recommended Next Step |
|---|---|---|---|
| Read auth | Read APIs have no access control | `api\chat-log.php`; `api\chat-entries.php` GET | Add read auth if served beyond localhost/LAN |
| Source coupling | Hardcoded relative source path | `api\chat-log.php` | Make source configurable if needed |
| Tests | No local automated test suite found | file inventory | Add tests for ordering, token auth, claim/post/edit/delete behavior |

### 18. Testing Status

No local automated test suite found / Not confirmed from code. Current behavior was confirmed from `ChatRepository.php` and `api/chat-entries.php`: list queries default to newest-first and support `order=asc`.

### 19. Evidence Summary

Evidence:
- `C:\wamp64\www\pbb\chatviewer\index.php`
- `C:\wamp64\www\pbb\chatviewer\api\chat-log.php`
- `C:\wamp64\www\pbb\chatviewer\src\ChatLogParser.php`
- `C:\wamp64\www\pbb\chatviewer\assets\app.mjs`

---

## App: PBB Support System

### 1. Executive Technical Summary

PBB Support System is a Laravel 12 support/SITREP operations app. It receives Relay-delivered SITREPs and Hotline support requests, stages the latest valid SITREP per source hub, consolidates SITREPs, displays the current consolidated SITREP, relays latest consolidated SITREPs upstream, and lets support operators process support requests through received/accepted/rejected/assigned/en_route/completed local states. Current code also confirms a Relay source-heartbeat webhook receiver at `POST /api/relay/source-heartbeats` and Support-specific Realtime configuration for publishing heartbeat snapshots. It runs as an operator/admin-facing Laravel web app on a PBB node or upper-level support node such as city/municipality, province, or HQ-side support deployment. It depends on Relay, Hub HQ `hub.json`, MapServer, local MySQL, queues, Realtime, and vendored SITREP SDKs.

### 2. Repository Overview

| Area | Details |
|---|---|
| App Name | PBB Support System |
| Local Path | `C:\wamp64\www\pbb\support` |
| Main Language | PHP, JavaScript |
| Main Framework | Laravel 12 |
| Frontend Framework | Vite, Tailwind/Vite plugin, custom JS, vendored Helper UI, MapLibre-style map code |
| Backend Framework | Laravel |
| Database | MySQL in `.env.example`; Laravel migrations for support settings, SITREP pipeline, support requests, jobs/cache/sessions |
| Realtime Technology | Support-specific Realtime client/project settings and source-heartbeat publisher confirmed |
| Queue / Worker System | Laravel database queue; jobs `SubmitSitrepRelayDelivery`, `SubmitSupportRequestUpdateDelivery`; scheduler commands |
| Package Manager | Composer, npm |
| Runtime Requirements | PHP `^8.2`, Node `>=22` per `release.json`, MySQL, queue worker, scheduler |
| Main Entry Points | `public/index.php`, `artisan`, `routes/web.php`, `routes/api.php`, `routes/console.php`, `resources/js/app.js` |
| Important Config Files | `.env.example`, `config/services.php`, `routes/*.php`, `release.json`, `vite.config.js` |
| Important Environment Variables | `APP_URL`, `DB_*`, `SESSION_*`, `QUEUE_CONNECTION`, `RELAY_HUB_JSON_URL`; support runtime settings store Relay URL/token, handler token, source-heartbeat webhook token, Realtime client/project codes, and CA bundle |
| Deployment Target | Laravel app under WAMP/Apache or PHP web server; Kit-bundle compatible per `release.json` |

### 3. App Purpose and PBB Role

Support System solves upper-level or receiving-agency support operations: ingest source SITREPs, consolidate current status, display source/coverage context, receive Hotline support requests, manage support action lifecycle, consume Relay source-heartbeat webhooks, and send lifecycle updates back through Relay. Hotline and Relay depend on it when support requests need downstream handling. It depends on Relay for inbound/outbound envelopes and heartbeat webhooks, Hub HQ/Relay `hub.json` for hub identity/uplinks, MapServer for maps and boundaries, Realtime for heartbeat snapshot publishing, and local database/queue/scheduler. If unavailable, support intake, consolidated SITREP support operations, and Support heartbeat dashboard updates stop, but Hotline local incident handling can still run.

### 4. User Roles and Permissions

| Role | Purpose | Capabilities | Code Evidence |
|---|---|---|---|
| authenticated user | Support dashboard operator/admin shell | Login, view dashboard/current SITREP/source heartbeats/support requests, update own user/password | `routes\web.php`; `AuthController.php`; `UserController.php` |
| admin-like authenticated user | Manage users/settings | Admin user CRUD and settings update routes are inside generic `auth` middleware; no route-level role guard confirmed | `routes\web.php`; `AdminUsersController.php`; `SettingsController.php`; `User.php` role column |
| Relay handler client | Machine-to-machine Relay handler | POST inbound SITREP/support request/lifecycle envelopes with bearer token from support settings | `routes\api.php`; `RelaySitrepHandlerController.php`; `RelaySupportRequestHandlerController.php` |
| Relay source-heartbeat webhook client | Machine-to-machine Relay operational webhook | POST `source.heartbeat.updated` with bearer or `X-Relay-Webhook-Key` token; Support deduplicates by `event_id` | `routes\api.php`; `RelaySourceHeartbeatController.php`; `SourceHeartbeatRealtimeTest.php` |

Formal route-level role authorization beyond authentication was not confirmed.

### 5. Main Features and Modules

### SITREP Ingestion and Staging

Purpose: Receive Relay SITREP envelopes, validate/normalize them, and stage latest valid report per source hub.
Main Code: `RelaySitrepHandlerController.php`, `DatabaseSitrepStagingStore.php`, vendored `pbb-sitrep-consolidator`.
Database Tables: `relay_inbound_sitreps`, `sitrep_stagings`.
APIs / Routes: `POST /api/relay/sitreps`.
Offline Behavior: Inbound processing is local once Relay delivers; source delivery depends on Relay reachability.
Sync Behavior: Latest valid SITREP per source hub is staged locally.
Related PBB Apps: PBB Relay, Hotline, Hub/HQ.
Evidence: `routes\api.php`; `database\migrations\2026_05_30_020000_create_sitrep_pipeline_tables.php`.

### SITREP Consolidation and Upstream Relay

Purpose: Consolidate staged SITREPs into one current SITREP and send latest-only upstream through Relay.
Main Code: `SitrepConsolidationService.php`, `SitrepRelayOutboxService.php`, `SitrepRelaySubmissionService.php`, `ConsolidateSitreps.php`, `SubmitLatestSitrepToRelay.php`.
Database Tables: `consolidated_sitreps`, `sitrep_relay_deliveries`.
APIs / Routes: CLI `support:sitreps:consolidate`, `support:sitreps:relay-latest`; `GET /api/sitreps/current`.
Offline Behavior: Consolidation works locally from staged data; upstream relay submission waits/fails if Relay URL/token or uplink target is unavailable.
Sync Behavior: Sends `sitrep.record` envelopes to Relay `/api/v1/messages` with `X-Relay-Key`.
Related PBB Apps: PBB Relay, Hub/HQ.
Evidence: `routes\console.php`; `SitrepRelaySubmissionService.php`; `release.json`.

### Support Request Intake and Lifecycle

Purpose: Receive Hotline support requests, validate required justification fields, track support actions, and relay lifecycle updates back to Hotline.
Main Code: `RelaySupportRequestHandlerController.php`, `SupportRequestsController.php`, `SupportRequestLifecycleRelayService.php`.
Database Tables: `support_requests`, `support_request_messages`, `support_request_actions`, `support_request_update_deliveries`.
APIs / Routes: `POST /api/relay/support-requests`, `POST /api/relay/support-request-lifecycle`, authenticated `/api/support-requests/*` action routes.
Offline Behavior: Intake/action state persists locally; outbound lifecycle update delivery depends on Relay connectivity.
Sync Behavior: Local `completed` maps to outbound `support.request.fulfilled`; outbound lifecycle updates use Relay `/api/v1/messages`.
Related PBB Apps: Hotline, Relay.
Evidence: `routes\web.php`; `routes\api.php`; `SupportRequestLifecycleRelayService.php`.

### Source Heartbeat Webhook and Realtime Publish

Purpose: Receive Relay-owned `source.heartbeat.updated` operational webhook events and publish normalized source heartbeat snapshots to the Support Realtime room.
Main Code: `RelaySourceHeartbeatController.php`, `SourceHeartbeatRealtimePublisher.php`, `SupportSettings.php`.
Database Tables: Cache is used for event-id idempotency; settings are stored in `support_settings`.
APIs / Routes: `POST /api/relay/source-heartbeats`; authenticated `GET /api/source-heartbeats` remains an initial load/fallback path.
Offline Behavior: Webhook receipt is local if Relay can reach Support; Realtime publish may fail without rejecting accepted webhook receipt.
Sync Behavior: This is operational telemetry, not a normal `hub_relay_messages` app-to-app message.
Related PBB Apps: Relay, Realtime, Kit Setup.
Evidence: `C:\wamp64\www\pbb\support\routes\api.php`; `C:\wamp64\www\pbb\support\app\Http\Controllers\Api\RelaySourceHeartbeatController.php`; `C:\wamp64\www\pbb\support\tests\Feature\SourceHeartbeatRealtimeTest.php`; `C:\wamp64\www\pbb\kit-setup\docs\relay-hotline-support-data-prep-contract.md`.

### Support Map and Dashboard

Purpose: Display current support/SITREP context with map and source boundaries.
Main Code: `SupportMapConfigController.php`, `SupportMapBoundaryController.php`, `resources/js/maps/supportDashboardMap.js`, `MapServerUrls.php`.
Database Tables: Uses current SITREP/support tables for data; no dedicated map table.
APIs / Routes: `GET /support-map.json`, `GET /map-boundaries/{scope}/{code}.geojson`.
Offline Behavior: LAN/local operation depends on local MapServer cached/prepared tiles and boundaries.
Sync Behavior: None.
Related PBB Apps: PBB MapServer, Hub/HQ.
Evidence: `routes\web.php`; `app\Support\Maps\MapServerUrls.php`.

### Settings and Admin Shell

Purpose: Store encrypted support settings such as Relay URL/token, handler token, target systems, realtime settings, and alert policy.
Main Code: `SupportSettings.php`, `SettingsController.php`, `AdminUsersController.php`.
Database Tables: `support_settings`, `users`.
APIs / Routes: Authenticated `GET/POST /api/settings`, `/api/admin/users`.
Offline Behavior: Local settings storage works offline.
Sync Behavior: Settings control Relay submissions but do not sync themselves.
Related PBB Apps: Relay, Realtime, MapServer.
Evidence: `SupportSettings.php`; `routes\web.php`.

### 6. Database Schema Summary

| Table | Purpose | Important Columns | Relationships / Notes |
|---|---|---|---|
| users | Support app users | name, email, role, password | Role column exists; route-level role guard not confirmed |
| support_settings | Encrypted runtime settings | key, encrypted value | Stores Relay URL/token/handler token and app settings |
| relay_inbound_sitreps | Raw inbound Relay SITREPs | relay_message_id, source_hub_id, message_type, validation_status, raw_envelope, sitrep_payload | Staged if valid |
| sitrep_stagings | Latest valid SITREP per source hub | source_deployment, source_hub_id, alert_level, payload_hash, normalized_sitrep | `source_hub_id` unique |
| consolidated_sitreps | Current/superseded consolidated SITREPs | status, alert_level, source_sitrep_count, sitrep_payload, source_index | Current report displayed and relayed |
| sitrep_relay_deliveries | Outbound consolidated SITREP delivery state | consolidated_sitrep_id, status, relay_message_id, attempt_count, last_error | Queue/retry tracking |
| support_requests | Support request master record | support_request_id, local_request_id, correlation_id, source fields, status, urgency, justification codes, contexts | Received from Hotline |
| support_request_messages | Inbound support request/lifecycle messages | relay_message_id, message_type, direction, validation_status, payload | Intake/audit records |
| support_request_actions | Operator actions | action, from_status, to_status, actor, notes, metadata | Local lifecycle audit |
| support_request_update_deliveries | Outbound support lifecycle delivery state | update_id, message_type, status, delivery_status, envelope, attempts | Sends updates back through Relay |

Relationship map:

```text
relay_inbound_sitreps
  -> sitrep_stagings
sitrep_stagings
  -> consolidated_sitreps
        -> sitrep_relay_deliveries
support_requests
  -> support_request_messages
  -> support_request_actions
  -> support_request_update_deliveries
users
  -> support_request_actions
```

### 7. API and Route Inventory

| Method | Path / Endpoint | Purpose | Auth | Handler / File | Notes |
|---|---|---|---|---|---|
| GET | `/`, `/dashboard` | Support dashboard shell | web/session surface | `routes\web.php` | View `app` |
| GET | `/support-map.json` | Map config | none confirmed | `SupportMapConfigController` | Uses MapServer URL |
| GET | `/map-boundaries/{scope}/{code}.geojson` | Boundary proxy/config URL | none confirmed | `SupportMapBoundaryController` | scope: barangay/city/province/region |
| GET | `/api/bootstrap`, `/api/csrf-token` | App bootstrap/session support | none | `BootstrapController`, `AuthController` | |
| POST | `/api/login` | Login | throttle | `AuthController` | |
| GET/POST | `/api/user`, `/api/user/password` | User profile/password | auth | `UserController` | |
| GET/POST/DELETE | `/api/admin/users*` | User admin | auth | `AdminUsersController` | No role guard confirmed |
| GET/POST | `/api/settings` | Support settings | auth | `SettingsController` | Stores Relay settings |
| GET/POST | `/api/support-requests*` | Support request list/detail/actions | auth | `SupportRequestsController` | receive/accept/reject/assign/en-route/complete |
| GET | `/api/sitreps/current` | Current consolidated SITREP | auth | `CurrentSitrepController` | |
| GET | `/api/source-heartbeats` | Relay source heartbeat lookup | auth | `SourceHeartbeatController` | Calls Relay source-heartbeats endpoint |
| POST | `/api/relay/source-heartbeats` | Relay source heartbeat webhook receiver | bearer or `X-Relay-Webhook-Key` token | `RelaySourceHeartbeatController` | Accepts `event_type=source.heartbeat.updated`, deduplicates by `event_id`, publishes to Realtime |
| POST | `/api/relay/sitreps` | Relay handler for SITREPs | bearer token from settings | `RelaySitrepHandlerController` | Throttled |
| POST | `/api/relay/support-requests` | Relay handler for support.request | bearer token from settings | `RelaySupportRequestHandlerController` | Throttled |
| POST | `/api/relay/support-request-lifecycle` | Relay handler for request lifecycle/cancel | bearer token from settings | `RelaySupportRequestLifecycleHandlerController` | Throttled |
| Console | `support:sitreps:consolidate` | Consolidate staged SITREPs | CLI | `ConsolidateSitreps` | Every 15 minutes |
| Console | `support:sitreps:relay-latest` | Submit latest consolidated SITREP | CLI | `SubmitLatestSitrepToRelay` | Every 5 minutes |

### 8. Data Flow and Operational Flow

```text
Relay handler delivery
-> Support `/api/relay/sitreps`
-> relay_inbound_sitreps
-> sitrep_stagings latest per source hub
-> `support:sitreps:consolidate`
-> consolidated_sitreps current
-> sitrep_relay_deliveries
-> `support:sitreps:relay-latest`
-> Relay `/api/v1/messages`
-> upstream hub
```

```text
Hotline support.request
-> Relay
-> Support `/api/relay/support-requests`
-> support_requests + support_request_messages
-> support operator action
-> support_request_actions
-> support_request_update_deliveries
-> Relay `/api/v1/messages`
-> Hotline lifecycle ingress
```

### 9. Offline-First Behavior

Support System can run on LAN/local WAMP with local MySQL, queue, scheduler, and cached app assets. SITREP consolidation and support request handling work from local database state after Relay has delivered messages. Outbound SITREP and support lifecycle sync requires local Relay URL/token and Relay availability; upstream delivery then depends on Relay topology/connectivity. Map behavior depends on MapServer and cached/prepared map data. No service worker/PWA durable browser outbox was confirmed.

### 10. Integration with Other PBB Apps

| Integration | Direction | Protocol / Method | Purpose | Code Evidence |
|---|---|---|---|---|
| Relay -> Support | inbound HTTP handler | Bearer token | SITREP/support request delivery | `routes\api.php`; relay handler controllers |
| Support -> Relay | outbound HTTP `/api/v1/messages` | `X-Relay-Key` | Consolidated SITREP and support lifecycle updates | `SitrepRelaySubmissionService.php`; `SupportRequestLifecycleRelayService.php` |
| Support -> Hub/Relay hub JSON | outbound HTTP GET | `RELAY_HUB_JSON_URL` | Hub identity/uplinks for consolidation and targets | `SitrepConsolidationService.php`; `SitrepRelaySubmissionService.php`; `.env.example` |
| Support -> MapServer | HTTP tile/boundary URLs | Map display | Support dashboard maps | `MapServerUrls.php`; `SupportMapConfigController.php` |
| Hotline -> Support through Relay | Relay envelope | support.request intake | Support operations from Hotline requests | `RelaySupportRequestHandlerController.php` |

### 11. Deployment and Runtime Architecture

Support is a Laravel app with `public/index.php`, Composer/npm, MySQL, Vite build, database queue, and scheduler commands. `release.json` declares runtime `php ^8.2`, `node >=22`, additive database migrations, service restart required, and no rollback support. `.env.example` uses `APP_URL=https://support.pbb.ph`, `DB_DATABASE=pbb_support`, `SESSION_DOMAIN=support.pbb.ph`, `QUEUE_CONNECTION=database`, and `RELAY_HUB_JSON_URL=https://relay.pbb.ph/hub.json`.

### 12. Security and Privacy Notes

| Risk | Severity | Evidence | Suggested Fix |
|---|---|---|---|
| Default admin credentials documented | High | `README.md` lists `admin@support.pbb.ph` / `password` | Force password change during install and block default password in shared deployment |
| Relay handler bearer token stored in app settings | High | `RelaySitrepHandlerController::isAuthorized`; `SupportSettings` | Generate strong token in Kit Setup, rotate, and avoid logging |
| Relay client token stored in encrypted settings | High | `SupportSettings`; outbound services use `X-Relay-Key` | Keep encrypted, mask in UI/logs, rotate per node |
| Admin routes are auth-only in route file | Medium | `routes\web.php` wraps admin users/settings only in `auth` | Add/confirm role middleware for user/settings administration |
| Support requests contain incident/SITREP evidence and requester data | High | support request migrations and controllers | Define retention, audit, access policy, and export/purge behavior |

### 13. Realtime Communication

Support has confirmed backend-to-Realtime publishing for source heartbeat snapshots. Relay posts `source.heartbeat.updated` to `POST /api/relay/source-heartbeats`; Support normalizes the source/heartbeat/rollup payload, deduplicates by `event_id`, and calls `SourceHeartbeatRealtimePublisher` to publish an availability/sources snapshot. Kit and Realtime now use Support-specific defaults such as `clt_PBB_SUPPORT`, `prj_SUPPORT_SERVER`, and `prj_SUPPORT_ADMIN`, plus a Support-specific backend ingress secret. Browser WebSocket usage inside Support was not confirmed from the reviewed files.

### 14. Mapping and Geolocation

Support uses dashboard map code and MapServer URLs. `GET /support-map.json` returns tile endpoints for vector, terrain, glyphs, and POI from MapServer. Boundary URLs are generated as `/boundaries/{scope}/{code}.geojson` through MapServer. Current SITREP data can include source boundaries and map context.

### 15. Background Jobs, Schedulers, and Maintenance Tasks

| Task | Schedule / Trigger | Purpose | Code Evidence |
|---|---|---|---|
| `support:sitreps:consolidate` | Every 15 minutes | Consolidate staged SITREPs | `routes\console.php`; `ConsolidateSitreps.php` |
| `support:sitreps:relay-latest` | Every 5 minutes | Submit latest consolidated SITREP to Relay | `routes\console.php`; `SubmitLatestSitrepToRelay.php` |
| `SubmitSitrepRelayDelivery` | Queue job | Submit specific SITREP delivery | `app\Jobs\SubmitSitrepRelayDelivery.php` |
| `SubmitSupportRequestUpdateDelivery` | Queue job | Submit support lifecycle update to Relay | `app\Jobs\SubmitSupportRequestUpdateDelivery.php` |

### 16. Configuration and Environment Variables

| Variable / Config | Purpose | Required | Default / Example | Related Module |
|---|---|---|---|---|
| `APP_URL` | Support app URL | Yes | `https://support.pbb.ph` | Web/session |
| `DB_*` | Database | Yes | `pbb_support` | Laravel DB |
| `SESSION_DOMAIN`, `SESSION_COOKIE`, `SESSION_LIFETIME` | Session scope/lifetime | Yes | `support.pbb.ph`, 43200 | Auth |
| `QUEUE_CONNECTION` | Queue backend | Yes | `database` | Jobs |
| `RELAY_HUB_JSON_URL` | Hub identity/uplink source | Optional but needed for target derivation | `https://relay.pbb.ph/hub.json` | SITREP consolidation/relay |
| `support_settings.relayUrl` | Relay base URL | Required for outbound relay | `https://relay.pbb.ph` | Settings DB |
| `support_settings.relayToken` | Relay client token | Required for outbound relay | empty default | Settings DB |
| `support_settings.relayHandlerToken` | Inbound Relay handler bearer token | Required for handlers | empty default | Settings DB |
| `support_settings.sourceHeartbeatWebhookToken` | Inbound Relay source-heartbeat webhook token | Required for `/api/relay/source-heartbeats` | generated by Kit as `support_source_heartbeat_webhook_token` | Source heartbeat webhook |
| Support Realtime settings | Realtime client/project/ingress config | Required for heartbeat publish | `clt_PBB_SUPPORT`, `prj_SUPPORT_SERVER`, `prj_SUPPORT_ADMIN` | Source heartbeat Realtime publisher |
| `support_settings.relayCaBundle` / `relay_ca_bundle` | CA bundle for Support outbound Relay HTTPS | Required on local TLS installs | Kit-provided local fullchain path | Relay HTTP options |
| `services.map_server.url` | MapServer base URL | Optional | `https://mapserver.pbb.ph` | Maps |

### 17. Known Technical Debt and Gaps

| Area | Issue | Evidence | Recommended Next Step |
|---|---|---|---|
| Authorization | Admin routes appear auth-only | `routes\web.php` | Add role middleware or document single-role operator model |
| Default credentials | README documents default login | `README.md` | Enforce password change during provisioning |
| Realtime frontend behavior | Backend Realtime publish is confirmed, but browser socket behavior was not reviewed | `SourceHeartbeatRealtimePublisher.php`; `SourceHeartbeatRealtimeTest.php` | Document Support UI subscription behavior when finalized |
| Endpoint metadata | Support may need remote Hotline media/app URLs; resolver/app endpoint contract remains separate | Support/Relay media flow context | Use Landing/Hub registry or Relay metadata for app URL discovery |
| Tests for full production topology | App has tests, but complete multi-node SITREP/support topology not verified here | Local review only | Add integration tests with Relay + Hotline fixtures |

### 18. Testing Status

PHPUnit is configured through `phpunit.xml`; Composer script `composer test` clears config then runs `php artisan test`. Local tests exist under `tests`. npm has Vite build/dev scripts. No tests were run during this documentation update.

### 19. Evidence Summary

Evidence:
- `C:\wamp64\www\pbb\support\README.md`
- `C:\wamp64\www\pbb\support\composer.json`
- `C:\wamp64\www\pbb\support\release.json`
- `C:\wamp64\www\pbb\support\routes\web.php`
- `C:\wamp64\www\pbb\support\routes\api.php`
- `C:\wamp64\www\pbb\support\routes\console.php`
- `C:\wamp64\www\pbb\support\database\migrations`
- `C:\wamp64\www\pbb\support\app\Support`
- `C:\wamp64\www\pbb\support\app\Http\Controllers\Api`
- `C:\wamp64\www\pbb\support\app\Http\Controllers\Api\RelaySourceHeartbeatController.php`
- `C:\wamp64\www\pbb\support\tests\Feature\SourceHeartbeatRealtimeTest.php`

---

## App: PBB Landing

### 1. Executive Technical Summary

PBB Landing is a lightweight PHP/JavaScript local LAN launcher and public hub-safe metadata/gateway surface. It serves the local `https://pbb.ph` launcher, exposes a sanitized public `/.well-known/pbb-hub.json` on the hub public domain from Relay `hub.json`, stores a Kit-managed app registry, and can gateway machine-to-machine Relay API paths under `/relay/api/v1/*` to the registered local Relay target. It is infrastructure-level and setup/runtime-facing rather than an emergency operations app.

### 2. Repository Overview

| Area | Details |
|---|---|
| App Name | PBB Landing |
| Local Path | `C:\wamp64\www\pbb\landing` |
| Main Language | PHP, JavaScript, CSS |
| Main Framework | Lightweight custom PHP classes |
| Frontend Framework | Plain JS/CSS with vendored Helper UI bundle |
| Backend Framework | Custom PHP front controller |
| Database | No database; JSON registry file in `storage\registry.json` |
| Realtime Technology | None found |
| Queue / Worker System | None found |
| Package Manager | None found |
| Runtime Requirements | PHP `>=8.2`, `json`, `openssl`, `curl`; WAMP/Apache or PHP server |
| Main Entry Points | `public/index.php`, `src\App.php`, `src\Gateway.php`, `src\RegistryStore.php` |
| Important Config Files | `config\landing.php`, `release.json`, `storage\registry.json` |
| Important Environment Variables | `PBB_LANDING_REGISTRY_TOKEN_HASH`, `PBB_LANDING_DEV_HOSTS`, `PBB_LANDING_CA_BUNDLE` |
| Deployment Target | Kit-managed local `pbb.ph` and public hub-domain vhosts/TLS |

### 3. App Purpose and PBB Role

Landing solves node launch/discovery and public-safe hub metadata exposure. It reads Relay public `hub.json`, projects a sanitized hub/service/endpoints document, displays a local launcher using the app registry, and provides an authenticated internal registry API for Kit Setup to register installed apps. If unavailable, the local landing launcher, public `.well-known` metadata surface, and Landing Relay gateway route stop; individual apps may still be reachable by their direct local domains.

### 4. User Roles and Permissions

| Role | Purpose | Capabilities | Code Evidence |
|---|---|---|---|
| local user | LAN launcher user | View launcher on `pbb.ph` | `App.php`; `Renderer.php` |
| Kit Setup / registry client | Machine client | GET/PUT/DELETE registry records under `/internal/registry/apps` using bearer token | `Auth.php`; `RegistryStore.php`; `App.php` |
| public peer/domain caller | Machine-to-machine gateway caller | Access public `.well-known` and allowed Relay gateway when peer domain is allowed | `Gateway.php`; `HubProjection.php` |

### 5. Main Features and Modules

### Local Launcher

Purpose: Render the local PBB launcher from hub projection and registered app records.
Main Code: `App.php`, `Renderer.php`, `public\assets\app.js`, `public\assets\app.css`.
Database Tables: No database.
APIs / Routes: `GET /` on local host `pbb.ph` or dev hosts.
Offline Behavior: Works locally if registry and Relay `hub.json` file are available; degrades if hub source missing.
Sync Behavior: None.
Related PBB Apps: Kit Setup, Relay, installed PBB apps.
Evidence: `README.md`; `App.php`.

### Public Hub Metadata

Purpose: Serve sanitized hub metadata and app endpoint projection.
Main Code: `HubSource.php`, `HubProjection.php`, `App.php`.
Database Tables: None.
APIs / Routes: `GET /.well-known/pbb-hub.json` on public hub domain from `hub.json`.
Offline Behavior: Reads local Relay `public/hub.json`; public access requires domain/vhost reachability.
Sync Behavior: None; projection reflects local files/registry.
Related PBB Apps: Relay, Hub/HQ, Kit Setup.
Evidence: `config\landing.php`; `HubProjection.php`; `tests\run.php`.

### Internal App Registry

Purpose: Store app launch/health/public gateway metadata registered by Kit Setup.
Main Code: `RegistryStore.php`, `Auth.php`, `App.php`.
Database Tables: JSON file `storage\registry.json`.
APIs / Routes: `GET /internal/registry/apps`, `PUT /internal/registry/apps/{appId}`, `DELETE /internal/registry/apps/{appId}` on local host only.
Offline Behavior: Fully local file store.
Sync Behavior: None.
Related PBB Apps: Kit Setup, all registered apps.
Evidence: `RegistryStore.php`; `README.md`.

### Relay Public Gateway

Purpose: Forward public machine-to-machine Relay API traffic from hub domain `/relay/api/v1/*` to registered local Relay target.
Main Code: `Gateway.php`, `RegistryStore.php`, `HubProjection.php`.
Database Tables: Registry JSON.
APIs / Routes: `/relay/api/v1/*` on public hub domain.
Offline Behavior: Requires registered Relay target and network reachability to local Relay.
Sync Behavior: Forwards HTTP only; Relay handles store-and-forward.
Related PBB Apps: Relay, Hub/HQ, FRP/public exposure path.
Evidence: `Gateway.php`; `tests\run.php`.

### 6. Database Schema Summary

No database usage found / Not confirmed from code.

File storage map:

```text
storage/registry.json
  -> apps[pbb-relay]
       -> local_url / launch_url / health_url
       -> public_gateway.target_base_url
storage/logs/landing-audit.log
storage/logs/landing-gateway.log
relay/public/hub.json
  -> Landing public projection
```

### 7. API and Route Inventory

| Method | Path / Endpoint | Purpose | Auth | Handler / File | Notes |
|---|---|---|---|---|---|
| GET | `/` | Local launcher | host must be local/development or public host for public hub page | `App.php`; `Renderer.php` | Different rendering for local/public host |
| GET | `/.well-known/pbb-hub.json` | Sanitized public hub metadata | public hub host | `App.php`; `HubProjection.php` | Excludes denied Relay fields in tests |
| GET | `/assets/app.css`, `/assets/app.js`, `/assets/helper-ui-bundle.js` | Static assets | none | `App.php` asset handler | |
| GET | `/internal/registry/apps` | Read private registry | local host + bearer token | `App.php`; `Auth.php`; `RegistryStore.php` | Token hash env var |
| PUT | `/internal/registry/apps/{appId}` | Register/update app | local host + bearer token | `RegistryStore.php` | Requires HTTPS URLs in app record |
| DELETE | `/internal/registry/apps/{appId}` | Delete app registration | local host + bearer token | `RegistryStore.php` | |
| Any | `/relay/api/v1/*` | Public M2M Relay gateway | public host + allowed peer domain | `Gateway.php` | Forwards to registered Relay `target_base_url` |

### 8. Data Flow and Operational Flow

```text
Kit Setup
-> Landing `/internal/registry/apps/{appId}` with bearer token
-> storage/registry.json
-> local `https://pbb.ph`
-> launcher lists installed apps
```

```text
Relay `public/hub.json`
-> Landing HubSource
-> HubProjection
-> public `/.well-known/pbb-hub.json`
-> peers/HQ discover safe hub metadata and registered public gateway endpoints
```

```text
Peer/HQ request
-> public hub domain `/relay/api/v1/*`
-> Landing Gateway peer-domain allowlist
-> registered local Relay target_base_url
-> Relay API
```

### 9. Offline-First Behavior

Landing can run locally without internet if PHP, local vhost/DNS, registry JSON, and Relay `hub.json` file are present. Public metadata/gateway behavior requires public hub-domain reachability. Registry operations are local-only and token-protected. It does not implement queueing or retry; Relay handles store-and-forward behind the gateway.

### 10. Integration with Other PBB Apps

| Integration | Direction | Protocol / Method | Purpose | Code Evidence |
|---|---|---|---|---|
| Landing -> Relay hub JSON | file read | local JSON file | Hub identity/public projection source | `config\landing.php`; `HubSource.php` |
| Kit Setup -> Landing | HTTP internal registry | Bearer token | Register app launch/health/gateway metadata | `README.md`; `Auth.php`; `RegistryStore.php` |
| Landing -> Relay | HTTP gateway/cURL | Forward M2M Relay API | Public Relay endpoint via hub domain | `Gateway.php` |
| Landing -> Helper UI | vendored assets | JS/CSS | Launcher UI | `public\vendor\helpers.pbb.ph` |

### 11. Deployment and Runtime Architecture

Landing uses a PHP front controller in `public/index.php`; README shows local dev with WAMP PHP built-in server. Production local host is `https://pbb.ph`; Kit Setup owns vhosts, TLS, registry token configuration, and app registry records. `release.json` declares PHP lightweight type, PHP `>=8.2`, required extensions `json`, `openssl`, `curl`, and health status path `/.well-known/pbb-hub.json`.

### 12. Security and Privacy Notes

| Risk | Severity | Evidence | Suggested Fix |
|---|---|---|---|
| Internal registry token hash must be configured | High | `PBB_LANDING_REGISTRY_TOKEN_HASH`; `Auth.php` rejects empty hash | Generate per install, store hash only, rotate if exposed |
| Registry contains local install paths and URLs | Medium | `RegistryStore.php`; `storage\registry.json` | Keep internal registry local-host-only and token protected |
| Gateway forwards Relay API traffic | High | `Gateway.php` | Keep M2M-only, enforce peer allowlist, audit gateway logs |
| Public projection must avoid sensitive Relay fields | Medium | `HubProjection.php`; tests assert `snapshot_hash`/`base_url` excluded | Maintain allowlist projection tests |
| Dev hosts can bypass normal host model | Medium | `PBB_LANDING_DEV_HOSTS` | Never set in Kit-managed installs |

### 13. Realtime Communication

No realtime functionality found / Not confirmed from code.

### 14. Mapping and Geolocation

Landing does not render maps. Public projection includes hub administrative fields from Relay `hub.json`: country, region, province, city/municipality, barangay codes, deployment, domain, and status.

### 15. Background Jobs, Schedulers, and Maintenance Tasks

| Task | Schedule / Trigger | Purpose | Code Evidence |
|---|---|---|---|
| Registry audit append | Registry PUT/DELETE | Record registry changes | `RegistryStore.php` |
| Gateway audit append | Gateway forward | Record gateway method/path/status/peer/body bytes | `Gateway.php` |

No cron/queue scheduler found.

### 16. Configuration and Environment Variables

| Variable / Config | Purpose | Required | Default / Example | Related Module |
|---|---|---|---|---|
| `hosts.local` | Local launcher host | Yes | `pbb.ph` | `config\landing.php` |
| `hosts.hq` | HQ peer host | Yes | `hub.pbb.ph` | Gateway peer allowlist |
| `paths.registry` | Registry JSON path | Yes | `storage\registry.json` | Registry |
| `paths.relay_hub_json` | Relay hub JSON source | Yes | `C:/wamp64/www/pbb/relay/public/hub.json` | Hub projection |
| `PBB_LANDING_REGISTRY_TOKEN_HASH` | SHA-256 hash of Kit-generated registry token | Yes for internal registry | empty default | Auth |
| `PBB_LANDING_DEV_HOSTS` | Local dev host allowlist | No; dev only | empty | Host checks |
| `PBB_LANDING_CA_BUNDLE` | Gateway cURL CA bundle | Optional | empty | Gateway |

### 17. Known Technical Debt and Gaps

| Area | Issue | Evidence | Recommended Next Step |
|---|---|---|---|
| Gateway scope | Only `/relay/api/v1/*` gateway behavior confirmed | `Gateway.php` | Document whether other app public gateways will be added |
| Registry schema | JSON registry validation is code-only | `RegistryStore.php` | Publish app registry contract for Kit Setup and apps |
| Public exposure | Depends on Kit vhost/TLS/FRP setup | `README.md`; `release.json` | Tie Landing deployment docs to Kit Setup/FRP runbook |
| No package manager/build metadata | No composer/package file found | file inventory | Document static/PHP-lightweight release process |

### 18. Testing Status

Landing has `tests\run.php`, a custom PHP test script covering host normalization, public projection, registry auth, registry PUT, gateway host restrictions, peer allowlist, and sensitive field exclusion. It was not run during this documentation update.

### 19. Evidence Summary

Evidence:
- `C:\wamp64\www\pbb\landing\README.md`
- `C:\wamp64\www\pbb\landing\release.json`
- `C:\wamp64\www\pbb\landing\config\landing.php`
- `C:\wamp64\www\pbb\landing\public\index.php`
- `C:\wamp64\www\pbb\landing\src\App.php`
- `C:\wamp64\www\pbb\landing\src\Gateway.php`
- `C:\wamp64\www\pbb\landing\src\RegistryStore.php`
- `C:\wamp64\www\pbb\landing\src\HubProjection.php`
- `C:\wamp64\www\pbb\landing\tests\run.php`

---

## App: PBB Chat

### 1. Executive Technical Summary

PBB Chat is a Laravel 12 local barangay chat app. It is citizen-facing and admin/moderator-facing. Code and `release.json` identify it as a local LAN app with public gateway disabled, running at `https://chat.pbb.ph`, backed by a local `pbb_chat` MySQL database, Helper UI assets, and PBB Realtime admission/event publishing. It supports rooms, direct conversations, message requests, local profiles/sessions, reports/blocks/moderation, badges, room access controls, and a Hotline escalation handoff response that returns the configured Hotline URL and local chat user context.

### 2. Repository Overview

| Area | Details |
|---|---|
| App Name | PBB Chat |
| Local Path | `C:\wamp64\www\pbb\chat` |
| Main Language | PHP, JavaScript |
| Main Framework | Laravel 12 |
| Frontend Framework | Vite, Tailwind, Helper UI components |
| Backend Framework | Laravel |
| Database | MySQL database `pbb_chat` in `.env.example`; Laravel migrations define chat/session/cache/queue tables |
| Realtime Technology | PBB Realtime SDK/admission and backend ingest publishing |
| Queue / Worker System | Laravel queue config exists; no Chat-specific background command was confirmed |
| Package Manager | Composer, npm |
| Runtime Requirements | PHP `^8.2`, Composer, Node/npm for builds, MySQL, WAMP/Apache or PHP web server |
| Main Entry Points | `public/index.php`, `artisan`, `routes/web.php`, `resources/js` |
| Important Config Files | `.env.example`, `config/chat.php`, `config/session.php`, `release.json`, `public/openapi/pbb-chat.yaml` |
| Important Environment Variables | `DB_*`, `SESSION_*`, `PBB_CHAT_HOME_HUB_ID`, `PBB_CHAT_HOME_NAME`, `PBB_CHAT_HOTLINE_URL`, `PBB_CHAT_REALTIME_URL`, `PBB_CHAT_REALTIME_SDK_URL`, `PBB_CHAT_REALTIME_TOKEN_SIGNING_SECRET`, `PBB_CHAT_REALTIME_BACKEND_INGRESS_SECRET`, `PBB_CHAT_REALTIME_TOKEN_TTL_SECONDS` |
| Deployment Target | Local barangay LAN browser app; `release.json` sets Landing launcher visible and `public_gateway.enabled=false` |

### 3. App Purpose and PBB Role

PBB Chat solves local barangay community messaging on the PBB local network. It is not the emergency operations system; escalation to Hotline is represented by a handoff endpoint rather than confirmed direct incident creation. It depends on local MySQL, Helper UI components, and PBB Realtime for live chat rooms/direct-message channels. If unavailable, local community chat, direct messages, message requests, reports/moderation, and badge UX are unavailable, but core Hotline/Relay/SITREP operations are not shown as depending on it.

### 4. User Roles and Permissions

| Role | Purpose | Capabilities | Code Evidence |
|---|---|---|---|
| local chat user | Citizen/local participant | View rooms, join/leave rooms, post/read room messages, direct conversations, block/report users/messages, message requests | `routes\web.php`; `app\Http\Controllers\Api\Concerns\ResolvesChatUser.php` |
| blocked local user | Suspended/blocked participant | Rejected by `requireChatUser` when `blocked_at` is set | `app\Http\Controllers\Api\Concerns\ResolvesChatUser.php`; `database\migrations\2026_06_29_000100_add_room_member_and_user_moderation_fields.php` |
| admin / moderator | Chat administration and moderation | User CRUD/session ping, suspend/block-login/unblock-login, badges, reports review/hide/resolve, room/member controls | `routes\web.php`; `app\Http\Controllers\Api\ChatSafetyController.php`; migrations with moderation fields |

### 5. Main Features and Modules

### Room Chat

Purpose: Local room listing, membership, messages, reads, room access controls, and room realtime admission.
Main Code: `routes\web.php`; room/chat controllers under `app\Http\Controllers\Api`; `app\Services\ChatRealtimeEventPublisher.php`.
Database Tables: `chat_rooms`, `chat_room_memberships`, `chat_messages`, `chat_reports`.
APIs / Routes: `GET /api/chat/rooms`, `POST /api/chat/rooms/{room}/join`, `POST /api/chat/rooms/{room}/leave`, `GET /api/chat/rooms/{room}/messages`, `POST /api/chat/rooms/{room}/messages`, `POST /api/chat/rooms/{room}/realtime/admission`.
Offline Behavior: LAN/local operation is confirmed by release metadata and README. Browser-side offline queue was not confirmed.
Sync Behavior: Realtime room admission/publish is confirmed. Relay sync was not confirmed.
Related PBB Apps: PBB Realtime, PBB Helper.
Evidence: `routes\web.php`; `release.json`; `app\Http\Controllers\Api\ChatRealtimeController.php`.

### Direct Messaging and Message Requests

Purpose: One-to-one conversations, direct-message read state, mute/unmute, and request/accept/decline/cancel flow.
Main Code: `routes\web.php`; direct conversation/message request controllers.
Database Tables: `chat_direct_conversations`, direct message tables, `chat_message_requests`, read-state migrations.
APIs / Routes: `/api/chat/message-requests`, `/api/chat/direct-conversations`, `/api/chat/direct-conversations/{conversation}/messages`, direct mute/unmute and realtime admission endpoints.
Offline Behavior: Local/LAN operation confirmed; durable offline compose queue was not confirmed.
Sync Behavior: Realtime direct admission confirmed; Relay sync not confirmed.
Related PBB Apps: PBB Realtime, PBB Helper.
Evidence: `routes\web.php`; `database\migrations\2026_06_28_000140_add_direct_conversation_read_state.php`; recent Helper envelope/block/mute icon contracts.

### Safety, Reports, Blocks, and Moderation

Purpose: User/message reports, block/unblock, moderation review/hide/resolve, room member mute/kick/report controls.
Main Code: `app\Http\Controllers\Api\ChatSafetyController.php`; admin/report routes in `routes\web.php`.
Database Tables: `chat_reports`, `chat_blocks`, moderation columns on memberships/users/messages.
APIs / Routes: `POST /api/chat/messages/{message}/report`, `POST /api/chat/users/{user}/block`, `POST /api/chat/users/{user}/unblock`, `POST /api/chat/users/{user}/report`, admin report review/hide/resolve endpoints.
Offline Behavior: Local DB-backed moderation works on LAN; upstream reporting not confirmed.
Sync Behavior: `ChatSafetyController` publishes `chat.user_block.updated` through Realtime when configured.
Related PBB Apps: PBB Realtime.
Evidence: `app\Http\Controllers\Api\ChatSafetyController.php`; `database\migrations\2026_06_29_000100_add_room_member_and_user_moderation_fields.php`.

### Hotline Escalation Handoff

Purpose: Return a handoff payload pointing the local user to Hotline with optional summary/context flag.
Main Code: `app\Http\Controllers\Api\HotlineEscalationController.php`.
Database Tables: No dedicated escalation table confirmed.
APIs / Routes: `POST /api/chat/escalate-to-hotline`.
Offline Behavior: Returns configured local Hotline URL; direct Hotline API incident creation was not confirmed.
Sync Behavior: No Relay sync confirmed.
Related PBB Apps: PBB Hotline.
Evidence: `app\Http\Controllers\Api\HotlineEscalationController.php`; `config\chat.php`; `.env.example`.

### 6. Database Schema Summary

| Table | Purpose | Important Columns | Relationships / Notes |
|---|---|---|---|
| `users` | Local chat/admin users | identity/session/moderation fields including `blocked_at` | Used by chat sessions, memberships, messages, badges |
| `chat_rooms` | Room records | room identity, metadata/access fields | Has memberships and messages |
| `chat_room_memberships` | Room membership state | user/room, mute/member moderation fields | Links users to rooms |
| `chat_messages` | Room messages | user/room, text, reply, edit/delete/moderation fields | Supports reply-to and moderation |
| `chat_reports` | User/message/report records | reporter, target, reason/status | Admin review/hide/resolve |
| `chat_blocks` | User block relationships | blocker/blocked user IDs | Supports block/unblock |
| `chat_message_requests` | Direct message request workflow | sender/recipient/status | Request/accept/decline/cancel |
| `chat_direct_conversations` | Direct chat threads | participant IDs, read/mute state | Direct messages and realtime admission |
| `chat_invitation_tokens` | Invitation tokens | token, user/room context, expiry/status | Admin/user invitation flow |
| `badges`, `badge_awarders`, `user_badges` | Badge catalog and awards | badge metadata, awarder/user relationships | Community badge features |
| `sessions`, `cache`, `jobs` | Laravel infrastructure | standard Laravel columns | Session/queue/cache |

```text
users
  ├── chat_room_memberships
  │     └── chat_rooms
  │           └── chat_messages
  ├── chat_direct_conversations
  ├── chat_message_requests
  ├── chat_reports
  ├── chat_blocks
  └── user_badges
        └── badges
```

### 7. API and Route Inventory

| Method | Path / Endpoint | Purpose | Auth | Handler / File | Notes |
|---|---|---|---|---|---|
| GET | `/` and `/rooms/{path?}`, `/messages/{path?}`, `/users/{path?}`, `/badges/{path?}`, `/reports/{path?}` | Frontend app routes | Browser/session | `routes\web.php` | SPA-style views |
| GET | `/api/bootstrap` | App bootstrap | Open | `routes\web.php`; `BootstrapController` | Health path in `release.json` |
| POST | `/api/login` | Login | Throttled open | `routes\web.php` | throttle 6/min |
| GET/POST | `/api/chat/rooms*` | Rooms, membership, messages, reads, realtime admission | Local chat user/session for state-changing routes | `routes\web.php` | Room chat module |
| POST | `/api/chat/messages/{message}/report` | Report message | Local chat user | `ChatSafetyController` | Safety |
| POST | `/api/chat/users/{user}/block`, `/unblock`, `/report` | Block/report user | Local chat user | `ChatSafetyController` | Publishes block update when configured |
| POST | `/api/chat/escalate-to-hotline` | Hotline handoff payload | Local chat user | `HotlineEscalationController` | Returns HTTP 202 |
| GET/POST | `/api/chat/message-requests*` | Direct-message request workflow | Auth/session | `routes\web.php` | Auth group |
| GET/POST | `/api/chat/direct-conversations*` | Direct conversations/messages/read/mute/realtime admission | Auth/session | `routes\web.php` | Auth group |
| POST/PUT/DELETE | `/api/chat/badges*` | Badge catalog/awards | Auth/session | `routes\web.php` | Admin/community badge flow |
| POST | `/api/chat/admin/reports*` | Review/hide/resolve reports | Auth/session | `routes\web.php` | Moderation |

### 8. Data Flow and Operational Flow

```text
Citizen/local user
-> PBB Chat Laravel API
-> pbb_chat local database
-> optional Realtime admission for chat.thread/chat.direct rooms
-> PBB Realtime WebSocket
-> other local browser clients
```

```text
Chat user chooses Hotline escalation
-> POST /api/chat/escalate-to-hotline
-> validates local chat user
-> returns configured PBB_CHAT_HOTLINE_URL and optional summary/context metadata
```

### 9. Offline-First Behavior

`release.json` marks PBB Chat as local LAN only and disables public gateway exposure. The app can run without internet when local PHP/MySQL/assets/Realtime are available. Room/direct-message operation depends on local server and DB. Realtime live updates require the local PBB Realtime service. Durable browser-side offline queues, Relay sync, conflict handling, and service-worker/PWA behavior were not confirmed from code.

### 10. Integration with Other PBB Apps

| Integration | Direction | Protocol / Method | Purpose | Code Evidence |
|---|---|---|---|---|
| PBB Chat -> Realtime | HTTP backend ingest/admission and browser WebSocket SDK | Room/direct/notification realtime | `app\Services\ChatRealtimeEventPublisher.php`; `app\Http\Controllers\Api\ChatRealtimeController.php`; `config\chat.php` |
| PBB Chat -> Hotline | HTTP/browser handoff URL in response | Escalate chat context to Hotline | `HotlineEscalationController.php`; `PBB_CHAT_HOTLINE_URL` |
| PBB Chat -> Helper | Vendored frontend assets | Chat thread/composer/login/file/input/icon UI contracts | `README.md`; recent Helper `ui.loader.js` and changelog evidence |
| Landing -> PBB Chat | Launcher metadata | Local app launcher only; public gateway disabled | `release.json` |

### 11. Deployment and Runtime Architecture

PBB Chat is a Laravel app under WAMP/Apache or equivalent PHP web server pointing at `public/`. `release.json` declares PHP `>=8.2` and required extensions `curl`, `fileinfo`, `json`, `mbstring`, `openssl`, `pdo`, `pdo_mysql`, `tokenizer`, and `xml`. `.env.example` configures `APP_URL=https://chat.pbb.ph`, `DB_DATABASE=pbb_chat`, database sessions, `SESSION_DOMAIN=chat.pbb.ph`, and secure cookies. Build commands are Composer/npm standard Laravel/Vite commands; no Docker file was confirmed.

### 12. Security and Privacy Notes

| Risk | Severity | Evidence | Suggested Fix |
|---|---|---|---|
| Chat content, blocks, reports, and direct messages are locally stored citizen/community data | High | Chat migrations and routes | Define retention, moderation access, backup, and purge rules |
| Realtime backend ingress secret is required for trusted server publishing | High | `PBB_CHAT_REALTIME_BACKEND_INGRESS_SECRET`; `ChatRealtimeEventPublisher.php` | Generate per install and keep server-only |
| Token signing secret is required for realtime admission | High | `PBB_CHAT_REALTIME_TOKEN_SIGNING_SECRET`; `config\chat.php` | Generate per install and rotate on compromise |
| Public gateway must remain disabled unless explicitly designed | Medium | `release.json` has `public_gateway.enabled=false` | Keep Chat LAN-only; add tests/installer checks for gateway flag |
| Session lifetime is very long by default | Medium | `.env.example` `SESSION_LIFETIME=5256000` | Confirm local-device threat model and add lock/logout expectations |

### 13. Realtime Communication

Confirmed. `ChatRealtimeController` creates Realtime admission payloads for room/direct/notification flows. `ChatRealtimeEventPublisher` posts backend events to the configured Realtime URL with `X-Realtime-Backend-Secret`. Allowed room prefixes include `chat.thread.` and `chat.direct.`; room naming includes `chat.thread.pbb-chat.{room_uuid}`. Fallback behavior when backend ingress is not configured is to log and skip publish.

### 14. Mapping and Geolocation

No mapping/geolocation functionality found / Not confirmed from code.

### 15. Background Jobs, Schedulers, and Maintenance Tasks

| Task | Schedule / Trigger | Purpose | Code Evidence |
|---|---|---|---|
| Realtime event publish | Controller/service trigger | Publish chat safety/message-related events to Realtime backend ingress | `app\Services\ChatRealtimeEventPublisher.php` |
| Laravel queue infrastructure | Framework config | Queue tables/config exist, but Chat-specific worker task was not confirmed | migrations and Laravel config |

### 16. Configuration and Environment Variables

| Variable / Config | Purpose | Required | Default / Example | Related Module |
|---|---|---|---|---|
| `APP_URL` | Chat app URL | Yes | `https://chat.pbb.ph` | Laravel URL/session |
| `DB_DATABASE` | Local DB | Yes | `pbb_chat` | Database |
| `SESSION_DOMAIN` | Cookie domain | Yes | `chat.pbb.ph` | Session |
| `PBB_CHAT_HOME_HUB_ID` | Local hub identity | Yes | `local` | Bootstrap/topology |
| `PBB_CHAT_HOME_NAME` | Local hub display name | Yes | `Local Barangay` | Bootstrap |
| `PBB_CHAT_HOTLINE_URL` | Hotline handoff URL | Yes | `https://hotline.pbb.ph` | Escalation |
| `PBB_CHAT_REALTIME_URL` | Realtime base URL | Yes for realtime | `https://realtime.pbb.ph` | Realtime |
| `PBB_CHAT_REALTIME_SDK_URL` | Browser SDK path | Yes for realtime UI | `/vendor/realtime.pbb.ph/js/sdk/index.js` | Realtime UI |
| `PBB_CHAT_REALTIME_TOKEN_SIGNING_SECRET` | Realtime admission signing secret | Yes for realtime auth | empty | Realtime |
| `PBB_CHAT_REALTIME_BACKEND_INGRESS_SECRET` | Backend event publish secret | Yes for backend publish | empty | Realtime |
| `PBB_CHAT_REALTIME_TOKEN_TTL_SECONDS` | Realtime token lifetime | No | `3600` | Realtime |

### 17. Known Technical Debt and Gaps

| Area | Issue | Evidence | Recommended Next Step |
|---|---|---|---|
| Hotline escalation | Endpoint returns handoff payload only; direct Hotline incident/report creation not confirmed | `HotlineEscalationController.php` | Define whether Chat escalation remains handoff-only or creates Hotline records |
| Offline behavior | LAN operation confirmed; durable offline compose queue not confirmed | `release.json`; no service worker/outbox found in reviewed evidence | Decide whether PBB Chat needs offline message queueing |
| Public exposure | Release metadata disables gateway, but installer/policy should enforce it | `release.json` | Add Kit/Landing validation that Chat is not public-gateway exposed |
| Account/profile ownership | Local user/session model exists; Account Service now implements canonical identity with app-local sessions linked by `pbb_user_id` | `C:\wamp64\www\pbb\account\routes\web.php`; `C:\wamp64\www\pbb\account\routes\api.php`; `C:\wamp64\www\pbb\account\database\migrations` | Complete/verify Chat Account callback, logout, and local-user migration around `pbb_user_id` |

### 18. Testing Status

Composer and PHPUnit are present. Route/controller tests were not run during this documentation update. OpenAPI baseline exists at `public/openapi/pbb-chat.yaml`. Coverage for offline queueing, public-gateway disabled enforcement, and Realtime admission/security was not confirmed from test execution.

### 19. Evidence Summary

Evidence:
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

---

## App: PBB Games

### 1. Executive Technical Summary

PBB Games is a plain PHP optional local citizen engagement and emergency-preparedness learning app. It runs on the local PBB/WAMP node at `https://games.pbb.ph` with fallback `https://pbb.ph/games`. It is citizen-facing but not an emergency operations app. The README explicitly states version 1 does not call Hotline, Relay, Support, Realtime, Hub/HQ, Maestro, MapServer, or Kit Setup APIs. It uses local static assets and vendored Helper UI bundles, has no database, and includes modes that hide or disable games during active incidents or emergency state.

### 2. Repository Overview

| Area | Details |
|---|---|
| App Name | PBB Games |
| Local Path | `C:\wamp64\www\pbb\games` |
| Main Language | PHP, JavaScript |
| Main Framework | Plain PHP/static app |
| Frontend Framework | Plain JavaScript modules, Helper UI/game components |
| Backend Framework | None beyond PHP scripts/classes |
| Database | No database usage found / Not confirmed from code |
| Realtime Technology | None confirmed |
| Queue / Worker System | None confirmed |
| Package Manager | No root `package.json` or `composer.json` confirmed |
| Runtime Requirements | WAMP/Apache/PHP; local static assets |
| Main Entry Points | `index.php`, `health.php`, `src\ModePolicy.php`, `src\GameRegistry.php` |
| Important Config Files | `config\games.php`, `config\games.registry.php`, `manifest.json`, `README.md` |
| Important Environment Variables | None confirmed |
| Deployment Target | Optional local node app, local LAN |

### 3. App Purpose and PBB Role

PBB Games provides local games and preparedness learning activities for citizens connected to the local PBB network. It is intentionally outside core emergency operations. If unavailable, emergency reporting/SITREP/Relay operations are unaffected, but the optional local engagement surface is unavailable. It depends on vendored Helper UI assets; operational app API dependencies were not confirmed.

### 4. User Roles and Permissions

| Role | Purpose | Capabilities | Code Evidence |
|---|---|---|---|
| citizen/local visitor | Local app user | View visible games and preparedness activities based on mode policy | `README.md`; `index.php`; `src\ModePolicy.php` |
| setup/operator via config | Controls mode/availability through config | Set `normal`, `monitoring`, `active_incident`, or `emergency` mode and feature visibility | `config\games.php`; `src\ModePolicy.php` |

No formal authentication/authorization system was found.

### 5. Main Features and Modules

### Mode Policy

Purpose: Control game visibility and emergency-disabled behavior.
Main Code: `src\ModePolicy.php`; `config\games.php`.
Database Tables: None.
APIs / Routes: Used by `index.php` and `health.php`.
Offline Behavior: Local config-driven.
Sync Behavior: None confirmed.
Related PBB Apps: Landing may link to Games; no runtime API integration confirmed.
Evidence: `README.md`; `src\ModePolicy.php`; `config\games.php`.

### Game Registry

Purpose: Define available local, quick, learning, and retro game entries and their mode visibility.
Main Code: `src\GameRegistry.php`; `config\games.registry.php`.
Database Tables: None.
APIs / Routes: `index.php`, `health.php`.
Offline Behavior: Fully local after assets are installed.
Sync Behavior: None confirmed.
Related PBB Apps: PBB Helper.
Evidence: `config\games.registry.php`; `README.md`.

### Health Endpoint

Purpose: Report app status, enabled flag, mode, version, total/visible games, and categories.
Main Code: `health.php`.
Database Tables: None.
APIs / Routes: `GET /health.php`.
Offline Behavior: Fully local.
Sync Behavior: None.
Related PBB Apps: Landing or Kit Setup health checks may use it; direct integration not confirmed.
Evidence: `health.php`.

### 6. Database Schema Summary

No database usage found / Not confirmed from code.

```text
No database tables confirmed.
```

### 7. API and Route Inventory

| Method | Path / Endpoint | Purpose | Auth | Handler / File | Notes |
|---|---|---|---|---|---|
| GET | `/` | Games home/list/play surface | None confirmed | `index.php` | Mode policy controls visibility |
| GET | `/health.php` | JSON health/status summary | None confirmed | `health.php` | Reports mode/version/game counts |
| GET | `/manifest.json` | PWA/app metadata | None confirmed | `manifest.json` | Local/offline-safe manifest per README |

### 8. Data Flow and Operational Flow

```text
Citizen browser
-> PBB Games `index.php`
-> config/games.php + config/games.registry.php
-> local static assets and Helper UI bundle
```

```text
Health check
-> health.php
-> ModePolicy + GameRegistry
-> JSON status/mode/version/counts
```

### 9. Offline-First Behavior

README states internet is not required after installation. Game content/assets are local. `active_incident` mode hides Quick Games and Retro Corner and allows learning games; `emergency` mode disables all games and shows emergency guidance. No Relay outbox, conflict handling, or upstream sync exists because the app is not operational data infrastructure.

### 10. Integration with Other PBB Apps

| Integration | Direction | Protocol / Method | Purpose | Code Evidence |
|---|---|---|---|---|
| PBB Games -> Helper | Vendored static assets | UI/game components | `README.md`; `assets/helper`; Helper game component usage |
| PBB Games -> Hotline | Link/config only | `hotline_url` configured for emergency message/navigation | `config\games.php`; README says no Hotline API calls in v1 |
| Landing -> PBB Games | Launcher/navigation assumption | Local app link possible | README URL conventions; no direct registry evidence confirmed in Games code |

### 11. Deployment and Runtime Architecture

PBB Games is deployed as local PHP/static files under WAMP/Apache at `C:\wamp64\www\pbb\games`. README documents primary URL `https://games.pbb.ph` and fallback `https://pbb.ph/games`. No Docker, queue, database, Composer, or npm runtime was confirmed. Helper bundles are vendored from `C:\wamp64\www\hotline-helpers\dist` into `assets/helper`.

### 12. Security and Privacy Notes

| Risk | Severity | Evidence | Suggested Fix |
|---|---|---|---|
| Games must not distract during emergencies | Medium | `ModePolicy.php`; README emergency/active-incident mode behavior | Ensure Kit/operator runbook sets `emergency` or `active_incident` when needed |
| No auth on health endpoint | Low | `health.php` | Keep health data non-sensitive and local-only |
| Copyrighted ROM/assets must not be bundled | Medium | README states no commercial ROMs/BIOS/copyrighted assets | Keep asset review checklist before adding retro games |

### 13. Realtime Communication

No realtime functionality found / Not confirmed from code.

### 14. Mapping and Geolocation

No mapping/geolocation functionality found / Not confirmed from code.

### 15. Background Jobs, Schedulers, and Maintenance Tasks

| Task | Schedule / Trigger | Purpose | Code Evidence |
|---|---|---|---|
| None found | Not applicable | No cron, queue, worker, scheduler, or service task found | Local file inventory |

### 16. Configuration and Environment Variables

| Variable / Config | Purpose | Required | Default / Example | Related Module |
|---|---|---|---|---|
| `enabled` | App enabled flag | Yes | `true` | `config\games.php` |
| `mode` | Runtime mode | Yes | `normal` | `ModePolicy` |
| `show_quick_games` | Quick games visibility | No | `true` | Registry/policy |
| `show_learning_games` | Learning games visibility | No | `true` | Registry/policy |
| `show_retro_corner` | Retro corner visibility | No | `true` | Registry/policy |
| `landing_url` | Local hub URL | No | `https://pbb.ph` | Navigation |
| `hotline_url` | Hotline URL for emergency guidance | No | `https://hotline.pbb.ph` | Emergency guidance |
| `version` | App version | Yes | `0.2.13` | Health endpoint |

### 17. Known Technical Debt and Gaps

| Area | Issue | Evidence | Recommended Next Step |
|---|---|---|---|
| Emergency mode control | No runtime API/admin UI confirmed for mode changes | `config\games.php` | Document how Kit/operator changes mode during active incidents |
| Operational integration | README explicitly says no v1 API calls to core PBB apps | `README.md` | Keep this boundary or define a future read-only status contract |
| Tests | No root package/composer test runner confirmed | file inventory | Add lightweight PHP/unit checks for mode policy and registry visibility |

### 18. Testing Status

Manual/local tests were not run during this documentation update. No root package test script was confirmed. Health behavior is inspectable through `health.php`.

### 19. Evidence Summary

Evidence:
- `C:\wamp64\www\pbb\games\README.md`
- `C:\wamp64\www\pbb\games\index.php`
- `C:\wamp64\www\pbb\games\health.php`
- `C:\wamp64\www\pbb\games\config\games.php`
- `C:\wamp64\www\pbb\games\config\games.registry.php`
- `C:\wamp64\www\pbb\games\src\ModePolicy.php`
- `C:\wamp64\www\pbb\games\src\GameRegistry.php`
- `C:\wamp64\www\pbb\games\manifest.json`

---

## App: PBB Library (Proposal / No Implementation Found)

### 1. Executive Technical Summary

PBB Library is a proposal-stage general-knowledge and reference infrastructure project. The local folder contains only Markdown proposal/specification files. No executable app code, routes, migrations, package manifests, or runtime configuration were found. The proposal defines two future deployables: PBB Library Cloud for content ingestion/governance/release construction, and PBB Library Node for offline local catalog/search/resource serving and release activation at `https://library.pbb.ph`. It is intended to support citizens, students, teachers, responders, health workers, and PBB Learning as a reference provider. It is not intended to own formal courses, assignments, grades, learner progress, or institutional LMS records.

### 2. Repository Overview

| Area | Details |
|---|---|
| App Name | PBB Library |
| Local Path | `C:\wamp64\www\pbb\library` |
| Main Language | Unknown / Not confirmed from code |
| Main Framework | Unknown / Not confirmed from code |
| Frontend Framework | Unknown / Not confirmed from code |
| Backend Framework | Unknown / Not confirmed from code |
| Database | Proposed only; no migrations/schema found |
| Realtime Technology | Deferred/optional; no implementation found |
| Queue / Worker System | Proposed Cloud/Node jobs; no implementation found |
| Package Manager | None found |
| Runtime Requirements | Proposal mentions web/API app, relational DB, search engine, deployment worker, scheduler, dedicated Library HDD, Kit Setup/WinSW services |
| Main Entry Points | No executable entry points found |
| Important Config Files | Proposal docs only |
| Important Environment Variables | Proposed `PBB_LIBRARY_BASE_URL`, `PBB_LIBRARY_API_TOKEN` in Learning addendum; no implementation config found |
| Deployment Target | Proposed Library Cloud and local Library Node |

### 3. App Purpose and PBB Role

PBB Library is proposed to provide centrally governed general-reference content, offline node search, content package verification, release activation, rollback, and local resource serving. Library Cloud would govern sources, provenance, licensing, signed releases, node profiles, and fleet deployment reports. Library Node would serve approved immutable releases locally and expose read-only APIs for Learning and approved clients. If unavailable, PBB Learning should continue operating but research-library features should degrade gracefully.

### 4. User Roles and Permissions

| Role | Purpose | Capabilities | Code Evidence |
|---|---|---|---|
| anonymous public user | Proposed local Library reader | Search/browse/view public Library content | `library\node\docs\PBB_LIBRARY_NODE_DEVELOPMENT_PROPOSAL.md` |
| Library Node administrator | Proposed local admin | View release/storage/search/integration health; no canonical content editing in V1 | `library\node\docs\PBB_LIBRARY_NODE_DEVELOPMENT_PROPOSAL.md` |
| Library Cloud administrator | Proposed cloud admin | Source ingestion, review, releases, signing, node assignment, audit | `library\cloud\docs\PBB_LIBRARY_CLOUD_DEVELOPMENT_PROPOSAL.md` |
| PBB Learning client | Proposed local API client | Search resources, retrieve metadata/citations, link to resources | `learning\docs\PBB_LEARNING_PBB_LIBRARY_INTEGRATION_ADDENDUM.md` |
| machine client | Proposed Cloud/Node release client | Authenticated offer/download/reporting APIs | `library\cloud\docs\PBB_LIBRARY_CLOUD_DEVELOPMENT_PROPOSAL.md`; `library\node\docs\PBB_LIBRARY_NODE_DEVELOPMENT_PROPOSAL.md` |

### 5. Main Features and Modules

### Library Cloud

Purpose: Proposed content governance, ingestion, normalization, licensing, review, object storage, release building, manifest signing, node assignment, and fleet reporting.
Main Code: No implementation found.
Database Tables: Proposed only.
APIs / Routes: Proposed node offer/object download/report APIs.
Offline Behavior: Cloud is not the public offline portal; offline behavior belongs to Node.
Sync Behavior: Node-pull release distribution; large content transfer should not use Relay.
Related PBB Apps: Library Node, Kit Setup, Landing, Learning.
Evidence: `library\cloud\docs\PBB_LIBRARY_CLOUD_DEVELOPMENT_PROPOSAL.md`.

### Library Node

Purpose: Proposed local offline portal, catalog/search, resource viewing, release verification, staging, activation, rollback, storage health, and Learning integration API.
Main Code: No implementation found.
Database Tables: Proposed only.
APIs / Routes: Proposed public routes `/`, `/search`, `/collections`, `/resources/{id}`, `/health`; proposed versioned API `/api/v1/search`, `/api/v1/resources/{id}`, `/api/v1/collections`, `/api/v1/citations/{id}`.
Offline Behavior: Intended to work without internet, Cloud, Learning, Account, or Realtime after commissioning.
Sync Behavior: Node initiates authenticated Cloud offer/download/report flow.
Related PBB Apps: PBB Learning, Landing, Kit Setup, Account-ready admin, possible Maestro observation later.
Evidence: `library\node\docs\PBB_LIBRARY_NODE_DEVELOPMENT_PROPOSAL.md`.

### Learning Reference Provider Integration

Purpose: Let PBB Learning use Library as a reference provider without copying Library content or treating Library as an LMS.
Main Code: No implementation found.
Database Tables: Proposed Learning-side reference provider tables.
APIs / Routes: Proposed adapter methods `search`, `resource`, `collections`, `citation`, `related`.
Offline Behavior: Learning should degrade gracefully when Library is unavailable.
Sync Behavior: Learning is not in Library release-update path.
Related PBB Apps: PBB Learning.
Evidence: `learning\docs\PBB_LEARNING_PBB_LIBRARY_INTEGRATION_ADDENDUM.md`.

### 6. Database Schema Summary

No implemented database usage found / Not confirmed from code.

Proposal documents suggest Cloud database groups for sources, canonical resources, files/renditions, collections, node profiles, releases, signing/audit, deployment reports, and operations. Node proposal suggests local groups for releases, objects, catalog resources, search/index state, deployment sessions, settings, health, and integration state.

```text
Library Cloud proposal
  ├── sources / imported records
  ├── canonical resources / collections
  ├── content-addressed objects
  ├── signed releases / node profiles
  └── deployment reports / audit

Library Node proposal
  ├── active/staged releases
  ├── immutable object inventory
  ├── local catalog/search indexes
  ├── deployment sessions
  └── Learning integration health
```

### 7. API and Route Inventory

No implemented routes found.

| Method | Path / Endpoint | Purpose | Auth | Handler / File | Notes |
|---|---|---|---|---|---|
| GET | `/` | Proposed Library home | Public | No implementation found | Node proposal |
| GET | `/search` | Proposed public search | Public | No implementation found | Node proposal |
| GET | `/collections` | Proposed collection browse | Public | No implementation found | Node proposal |
| GET | `/resources/{id}` | Proposed resource viewer | Public / collection policy | No implementation found | Node proposal |
| GET | `/api/v1/search` | Proposed Learning/public search API | Public or app credential depending route | No implementation found | Addendum |
| GET | `/api/v1/resources/{id}` | Proposed metadata/resource API | Public or app credential depending route | No implementation found | Addendum |
| GET | `/api/v1/citations/{id}` | Proposed citation API | Public or app credential depending route | No implementation found | Addendum |
| POST/GET | Cloud offer/object/report APIs | Proposed node-pull release distribution | Machine credential | No implementation found | Cloud/Node proposals |

### 8. Data Flow and Operational Flow

```text
Approved upstream sources
-> PBB Library Cloud ingestion/review
-> canonical metadata + object store
-> signed release manifest
-> Library Node authenticated pull
-> object verification + catalog import + search index
-> atomic activation
-> local public search/resource access
-> PBB Learning reference-provider adapter
```

### 9. Offline-First Behavior

Library Node is explicitly proposed as offline-first after commissioning. Public search, browsing, viewing, and citations should work without internet, Cloud, Learning, Account, or Realtime. Library Cloud is required for new approved releases, but Node should continue serving the active release when Cloud is unreachable. Large routine release transfer should not use PBB Relay.

### 10. Integration with Other PBB Apps

| Integration | Direction | Protocol / Method | Purpose | Code Evidence |
|---|---|---|---|---|
| Library Node -> Library Cloud | Outbound authenticated pull | Offer API, object download, deployment report | Signed release distribution | Proposal docs |
| PBB Learning -> Library Node | Local API/reference adapter | Search/resource/citation/collections | Research Library integration | Learning addendum |
| Kit Setup -> Library Node | Installer/Data Prep | Install, trust keys, dedicated HDD, physical release import, smoke tests | Commissioning | Library proposals; Learning addendum |
| Library Node -> Landing | Registry/launcher | Local launcher; public gateway disabled for browsing | App discovery | Library Node proposal |
| Cloud -> Landing -> Library Node | Controlled machine gateway concept | Release operations only, not bulk browsing | Periodic update reachability | Library proposal |
| Account -> Library admin | Proposed/ready | `pbb_user_id` linked local admins | Admin identity | Library Node proposal |

### 11. Deployment and Runtime Architecture

No implementation found. Proposal recommends separate Cloud and Node deployables, local Library Node under `https://library.pbb.ph`, a dedicated Library HDD for content objects, SSD for app/database/search indexes, deployment worker/scheduler, Windows services installed by Kit Setup/WinSW, and physical initial release import during Kit Setup Data Prep.

### 12. Security and Privacy Notes

| Risk | Severity | Evidence | Suggested Fix |
|---|---|---|---|
| Large content release pipeline requires strong signing/trust model | High | Library Cloud/Node proposal Ed25519 signed manifest requirements | Implement manifest signing, trust store, downgrade protection, and test vectors before release |
| License/provenance mistakes could create redistribution risk | High | Cloud proposal provenance/licensing sections | Require policy decisions and review before content is deployable |
| Public search API must not expose filesystem paths or deployment controls | High | Node proposal security/acceptance criteria | Separate public read APIs from admin/machine APIs |
| Library updates must not degrade emergency apps | High | Emergency-safe design sections | Enforce update eligibility, alert-level gates, bandwidth throttles, and service isolation |
| User privacy should be minimal in V1 | Medium | Library privacy sections | Avoid mandatory login and do not upload raw citizen search queries to Cloud |

### 13. Realtime Communication

No realtime functionality found / Not confirmed from code. Realtime is listed as optional/deferred for non-essential UI refresh in proposals.

### 14. Mapping and Geolocation

No mapping/geolocation functionality found / Not confirmed from code.

### 15. Background Jobs, Schedulers, and Maintenance Tasks

| Task | Schedule / Trigger | Purpose | Code Evidence |
|---|---|---|---|
| Cloud import/review/release jobs | Proposed | Source ingestion, malware scan, normalization, release build/signing | Cloud proposal |
| Node deployment worker | Proposed | Offer check, download, verify, import, index, activate, smoke test | Node proposal |
| Node scheduler | Proposed | Emergency-safe update windows and health checks | Node proposal |
| Garbage collection | Proposed | Remove unreferenced content objects safely | Node proposal |

### 16. Configuration and Environment Variables

No implemented config found.

| Variable / Config | Purpose | Required | Default / Example | Related Module |
|---|---|---|---|---|
| `PBB_LIBRARY_BASE_URL` | Proposed Learning-to-Library base URL | If integrated | `https://library.pbb.ph` | Learning addendum |
| `PBB_LIBRARY_API_TOKEN` | Proposed app-scoped integration token | If protected routes used | Masked | Learning addendum |
| dedicated Library volume identity | Proposed content HDD binding | Yes for Node | Unknown / Not confirmed from code | Kit Setup/Data Prep |
| Cloud machine credentials/trust keys | Proposed node-pull auth and manifest verification | Yes for release updates | Unknown / Not confirmed from code | Cloud/Node |

### 17. Known Technical Debt and Gaps

| Area | Issue | Evidence | Recommended Next Step |
|---|---|---|---|
| Implementation | Only proposal docs found | Folder scan | Scaffold separate Cloud and Node apps or create contract package first |
| Release contract | Manifest/API schemas are proposed, not implemented | Library proposals | Define versioned JSON Schema/OpenAPI and signature test vectors |
| Storage sizing | 10 TB drive assumptions need prototype measurements | Library proposal | Run target-hardware benchmarks and capacity tests |
| Emergency gating | Node-state source is an open question | Library proposal open questions | Define approved local node-state API |
| Learning adapter | Integration is proposed, not implemented | Learning addendum | Implement direct link/health check before integrated search |

### 18. Testing Status

No test framework or executable tests found. Proposal documents require tests for ingestion, release signing, object verification, offline operation, Learning integration, emergency mode, storage failure, rollback, and security.

### 19. Evidence Summary

Evidence:
- `C:\wamp64\www\pbb\library\docs\PBB_LIBRARY_DEVELOPMENT_PROPOSAL.md`
- `C:\wamp64\www\pbb\library\node\docs\PBB_LIBRARY_NODE_DEVELOPMENT_PROPOSAL.md`
- `C:\wamp64\www\pbb\library\cloud\docs\PBB_LIBRARY_CLOUD_DEVELOPMENT_PROPOSAL.md`
- `C:\wamp64\www\pbb\learning\docs\PBB_LEARNING_PBB_LIBRARY_INTEGRATION_ADDENDUM.md`

---

## App: PBB Learning / Lumaria (Proposal / No Implementation Found)

### 1. Executive Technical Summary

PBB Learning, product name Lumaria, is a proposal-stage LMS-agnostic local learning gateway and control plane. The local folder contains only Markdown proposal/addendum files. No executable app code, routes, migrations, package manifests, or runtime configuration were found. The proposal positions Moodle as the first supported LMS adapter, not the whole product. Learning should provide `https://learning.pbb.ph`, an admin surface, provider/platform/instance/server/catalog/package registries, local/offline availability labels, emergency-mode rules, health checks, diagnostics, PBB Account as preferred identity, PBB Landing integration, and PBB Library as a separate reference provider.

### 2. Repository Overview

| Area | Details |
|---|---|
| App Name | PBB Learning / Lumaria |
| Local Path | `C:\wamp64\www\pbb\learning` |
| Main Language | Unknown / Not confirmed from code |
| Main Framework | Unknown / Not confirmed from code |
| Frontend Framework | Unknown / Not confirmed from code |
| Backend Framework | Unknown / Not confirmed from code |
| Database | Proposed only; no migrations/schema found |
| Realtime Technology | Unknown / Not confirmed from code |
| Queue / Worker System | Proposed job tracking/provisioning; no implementation found |
| Package Manager | None found |
| Runtime Requirements | Proposal suggests web/API app, admin UI, health checks, Moodle/external LMS adapters, optional dedicated learning server/machine |
| Main Entry Points | No executable entry points found |
| Important Config Files | Proposal docs only |
| Important Environment Variables | Proposed Library integration vars in addendum; no implementation config found |
| Deployment Target | Local PBB node or dedicated learning server; public surface `https://learning.pbb.ph` |

### 3. App Purpose and PBB Role

PBB Learning is proposed to organize learning access without replacing institutional LMS systems. It should manage providers, platforms, instances, servers, catalog items, packages, health checks, diagnostics, emergency visibility, and links/local availability. It should not merge institutional LMS databases or own general-reference Library content. If unavailable, Library and core emergency apps should remain unaffected; LMS links/content may be unavailable through the unified Learning portal.

### 4. User Roles and Permissions

| Role | Purpose | Capabilities | Code Evidence |
|---|---|---|---|
| public learner/user | Proposed local learning user | Browse catalog, offline content, emergency learning, provider pages | Learning proposal |
| learning administrator | Proposed admin | Manage providers, platforms, instances, servers, catalog, packages, health/diagnostics | Learning proposal |
| provider/institution admin | Proposed partner-facing operator | Manage institution-linked learning records according to policy | Learning proposal |
| PBB Account user/admin | Preferred identity model | Portal/admin authentication; future `pbb_user_id` support | Learning proposal |
| Library reference provider client | Learning server-side integration | Search/reference/citation through Library Node | Learning addendum |

### 5. Main Features and Modules

### Learning Gateway And Catalog

Purpose: Unified local public portal for learning providers, courses/packages, local/offline availability, external links, and emergency-prioritized content.
Main Code: No implementation found.
Database Tables: Proposed providers/platforms/instances/servers/catalog/package/job tables.
APIs / Routes: Proposed public routes `/`, `/providers`, `/catalog`, `/offline`, `/emergency`, `/research`.
Offline Behavior: Proposed local catalog and local links/content should work without cloud.
Sync Behavior: No implemented sync; proposal rejects full LMS database sync in V1.
Related PBB Apps: Landing, Account, Library, core emergency apps through emergency-safe policy.
Evidence: `learning\docs\PBB_LEARNING_DEVELOPMENT_PROPOSAL_UPDATED.md`.

### LMS Adapter Layer

Purpose: Support Moodle first while keeping the architecture LMS-agnostic for Canvas, Open edX, external links, static packages, and future adapters.
Main Code: No implementation found.
Database Tables: Proposed platform/instance/server/package/job tables.
APIs / Routes: Proposed admin/API route structures.
Offline Behavior: Depends on local LMS/content instance availability.
Sync Behavior: No deep Moodle DB sync in V1.
Related PBB Apps: Kit Setup, Landing, Account.
Evidence: Learning proposal.

### PBB Library Reference Provider

Purpose: Integrate Library as a research/reference provider, not as an LMS.
Main Code: No implementation found.
Database Tables: Proposed Learning-side provider/reference/link tables.
APIs / Routes: Proposed direct launch, integrated search, contextual research, and citation/resource calls.
Offline Behavior: Learning should continue if Library is unavailable and show a graceful unavailable state.
Sync Behavior: Learning does not participate in Library release updates.
Related PBB Apps: PBB Library Node/Cloud, Kit Setup, Landing.
Evidence: `learning\docs\PBB_LEARNING_PBB_LIBRARY_INTEGRATION_ADDENDUM.md`.

### 6. Database Schema Summary

No implemented database usage found / Not confirmed from code.

Proposal documents suggest tables for providers, platforms, instances, servers, catalog items, packages, jobs, health checks, emergency policies, Library reference providers, Library resource links, integration health, and audit logs.

```text
learning_providers
  └── learning_instances
        ├── learning_catalog_items
        ├── learning_packages
        └── learning_jobs

learning_reference_providers
  └── course_to_library_reference_links
```

### 7. API and Route Inventory

No implemented routes found.

| Method | Path / Endpoint | Purpose | Auth | Handler / File | Notes |
|---|---|---|---|---|---|
| GET | `/` | Proposed learning home | Public | No implementation found | Proposal |
| GET | `/providers` | Proposed providers page | Public | No implementation found | Proposal |
| GET | `/catalog` | Proposed learning catalog | Public | No implementation found | Proposal |
| GET | `/offline` | Proposed offline-available learning | Public | No implementation found | Proposal |
| GET | `/emergency` | Proposed emergency/prioritized learning | Public | No implementation found | Proposal |
| GET | `/research` | Proposed Library research entry | Public | No implementation found | Addendum |
| Admin/API routes | `/admin/*`, `/api/*` | Proposed provider/platform/instance/package/health/job management | Account/admin | No implementation found | Proposal |

### 8. Data Flow and Operational Flow

```text
User
-> learning.pbb.ph
-> Learning catalog/provider/instance registry
-> local LMS/content instance or external LMS link
-> optional Research Library action
-> PBB Library Node search/resource/citation API
-> return to LMS/course workflow
```

### 9. Offline-First Behavior

Learning is proposed as local-node-first. Local catalog browsing, provider pages, local/offline availability labels, links to local LMS/content instances, diagnostics, and emergency filtering should work locally. External partner LMS links require internet or reachable partner infrastructure. Learning workloads must be throttled/disabled if they risk core emergency apps.

### 10. Integration with Other PBB Apps

| Integration | Direction | Protocol / Method | Purpose | Code Evidence |
|---|---|---|---|---|
| Learning -> PBB Library Node | Proposed local API/reference adapter | Search/resources/citations/collections | Research Library | Learning addendum |
| Learning -> PBB Account | Proposed SSO/preferred identity | Portal/admin authentication, future `pbb_user_id` support | Identity | Learning proposal |
| Learning -> PBB Landing | Proposed registration metadata | Launcher/catalog visibility | App discovery | Learning proposal/addendum |
| Kit Setup -> Learning/Library | Proposed install/config/smoke tests | Configure Learning-Library trust and Library URL/token | Commissioning | Learning addendum |
| Learning -> Moodle/external LMS | Proposed adapter/link/health check | Course access and local/external LMS management | Learning runtime | Learning proposal |

### 11. Deployment and Runtime Architecture

No implementation found. Proposal suggests `https://learning.pbb.ph`, admin surface `/admin`, possible shared node deployment for low-risk content, dedicated LMS instances, and dedicated LMS machines for heavier workloads. V2/V3 propose Moodle provisioning, DNS/Apache/Technitium/Kit Setup integration, backups, diagnostics, and remote learning agents.

### 12. Security and Privacy Notes

| Risk | Severity | Evidence | Suggested Fix |
|---|---|---|---|
| Learning workload could degrade emergency apps | High | Learning emergency-safe design sections | Enforce emergency mode, resource limits, and dedicated learning machines for heavy LMS |
| Institutional LMS records are sensitive | High | Proposal rejects full LMS DB sync | Keep grade/submission/enrollment records under institution/LMS authority |
| Account/identity integration not implemented | Medium | Proposal only | Define Account SSO/admin roles before implementation |
| Library credentials must be separated from Learning | High | Addendum security boundaries | Do not give Learning Library Cloud deployment credentials |
| LMS admin credentials must not leak to Library | High | Addendum security boundaries | Keep LMS admin credentials inside Learning/adapter layer |

### 13. Realtime Communication

No realtime functionality found / Not confirmed from code.

### 14. Mapping and Geolocation

No mapping/geolocation functionality found / Not confirmed from code.

### 15. Background Jobs, Schedulers, and Maintenance Tasks

| Task | Schedule / Trigger | Purpose | Code Evidence |
|---|---|---|---|
| Health checks | Proposed | Check LMS/external/local availability | Learning proposal |
| Provisioning jobs | Proposed V2/V3 | Moodle instance/server/package operations | Learning proposal |
| Backup/diagnostic jobs | Proposed | LMS backups and diagnostics | Learning proposal |
| Library integration smoke tests | Proposed | Verify Library health/search/citation | Learning addendum |

### 16. Configuration and Environment Variables

No implemented config found.

| Variable / Config | Purpose | Required | Default / Example | Related Module |
|---|---|---|---|---|
| `PBB_LIBRARY_BASE_URL` | Proposed local Library URL | If Library integration enabled | `https://library.pbb.ph` | Library adapter |
| `PBB_LIBRARY_API_TOKEN` | Proposed app-scoped Library API token | If protected routes used | Masked | Library adapter |
| Account SSO config | Proposed preferred identity | If Account enabled | Unknown / Not confirmed from code | Auth |
| Moodle/external LMS settings | Proposed adapter config | Per provider/instance | Unknown / Not confirmed from code | LMS adapters |

### 17. Known Technical Debt and Gaps

| Area | Issue | Evidence | Recommended Next Step |
|---|---|---|---|
| Implementation | Only proposal docs found | Folder scan | Scaffold app or create contract docs first |
| LMS adapter boundary | Moodle-first but LMS-agnostic design is not implemented | Learning proposal | Define platform-neutral schema/routes before Moodle-specific work |
| Account integration | Preferred identity only proposed | Learning proposal | Reuse Account SDK/conventions from Chat/Hotline/Support/etc. |
| Library adapter | Addendum proposes adapter/API but no code | Learning addendum | Implement direct link and health check before integrated search |
| Emergency mode | Policy proposed but not wired to node state | Learning proposal | Define shared emergency/node-state signal |

### 18. Testing Status

No test framework or executable tests found. Proposal documents call for Learning catalog, provider/instance, health, emergency mode, LMS adapter, Library integration, and unavailable-state tests.

### 19. Evidence Summary

Evidence:
- `C:\wamp64\www\pbb\learning\docs\PBB_LEARNING_DEVELOPMENT_PROPOSAL_UPDATED.md`
- `C:\wamp64\www\pbb\learning\docs\PBB_LEARNING_PBB_LIBRARY_INTEGRATION_ADDENDUM.md`
- `C:\wamp64\www\pbb\library\docs\PBB_LIBRARY_DEVELOPMENT_PROPOSAL.md`

---

## App: PBB Natalium

### 1. Executive Technical Summary

PBB Natalium is a local health-center foundation app for patient identity/registry workflows, practitioner and capability management, patient profile applications, document intake/review, patient access grants, and audit events. It runs as a custom PHP/Composer app on a local PBB node with its own MySQL schema. Code confirms PBB Account SSO integration hooks, Account-admin user sync, local user/capability enforcement, local document storage, and use of Relay `public/hub.json` as node context. Chatviewer messages from PBB Natalium describe the broader product direction as barangay-level continuity-of-care health workflows; those broader programs are planning/design context unless implemented in code.

### 2. Repository Overview

| Area | Details |
|---|---|
| App Name | PBB Natalium |
| Local Path | `C:\wamp64\www\pbb\natalium` |
| Main Language | PHP |
| Main Framework | Custom PHP app using Composer PSR-4 autoloading |
| Frontend Framework | Unknown / Not confirmed from code |
| Backend Framework | Custom PHP router/container/controllers; not Laravel |
| Database | MySQL `pbb_natalium` via `database\schema.sql` |
| Realtime Technology | No realtime functionality found / Not confirmed from code |
| Queue / Worker System | No queue/worker system found / Not confirmed from code |
| Package Manager | Composer |
| Runtime Requirements | PHP; config references `C:\wamp64\bin\php\php8.2.29\php.exe`; MySQL |
| Main Entry Points | `public\index.php`, `routes\api.php`, `bin\migrate.php`, `bin\test.php` |
| Important Config Files | `composer.json`, `config\app.php`, `database\schema.sql` |
| Important Environment Variables | `NATALIUM_ACCOUNT_CLIENT_SECRET`, `NATALIUM_ACCOUNT_REDIRECT_URI`, `NATALIUM_ACCOUNT_POST_LOGOUT_REDIRECT_URI`, `NATALIUM_ACCOUNT_ADMIN_API_ENABLED`, `NATALIUM_ACCOUNT_ADMIN_API_TOKEN`, `NATALIUM_ACCOUNT_ADMIN_API_CLIENT` |
| Deployment Target | Local PBB node health-center/barangay service |

### 3. App Purpose and PBB Role

Natalium solves local health-center identity and patient registry workflow needs. Code confirms local practitioners, users, patient applications, patient records, contacts, addresses, relationships, access grants, documents, audit events, and search logs. It depends on PBB Account for SSO/admin sync and reads local Relay `public\hub.json` for node context. Other PBB apps do not currently depend on Natalium from reviewed code. If Natalium is unavailable, emergency Hotline/Relay/SITREP flows are not directly blocked, but local health registry, profile application, document review, and practitioner access workflows are unavailable.

### 4. User Roles and Permissions

| Role | Purpose | Capabilities | Code Evidence |
|---|---|---|---|
| local user | App-local user row linked to Account SSO identity | Login/session identity, status, role, capability grants | `routes\api.php`; `database\schema.sql`; `bin\test.php` |
| admin / user manager | Manage local users and capabilities | View/update users; grant/revoke capabilities | `routes\api.php`; `bin\test.php` |
| practitioner | Health worker profile | Practitioner profile and capability grants | `routes\api.php`; `database\schema.sql` |
| patient / applicant | Patient profile subject or applicant | Submit profile applications, requirements, access grant requests | `routes\api.php`; `database\schema.sql` |
| Account admin API client | Server-to-server sync client | Sync Account users into Natalium when enabled and token-authenticated | `routes\api.php`; `config\app.php`; `bin\test.php` |

### 5. Main Features and Modules

### Account SSO And Local Session

Purpose: Redirect users to PBB Account, process callback, create/update app-local identity, and logout through Account.
Main Code: `routes\api.php`; Account SSO classes under `app`.
Database Tables: `users`, `user_capabilities`, `audit_events`.
APIs / Routes: `GET /auth/login`, `GET /auth/callback`, `GET|POST /auth/logout`, `GET /api/session/me`.
Offline Behavior: Requires the configured Account base URL for new SSO login. Existing app-local session behavior beyond code paths was not fully confirmed.
Sync Behavior: Account-admin user sync endpoint exists.
Related PBB Apps: PBB Account.
Evidence: `config\app.php`; `routes\api.php`; `bin\test.php`.

### User, Capability, And Practitioner Management

Purpose: Manage local users, user capabilities, practitioner profiles, and practitioner capabilities.
Main Code: `routes\api.php`; controllers/services under `app`.
Database Tables: `users`, `user_capabilities`, `practitioner_profiles`, `practitioner_capabilities`.
APIs / Routes: `GET /api/users`, `GET /api/users/{id}`, `PATCH /api/users/{id}`, `POST /api/users/{id}/capabilities`, `DELETE /api/users/{id}/capabilities/{code}`, `GET|POST /api/practitioners`, `PATCH /api/practitioners/{id}`, practitioner capability grant/revoke routes.
Offline Behavior: Local database-backed once app is installed.
Sync Behavior: Account user sync exists; no broader upstream sync found.
Related PBB Apps: PBB Account.
Evidence: `routes\api.php`; `database\schema.sql`; `bin\test.php`.

### Patient Profile Applications And Registry

Purpose: Accept/review patient profile applications and create patient registry records with contacts, addresses, and relationships.
Main Code: `routes\api.php`; application/review services under `app`.
Database Tables: `patient_profile_applications`, `patient_profile_application_requirements`, `patients`, `patient_contacts`, `patient_addresses`, `patient_relationships`.
APIs / Routes: `GET /api/patients/search`; patient profile application create/submit/review requirement/approve routes.
Offline Behavior: Local database-backed.
Sync Behavior: No Relay/upstream sync found.
Related PBB Apps: Unknown / Not confirmed from code.
Evidence: `routes\api.php`; `database\schema.sql`; `bin\test.php`.

### Documents, Access Grants, Audit, And Search Logs

Purpose: Store document metadata/review events, patient access grants, audit events, and patient search logs.
Main Code: `routes\api.php`; document/access/audit services under `app`.
Database Tables: `documents`, `document_review_events`, `patient_access_grants`, `audit_events`, `patient_search_logs`.
APIs / Routes: patient access grant request/approve routes; document review behavior covered by tests.
Offline Behavior: Local database and local `storage\documents`.
Sync Behavior: No Relay/upstream sync found.
Related PBB Apps: Unknown / Not confirmed from code.
Evidence: `database\schema.sql`; `config\app.php`; `bin\test.php`.

### 6. Database Schema Summary

| Table | Purpose | Important Columns | Relationships / Notes |
|---|---|---|---|
| `settings` | Local app settings | key/value style fields | Exact fields from `schema.sql` |
| `users` | Local users linked to Account identity | Account/user identity, role/status fields | Capability source for guards/tests |
| `user_capabilities` | Capability grants | user reference, capability code | Used by management guards |
| `practitioner_profiles` | Practitioner records | user reference, profile fields | Linked to local users |
| `practitioner_capabilities` | Practitioner capability grants | practitioner reference, capability code | Health-worker specific capabilities |
| `patient_profile_applications` | Patient profile application workflow | applicant/patient fields, status | Duplicate and requirement gates covered by tests |
| `patient_profile_application_requirements` | Application requirement tracking | application reference, requirement/status | Gates approval |
| `patients` | Patient registry | identity/demographic fields | Root patient record |
| `patient_contacts` | Patient contact details | patient reference, contact fields | Patient child table |
| `patient_addresses` | Patient addresses | patient reference, address/location fields | Latitude/longitude presence not fully confirmed from docs excerpt; see schema |
| `patient_relationships` | Patient relationships | patient references/relationship fields | Patient child/cross-reference table |
| `patient_access_grants` | Access permission workflow | patient/user/status fields | Request/approve flow |
| `documents` | Document metadata/storage references | document owner, storage reference, review status | Tests confirm storage path hiding behavior |
| `audit_events` | Audit trail | actor/action/metadata fields | Used by sensitive workflows |
| `patient_search_logs` | Search audit | user/search fields | Records patient search activity |
| `document_review_events` | Document review audit | document reference/reviewer/status | Supports review history |

```text
users
  ├── user_capabilities
  └── practitioner_profiles
        └── practitioner_capabilities

patient_profile_applications
  └── patient_profile_application_requirements

patients
  ├── patient_contacts
  ├── patient_addresses
  ├── patient_relationships
  ├── patient_access_grants
  └── documents
        └── document_review_events

audit_events
patient_search_logs
```

### 7. API and Route Inventory

| Method | Path / Endpoint | Purpose | Auth | Handler / File | Notes |
|---|---|---|---|---|---|
| GET | `/auth/login` | Start Account SSO | Public start | `routes\api.php` | Redirects to Account authorize URL |
| GET | `/auth/callback` | Complete SSO callback | Account callback state | `routes\api.php` | Creates local session/user link |
| GET/POST | `/auth/logout` | Logout via Account | Session | `routes\api.php` | Redirects to Account logout |
| GET | `/api/node-context` | Return local node context | Session/guard not fully confirmed | `routes\api.php` | Uses configured Relay `hub.json` path |
| GET | `/api/session/me` | Current user/session | Session | `routes\api.php` | Local app identity |
| POST | `/api/account-admin/users/sync` | Sync Account users | Account admin token/client guard | `routes\api.php` | Disabled by default in config/tests |
| GET | `/api/users` | List users | Capability guard | `routes\api.php` | Capability behavior covered by tests |
| GET | `/api/users/{id}` | User details | Capability guard | `routes\api.php` |  |
| PATCH | `/api/users/{id}` | Update user | Capability guard | `routes\api.php` | Role/status update requires `users.manage` per tests |
| POST | `/api/users/{id}/capabilities` | Grant user capability | Capability guard | `routes\api.php` |  |
| DELETE | `/api/users/{id}/capabilities/{code}` | Revoke user capability | Capability guard | `routes\api.php` |  |
| GET/POST | `/api/practitioners` | List/create practitioners | Capability guard | `routes\api.php` |  |
| PATCH | `/api/practitioners/{id}` | Update practitioner | Capability guard | `routes\api.php` |  |
| POST/DELETE | Practitioner capability routes | Grant/revoke practitioner capabilities | Capability guard | `routes\api.php` | Exact path variants in route file |
| GET | `/api/patients/search` | Search patients | Capability/session guard | `routes\api.php` | Search log table exists |
| POST/PATCH | Patient application routes | Create/submit/review/approve applications | Capability/session guard | `routes\api.php` | Duplicate/requirements gates tested |
| POST/PATCH | Patient access grant routes | Request/approve access | Capability/session guard | `routes\api.php` |  |

### 8. Data Flow and Operational Flow

```text
Browser
  -> Natalium `/auth/login`
  -> PBB Account authorize/callback
  -> Natalium local `users`
  -> local session
  -> user/capability/practitioner/patient/document APIs
  -> MySQL `pbb_natalium`
  -> `storage/documents` for document files/metadata
```

```text
Account admin client
  -> `POST /api/account-admin/users/sync`
  -> AccountAdminGuard token/client validation
  -> Natalium local `users`
  -> audit/capability-protected local operations
```

### 9. Offline-First Behavior

Natalium is local database-backed and stores documents locally, so its registered workflows can operate on the node LAN after installation. New Account SSO login depends on the configured Account base URL and client settings. No Relay outbox, retry queue, conflict handling, service worker, or upstream health-data sync was found in code. Node context reads the local Relay `public\hub.json` file path.

### 10. Integration with Other PBB Apps

| Integration | Direction | Protocol / Method | Purpose | Code Evidence |
|---|---|---|---|---|
| Natalium to PBB Account | Outbound browser/server SSO | Redirect/callback and token/client config | Central login and account claims | `config\app.php`; `routes\api.php`; `bin\test.php` |
| PBB Account admin client to Natalium | Inbound HTTP | `POST /api/account-admin/users/sync` | Server-to-server local user sync | `routes\api.php`; `config\app.php`; `bin\test.php` |
| Natalium to Relay hub JSON | Local file read | `../../relay/public/hub.json` | Node context | `config\app.php`; `routes\api.php` |

### 11. Deployment and Runtime Architecture

Natalium is a Composer PHP app intended for WAMP-style local deployment. `config\app.php` includes MySQL connection defaults, an app path to `database\schema.sql`, local `storage\documents`, and a hardcoded PHP binary path under WAMP PHP 8.2.29. `composer.json` provides `composer test` and `composer migrate` scripts through `bin\test.php` and `bin\migrate.php`. No Docker, PM2, Supervisor, systemd, Laravel scheduler, or queue worker files were confirmed.

### 12. Security and Privacy Notes

| Risk | Severity | Evidence | Suggested Fix |
|---|---|---|---|
| Health/patient registry and document data are sensitive local records | High | `database\schema.sql`; `storage\documents` config | Define retention, backup, encryption-at-rest, and access/audit policy before live health deployment |
| Account admin sync token controls user provisioning | High | `NATALIUM_ACCOUNT_ADMIN_API_TOKEN`; `AccountAdminGuard`; `bin\test.php` | Keep disabled by default, generate per-node secrets, rotate on compromise |
| Account client secret is required for SSO | High | `NATALIUM_ACCOUNT_CLIENT_SECRET`; `config\app.php` | Store only in local env, never commit real values |
| Local DB config uses root/empty password defaults | Medium | `config\app.php` | Use installer-generated DB credentials for shared deployments |
| Document storage path must not leak to clients | Medium | Tests mention document review hides storage path | Keep tests and add download authorization/audit before broad use |

### 13. Realtime Communication

No realtime functionality found / Not confirmed from code.

### 14. Mapping and Geolocation

Patient address storage exists. No map UI, MapServer integration, geocoding, routing, or offline map behavior was confirmed from the scanned Natalium files.

### 15. Background Jobs, Schedulers, and Maintenance Tasks

| Task | Schedule / Trigger | Purpose | Code Evidence |
|---|---|---|---|
| Database migration | Manual CLI | Apply `database\schema.sql` | `bin\migrate.php`; `composer.json` |
| Test runner | Manual CLI | Run custom PHP tests | `bin\test.php`; `composer.json` |

### 16. Configuration and Environment Variables

| Variable / Config | Purpose | Required | Default / Example | Related Module |
|---|---|---|---|---|
| `schema_path` | SQL schema path | Yes | `../database/schema.sql` | Migration |
| `documents_path` | Local document storage path | Yes | `../storage/documents` | Documents |
| `relay_hub_json_path` | Local node context source | Yes | `../../relay/public/hub.json` | Node context |
| `account_base_url` | PBB Account base URL | Yes for SSO | `https://account.pbb.ph` | SSO |
| `account_client_id` | SSO client ID | Yes for SSO | `pbb-natalium` | SSO |
| `NATALIUM_ACCOUNT_CLIENT_SECRET` | SSO client secret | Yes for SSO | Masked | SSO |
| `NATALIUM_ACCOUNT_ADMIN_API_ENABLED` | Enables admin sync API | No; default false | `false` | Account admin sync |
| `NATALIUM_ACCOUNT_ADMIN_API_TOKEN` | Admin sync bearer token | Required if enabled | Masked | Account admin sync |
| `DB_DATABASE` equivalent config | MySQL database | Yes | `pbb_natalium` | Persistence |

### 17. Known Technical Debt and Gaps

| Area | Issue | Evidence | Recommended Next Step |
|---|---|---|---|
| Clinical program engine | Broader health programs are documented/planned, but current reviewed routes focus on identity/registry | `docs\README.md`; `routes\api.php` | Keep program/maternal/child/referral docs marked planned until routes/schema exist |
| Sync/offline conflict handling | No Relay outbox/upstream sync or conflict strategy found | file inventory; `routes\api.php` | Define whether health data remains node-local or syncs through Relay later |
| Account dependency | New SSO login depends on Account service availability | `config\app.php`; `routes\api.php` | Define local/offline login fallback or operator procedure |
| DB credentials | Root/empty local defaults in config | `config\app.php` | Move deployment values to env/installer-generated config |

### 18. Testing Status

Natalium has a custom PHP test runner via `composer test` / `php bin\test.php`. Tests cover SSO/local user linking, Account sync guard behavior, capability enforcement, user/practitioner management, patient application gates, and document review path hiding. Tests were inspected but not run during this documentation update.

### 19. Evidence Summary

Evidence:
- `C:\wamp64\www\pbb\natalium\composer.json`
- `C:\wamp64\www\pbb\natalium\config\app.php`
- `C:\wamp64\www\pbb\natalium\routes\api.php`
- `C:\wamp64\www\pbb\natalium\database\schema.sql`
- `C:\wamp64\www\pbb\natalium\docs\README.md`
- `C:\wamp64\www\pbb\natalium\bin\test.php`
- Chatviewer DB messages from PBB Natalium on 2026-07-06

---

## App: PBB Utility / Vena

### 1. Executive Technical Summary

PBB Utility is the Laravel 12 Vena utility-operator app. It runs locally as a browser-based utility operations surface with admin/operator/command/responder roles, utility assets and teams, settings, MapLibre map configuration, inbound-only Relay incident intake, normalized incident records, quarantine/stale handling, operator missions, and responder mission acknowledgement. Code confirms it consumes Hotline incident snapshots delivered by Relay as `hotline.incident.upserted` messages targeted to `utility.vena`. Chatviewer messages confirm Vena V1 is inbound-only through Relay and should preserve Hotline incident identity/correlation/location/details/resources/media metadata/raw payload.

### 2. Repository Overview

| Area | Details |
|---|---|
| App Name | PBB Utility / Vena |
| Local Path | `C:\wamp64\www\pbb\utility` |
| Main Language | PHP, JavaScript |
| Main Framework | Laravel 12 |
| Frontend Framework | Vite-built Laravel frontend; exact JS framework not fully confirmed |
| Backend Framework | Laravel |
| Database | MySQL `pbb_utility` |
| Realtime Technology | No Realtime integration found / Not confirmed from code |
| Queue / Worker System | Laravel database queue configured in `.env.example`; app-specific queued jobs not confirmed |
| Package Manager | Composer, npm |
| Runtime Requirements | PHP `^8.2`, Node/npm for assets, MySQL |
| Main Entry Points | `artisan`, `routes\web.php`, `routes\api.php`, `resources\js`, `database\migrations` |
| Important Config Files | `.env.example`, `composer.json`, `package.json`, `vite.config.js`, `config\vena.php` |
| Important Environment Variables | `VENA_RELAY_HANDLER_TOKEN`, `VENA_ALERT_LEVEL`, `VENA_ALERT_LEVEL_DESCRIPTION`, `VENA_MAP_SERVER_URL`, `VENA_MAP_VECTOR_TILES`, `VENA_MAP_TERRAIN_TILES`, `VENA_MAP_GLYPHS`, `VENA_MAP_POI_TILES` |
| Deployment Target | Local/utility operations PBB node or utility operator workstation/node |

### 3. App Purpose and PBB Role

Vena receives validated Hotline incidents through Relay and gives a utility operator local tools to view reports beside utility assets/teams, manage incident records, create missions, and let responders acknowledge assigned missions. It depends on Relay for inbound incident delivery and MapServer-compatible tile URLs for maps. Hotline/Relay can send incidents to it, but no other selected app currently depends on Vena for core emergency operation. If Vena is unavailable, utility-specific incident handling and mission coordination are unavailable, but Hotline local emergency intake is not directly blocked.

### 4. User Roles and Permissions

| Role | Purpose | Capabilities | Code Evidence |
|---|---|---|---|
| admin | Configure Vena and manage users/assets/teams/settings | Admin summary, user CRUD, asset/team category initialization, assets, teams, settings | `config\vena.php`; `routes\web.php`; `EnsureUserHasRole` |
| operator | Handle inbound incidents and missions | Operator dashboard, incident details, create/view/update missions | `routes\web.php`; operator controllers |
| command | Command-level web surface | `/command` web surface | `config\vena.php`; `routes\web.php` |
| responder | Field/helper role inside Vena | View assigned missions and accept mission | `routes\web.php`; `Responder\MissionController` |
| Relay handler client | Machine client posting incident envelopes | Bearer-token protected `POST /api/relay/incidents` | `routes\api.php`; `RelayIncidentHandlerController.php` |

### 5. Main Features and Modules

### Relay Incident Intake

Purpose: Receive Hotline incident snapshots from Relay, validate target/message type/version/media refs/source identity, store raw inbound payload, normalize current incident state, and quarantine invalid/stale records.
Main Code: `app\Http\Controllers\Api\RelayIncidentHandlerController.php`.
Database Tables: `vena_relay_inbound_incidents`, `vena_incidents`, `vena_incident_types`, `vena_incident_type_details`, `vena_incident_resources_needed`, `vena_incident_media_refs`.
APIs / Routes: `POST /api/relay/incidents`.
Offline Behavior: Works on local node when Relay can deliver locally; no outbound sync found.
Sync Behavior: Inbound-only Relay contract; stale versions do not overwrite newer normalized incidents.
Related PBB Apps: Hotline, Relay.
Evidence: `routes\api.php`; `config\vena.php`; `tests\Feature\VenaRelayIncidentIntakeTest.php`; Chatviewer Utility/Hotline contract messages.

### Admin Setup, Assets, Teams, And Settings

Purpose: Manage Vena users, utility assets, asset categories, teams, team categories, alert settings, and app setup data.
Main Code: Admin controllers under `app\Http\Controllers\Api\Admin`.
Database Tables: `users`, `vena_settings`, `vena_asset_categories`, `vena_assets`, `vena_team_categories`, `vena_teams`, `vena_activity_logs`.
APIs / Routes: Admin API routes in `routes\web.php`.
Offline Behavior: Local database-backed.
Sync Behavior: No upstream sync found.
Related PBB Apps: MapServer for map context; Helper UI if used by frontend assets.
Evidence: `routes\web.php`; migrations; admin feature tests.

### Operator Dashboard And Missions

Purpose: Let operators review utility incidents and create/manage missions.
Main Code: `app\Http\Controllers\Api\Operator\DashboardController.php`; `Operator\MissionController.php`.
Database Tables: `vena_incidents`, `vena_missions`, `vena_mission_incidents`, `vena_teams`, `vena_activity_logs`.
APIs / Routes: `/api/operator/dashboard`, `/api/operator/incidents/{incident}`, `/api/operator/missions`, mission show/status routes.
Offline Behavior: Local database-backed.
Sync Behavior: No outbound Relay lifecycle sync found in reviewed code.
Related PBB Apps: Hotline/Relay as incident source.
Evidence: `routes\web.php`; operator tests.

### Responder Missions

Purpose: Provide responder role mission lists, mission detail, and mission acceptance.
Main Code: `app\Http\Controllers\Api\Responder\MissionController.php`.
Database Tables: `vena_missions`, `vena_mission_incidents`, `vena_teams`.
APIs / Routes: `/api/responder/missions`, `/api/responder/missions/{mission}`, accept route.
Offline Behavior: Local web app/LAN behavior only; mobile offline sync not implemented in this repo.
Sync Behavior: No upstream sync found.
Related PBB Apps: Planned responder/helper mobile workflow is design context, not confirmed implementation here.
Evidence: `routes\web.php`; responder mission tests.

### Map Configuration

Purpose: Expose MapLibre map configuration and local fallback tile URL settings.
Main Code: `routes\web.php`.
Database Tables: Not applicable.
APIs / Routes: `GET /vena-map.json`.
Offline Behavior: Depends on local MapServer/tile cache URLs configured through env.
Sync Behavior: Not applicable.
Related PBB Apps: PBB MapServer.
Evidence: `routes\web.php`; `.env.example`.

### 6. Database Schema Summary

| Table | Purpose | Important Columns | Relationships / Notes |
|---|---|---|---|
| `users` | Laravel users | name/email/password/role-like fields | Role middleware controls web/API areas |
| `sessions`, `cache`, `jobs` | Laravel runtime tables | framework fields | DB session/queue/cache support |
| `vena_settings` | App settings | key/value settings | Alert/settings surfaces |
| `vena_activity_logs` | Activity/audit log | actor/action/metadata fields | Used by operations/admin flows |
| `vena_asset_categories` | Utility asset categories | category fields | Parent for assets |
| `vena_assets` | Utility assets | category/location/status fields | Used on map/operator context |
| `vena_team_categories` | Team categories | category fields | Parent for teams |
| `vena_teams` | Utility teams | category/status/contact/location fields | Used by missions |
| `vena_relay_inbound_incidents` | Raw Relay intake records | message type, target, status, raw envelope/payload | Quarantine/stale/accepted tracking |
| `vena_incidents` | Normalized current incident records | source identity, status, location, priority, updated/version fields | Upserted from inbound snapshots |
| `vena_incident_types` | Incident type list per incident | incident reference, type fields | Preserves Hotline many-type incidents |
| `vena_incident_type_details` | Per-type details | type reference, detail fields | Child of incident types |
| `vena_incident_resources_needed` | Requested resources | incident reference, resource fields | From Hotline payload |
| `vena_incident_media_refs` | Media reference metadata | incident reference, metadata fields | URL/path fields are rejected/quarantined |
| `vena_missions` | Utility missions | status/team/assignment fields | Created by operators |
| `vena_mission_incidents` | Mission-incident pivot | mission reference, incident reference | Links missions to incidents |

```text
users
  └── vena_activity_logs

vena_asset_categories
  └── vena_assets

vena_team_categories
  └── vena_teams
        └── vena_missions
              └── vena_mission_incidents
                    └── vena_incidents
                          ├── vena_incident_types
                          │     └── vena_incident_type_details
                          ├── vena_incident_resources_needed
                          └── vena_incident_media_refs

vena_relay_inbound_incidents
  └── normalized into vena_incidents
```

### 7. API and Route Inventory

| Method | Path / Endpoint | Purpose | Auth | Handler / File | Notes |
|---|---|---|---|---|---|
| GET | `/api/health` | Health check | Public | `routes\api.php` | Returns app code/status |
| POST | `/api/relay/incidents` | Relay incident intake | Bearer token `VENA_RELAY_HANDLER_TOKEN` | `RelayIncidentHandlerController@store` | Validates `hotline.incident.upserted`, target `utility.vena` |
| GET | `/vena-map.json` | MapLibre config | Web | `routes\web.php` | Uses MapServer env URLs/local fallback |
| GET | `/api/csrf-token` | CSRF token bootstrap | Public | `routes\web.php` |  |
| POST | `/api/login` | Login | Public throttled | Auth controller |  |
| GET | `/api/bootstrap` | App bootstrap | Auth | Bootstrap controller |  |
| GET | `/api/user` | Current user | Auth | User/session controller |  |
| POST | `/api/logout` | Logout | Auth | Auth controller |  |
| GET | `/api/operator/dashboard` | Operator dashboard | `operator`/`admin` roles | Operator dashboard controller |  |
| GET | `/api/operator/incidents/{incident}` | Incident detail | `operator`/`admin` roles | Operator controller |  |
| POST/GET/PATCH | `/api/operator/missions*` | Mission create/show/status | `operator`/`admin` roles | Operator mission controller |  |
| GET/POST | `/api/responder/missions*` | Responder mission list/detail/accept | `responder` role | Responder mission controller |  |
| GET/POST/PATCH/DELETE | `/api/admin/users*` | User administration | `admin` role | Admin user controller |  |
| GET/POST/PATCH/DELETE | `/api/admin/assets*`, `/api/admin/asset-categories*` | Asset setup | `admin` role | Admin asset controllers | Includes initialize routes |
| GET/POST/PATCH/DELETE | `/api/admin/teams*`, `/api/admin/team-categories*` | Team setup | `admin` role | Admin team controllers | Includes initialize routes |
| GET/POST | `/api/admin/settings` | App settings | `admin` role | Admin settings controller |  |
| GET | `/admin`, `/operator`, `/command`, `/responder` | Web role surfaces | Role/session | `routes\web.php` | Browser app entry surfaces |

### 8. Data Flow and Operational Flow

```text
PBB Hotline
  -> Relay envelope `hotline.incident.upserted`
  -> Relay delivery to Vena `POST /api/relay/incidents`
  -> bearer token validation
  -> `vena_relay_inbound_incidents` raw record
  -> target/type/version/source/media validation
  -> `vena_incidents` normalized current state
  -> child type/detail/resource/media metadata rows
  -> operator dashboard
  -> operator mission
  -> responder mission acceptance
```

### 9. Offline-First Behavior

Vena is a local Laravel app with local MySQL storage and can run on the LAN. It is inbound-only for Relay incident messages in V1. It does not require internet for local operator/responder workflows after incidents are delivered locally. Upstream/cloud connectivity is only needed for whatever Relay topology delivers messages from another node or cloud endpoint. No PWA service worker, client-side offline queue, conflict resolver, or outbound Relay sync was confirmed. Stale inbound incident snapshots are detected using the configured version strategy and do not overwrite newer normalized state.

### 10. Integration with Other PBB Apps

| Integration | Direction | Protocol / Method | Purpose | Code Evidence |
|---|---|---|---|---|
| Relay to Vena | Inbound HTTP | `POST /api/relay/incidents` bearer token | Deliver Hotline incident snapshots | `routes\api.php`; `RelayIncidentHandlerController.php`; `config\vena.php` |
| Hotline to Vena through Relay | Indirect | Relay message type `hotline.incident.upserted`, target `utility.vena` | Utility incident handoff | Chatviewer Hotline/Utility messages; `tests\Feature\VenaRelayIncidentIntakeTest.php` |
| Vena to MapServer | Browser HTTP map tile URLs | `GET /vena-map.json` returns MapLibre config/env tile URLs | Local utility maps | `routes\web.php`; `.env.example` |
| Vena to Laravel DB queue/session | Internal DB tables | database session/queue configuration | Runtime state/queue support | `.env.example`; migrations |

### 11. Deployment and Runtime Architecture

Vena is a standard Laravel 12 app. `README.md` documents local development with `php artisan serve --host=127.0.0.1 --port=8016`. `.env.example` configures MySQL database `pbb_utility`, DB sessions, database queue, and `VENA_RELAY_HANDLER_TOKEN`. Composer scripts include setup/dev/test helpers. Frontend assets use Vite/npm. No Docker, PM2, Supervisor, systemd, or Windows service registration was found inside the Utility repo; service lifecycle would be handled externally if installed by Kit Setup later.

### 12. Security and Privacy Notes

| Risk | Severity | Evidence | Suggested Fix |
|---|---|---|---|
| Relay handler token controls incident ingestion | High | `VENA_RELAY_HANDLER_TOKEN`; `RelayIncidentHandlerController.php` | Generate per-node/per-source token and rotate on compromise |
| Raw inbound incident payloads are retained | High | `vena_relay_inbound_incidents`; controller stores raw envelope/payload | Define retention, audit access, and purge policy |
| Media refs must remain metadata-only | High | Controller/test quarantine URL/path fields | Keep rejecting direct file paths/URLs and document media retrieval via proper backend contracts |
| Utility incidents may include location/reporter details | High | incident migrations/controller normalization | Apply role-based access, local encryption/backup policy, and log review |
| DB defaults use local root/empty password placeholders | Medium | `.env.example` | Installer should generate deployment credentials |

### 13. Realtime Communication

No realtime functionality found / Not confirmed from code.

### 14. Mapping and Geolocation

Vena exposes `GET /vena-map.json` for MapLibre configuration. Env variables configure MapServer/base URLs for vector tiles, terrain tiles, glyphs, and POI tiles, with local fallback behavior in the JSON response. Incident, asset, and team tables include map-relevant operational data from migrations/controllers, but exact column-level geolocation details should be read directly from migrations for implementation work.

### 15. Background Jobs, Schedulers, and Maintenance Tasks

| Task | Schedule / Trigger | Purpose | Code Evidence |
|---|---|---|---|
| Laravel queue support | Configured; app-specific jobs not confirmed | Database queue runtime support | `.env.example`; Laravel jobs migration |
| Tests | Manual `php artisan test` / Composer test script | Feature verification | `composer.json`; `tests\Feature` |
| Dev server | Manual | Local development on port 8016 | `README.md` |

### 16. Configuration and Environment Variables

| Variable / Config | Purpose | Required | Default / Example | Related Module |
|---|---|---|---|---|
| `APP_NAME` | Laravel app name | Yes | `Vena` | App |
| `APP_URL` | Local app URL | Yes | `http://127.0.0.1:8016` | App |
| `DB_DATABASE` | MySQL database | Yes | `pbb_utility` | Persistence |
| `QUEUE_CONNECTION` | Queue backend | Yes | `database` | Queue |
| `SESSION_DRIVER` | Session backend | Yes | `database` | Auth/session |
| `VENA_RELAY_HANDLER_TOKEN` | Bearer token for Relay intake | Yes in Relay-connected deployment | Masked | Relay intake |
| `VENA_ALERT_LEVEL` | Local alert level setting | No | env-defined | Settings/bootstrap |
| `VENA_ALERT_LEVEL_DESCRIPTION` | Alert text | No | env-defined | Settings/bootstrap |
| `VENA_MAP_SERVER_URL` | Local MapServer/base URL | No | env-defined | Maps |
| `VENA_MAP_VECTOR_TILES` | Vector tile URL template | No | env-defined | Maps |
| `VENA_MAP_TERRAIN_TILES` | Terrain tile URL template | No | env-defined | Maps |
| `VENA_MAP_GLYPHS` | Glyph URL template | No | env-defined | Maps |
| `VENA_MAP_POI_TILES` | POI tile URL template | No | env-defined | Maps |

### 17. Known Technical Debt and Gaps

| Area | Issue | Evidence | Recommended Next Step |
|---|---|---|---|
| Outbound utility lifecycle sync | No outbound Relay status/update contract found | `routes\api.php`; `routes\web.php` | Define if utility mission/status updates should be sent upstream |
| Mobile offline responder workflow | Web responder mission endpoints exist, but mobile/PWA offline sync not confirmed | `routes\web.php`; responder controller/tests | Keep planned mobile workflow separate until implemented |
| Realtime responder tracking | No Realtime integration found | file inventory/routes | Define whether live team locations/status should use PBB Realtime |
| Map preflight | Map config exists, but no local tile coverage preflight in this repo | `GET /vena-map.json`; `.env.example` | Reuse MapServer/Kit Setup preflight for utility deployments |

### 18. Testing Status

Vena has Laravel feature tests. `VenaRelayIncidentIntakeTest` covers unauthorized intake, wrong message type quarantine, wrong target quarantine, valid incident normalization, stale update protection, missing version quarantine, many incident type/detail preservation, and media URL/path quarantine. Other feature tests cover admin setup, baseline behavior, operator dashboard, operator missions, and responder missions. Tests were inspected but not run during this documentation update.

### 19. Evidence Summary

Evidence:
- `C:\wamp64\www\pbb\utility\README.md`
- `C:\wamp64\www\pbb\utility\composer.json`
- `C:\wamp64\www\pbb\utility\.env.example`
- `C:\wamp64\www\pbb\utility\config\vena.php`
- `C:\wamp64\www\pbb\utility\routes\web.php`
- `C:\wamp64\www\pbb\utility\routes\api.php`
- `C:\wamp64\www\pbb\utility\app\Http\Controllers\Api\RelayIncidentHandlerController.php`
- `C:\wamp64\www\pbb\utility\database\migrations`
- `C:\wamp64\www\pbb\utility\tests\Feature\VenaRelayIncidentIntakeTest.php`
- Chatviewer DB messages from PBB Utility and Hotline Beta on 2026-07-05

---

## App: PBB Account

### 1. Executive Technical Summary

PBB Account is a Laravel 12 local-node identity and SSO service for Project Bantay Bayan. Code confirms canonical account records keyed by `pbb_user_id`, email/mobile/password login, public registration, account profile/password updates, OAuth-style trusted-client authorization code flow, SSO logout, trusted client administration, app-admin provisioning hooks, account identity resolve/update APIs, session identity APIs for allowed `.pbb.ph` origins, optional Realtime admission, audit events, and a PHP SDK for consuming apps. It is implemented infrastructure-level identity service code.

### 2. Repository Overview

| Area | Details |
|---|---|
| App Name | PBB Account / Account Service |
| Local Path | `C:\wamp64\www\pbb\account` |
| Main Language | PHP, JavaScript |
| Main Framework | Laravel 12 |
| Frontend Framework | Vite/Laravel frontend; Helper assets vendored under `public\vendor\helpers.pbb.ph` |
| Backend Framework | Laravel |
| Database | MySQL `pbb_accounts` |
| Realtime Technology | Optional PBB Realtime admission and account event publishing |
| Queue / Worker System | Laravel database queue configured; no app-specific queue job inventory confirmed |
| Package Manager | Composer, npm |
| Runtime Requirements | PHP `^8.2`, MySQL, Node/npm for assets |
| Main Entry Points | `artisan`, `routes\web.php`, `routes\api.php`, `public\openapi\account.yaml`, `sdk\php\pbb_account_sdk.php` |
| Important Config Files | `.env.example`, `composer.json`, `package.json`, `config\account.php`, `config\session.php`, `database\migrations`, `database\seeders\DatabaseSeeder.php` |
| Important Environment Variables | `ACCOUNT_BASE_URL`, `ACCOUNT_SSO_CODE_TTL_SECONDS`, `ACCOUNT_NODE_HUB_JSON_URL`, `ACCOUNT_SESSION_IDENTITY_ALLOWED_ORIGIN_SUFFIX`, `ACCOUNT_REALTIME_ENABLED`, `ACCOUNT_REALTIME_WEBSOCKET_URL`, `ACCOUNT_REALTIME_TOKEN_SIGNING_SECRET`, `ACCOUNT_APP_ADMIN_VERIFY_TLS` |
| Deployment Target | Local/node identity service at `https://account.pbb.ph` per README and `.env.example` |

### 3. App Purpose and PBB Role

PBB Account is the implemented canonical identity and credential authority for local PBB apps. It centralizes account credentials, status, profile identity, trusted clients, SSO authorization codes, session identity, and audit events. Apps integrate through browser redirects to `/oauth/authorize`, server-side `/oauth/token` exchange, trusted-client identity APIs, and optional app-admin provisioning hooks. Code now confirms Account adoption points in Chat, Hotline, Landing, Natalium, Relay, Support, Realtime, and Maestro. These apps keep app-local sessions/domain roles while linking users by `pbb_user_id`.

### 4. User Roles and Permissions

| Role | Purpose | Capabilities | Code Evidence |
|---|---|---|---|
| account user | Canonical PBB identity holder | Login/register, view/update profile, change password, use SSO with trusted clients | `Account.php`; `AuthController.php`; `UserController.php`; `routes\api.php` |
| account admin | Account service administrator | Manage accounts, trusted clients, client secrets, app-admin provisioning hooks, audit events, account settings | `Account.php`; `AdminController.php`; `routes\api.php`; `AdminSurfaceApiTest.php` |
| trusted client app | Registered PBB consuming app | OAuth-style code exchange, account identity resolve/update, app-local session creation | `TrustedClient.php`; `OAuthController.php`; `AccountIdentityController.php` |
| app-admin API target | External app admin surface linked to trusted client | Metadata/user lookup/provision/role/status operations through Account admin surface | `AdminController.php`; `AppAdminApiClient.php`; `TrustedClient.php` |

Account roles are `admin` and `user`. Account status values found in the model are `active`, `pending`, `suspended`, and `disabled`; login/SSO rejects non-active accounts.

### 5. Main Features and Modules

### Central Identity And Credentials

Purpose: Own canonical `pbb_user_id`, name, email, mobile, password hash, status, role, avatar URL, verification timestamps, login time, and audit events.
Main Code: `Account.php`; `AuthController.php`; `UserController.php`; `AccountIdentityController.php`.
Database Tables: `accounts`, `account_audit_events`, `account_settings`.
APIs / Routes: `/api/login`, `/api/register`, `/api/logout`, `/api/user`, `/api/account/me`, `/api/user/password`, `/api/account-identities/resolve`, `/api/account-identities/{pbbUserId}`.
Offline Behavior: Local database-backed when Account is running on the node.
Sync Behavior: Trusted clients can resolve and update Account-owned identity fields; no Relay sync found.
Related PBB Apps: Chat, Hotline, Landing, Natalium, future apps.
Evidence: `routes\api.php`; `AuthController.php`; `AccountIdentityController.php`; migrations; tests.

### Browser SSO

Purpose: Let apps redirect users to Account Service, exchange short-lived one-time authorization codes server-to-server, and create app-local sessions.
Main Code: `OAuthController.php`; `TrustedClient.php`; `SsoAuthorizationCode.php`.
Database Tables: `sso_authorization_codes`, `trusted_clients`, `account_audit_events`.
APIs / Routes: `GET /oauth/authorize`, `POST /oauth/token`, `GET /oauth/logout`.
Offline Behavior: Works locally when Account and consuming app endpoints are reachable on the node/LAN.
Sync Behavior: App-local sessions remain separate; identity claims come from Account Service token exchange.
Related PBB Apps: Seeded clients include `pbb-chat`, `pbb-hotline`, and `pbb-landing`.
Evidence: `routes\web.php`; `OAuthController.php`; `DatabaseSeeder.php`; `OAuthClientUsageTest.php`.

### Admin Surface And Trusted Clients

Purpose: Let Account admins manage accounts, trusted clients, client secret rotation, client enable/disable, app-admin metadata, app user provisioning, app user role/status, and audit logs.
Main Code: `AdminController.php`; `AppAdminApiClient.php`; `TrustedClient.php`.
Database Tables: `accounts`, `trusted_clients`, `account_audit_events`.
APIs / Routes: `/api/admin/accounts`, `/api/admin/trusted-clients`, rotate/enable/disable routes, app-admin metadata/user/provision/role/status routes, `/api/admin/audit-events`.
Offline Behavior: Local Account admin workflow when target app-admin endpoints are reachable locally.
Sync Behavior: App-admin hooks call trusted client app endpoints over HTTP using configured app-admin base URL/token.
Related PBB Apps: Chat, Hotline, Landing, Natalium, Relay, Support, Realtime, Maestro, and other apps with account-admin endpoints.
Evidence: `AdminController.php`; `AppAdminApiClient.php`; `AdminSurfaceApiTest.php`.

### Session Identity And Realtime Admission

Purpose: Expose current Account session identity to allowed `.pbb.ph` origins and optionally provide Realtime admission tokens for account/browser sessions.
Main Code: `SessionIdentityController.php`; `RealtimeAdmissionController.php`; `AccountRealtimeAdmissionService.php`; `AccountRealtimeEventPublisher.php`.
Database Tables: `account_settings`; Realtime events use configured Realtime path when enabled.
APIs / Routes: `GET|OPTIONS /api/session/identity`, `GET|OPTIONS /api/session/realtime-admission`.
Offline Behavior: Local/LAN when Account and Realtime are local and configured.
Sync Behavior: No Relay sync found.
Related PBB Apps: Landing, Chat, Realtime, apps needing shared session identity.
Evidence: `config\account.php`; `routes\api.php`; `AccountIdentityApiTest.php`; `OAuthClientUsageTest.php`.

### 6. Database Schema Summary

| Table | Purpose | Important Columns | Relationships / Notes |
|---|---|---|---|
| `accounts` | Canonical Account users | `pbb_user_id`, `name`, `email`, `mobile`, `status`, `role`, verification timestamps, `avatar_url`, `password_hash`, `last_login_at`, `remember_token` | Auth model is `Account`; status/role checked in code |
| `trusted_clients` | Registered SSO/client apps | `client_id`, `client_secret_hash`, allowed redirect/logout URIs, scopes, `status`, `last_used_at`, app-admin config fields | Client secrets are hashed; app-admin API token is encrypted cast |
| `sso_authorization_codes` | One-time authorization code records | `code_hash`, `pbb_user_id`, `account_session_id`, `client_id`, `redirect_uri`, scopes, expiry, consumed timestamp | Codes are stored hashed and consumed under DB transaction |
| `account_audit_events` | Identity/security audit | `pbb_user_id`, `actor_pbb_user_id`, `client_id`, `event_type`, IP, user-agent, metadata, created time | Used by login/logout/SSO/admin/identity events |
| `account_settings` | Runtime settings | `key`, JSON `value` | Includes Realtime settings and configurable Account behavior |
| `sessions` | Laravel DB sessions | session id, user id, IP, user agent, payload, last activity | `SESSION_DRIVER=database` |
| `password_reset_tokens` | Laravel password reset token storage | email, token, created time | No reset routes confirmed in current route scan |
| `cache`, `cache_locks` | Laravel cache tables | framework fields | Runtime cache |
| `jobs`, `job_batches`, `failed_jobs` | Laravel queue tables | framework fields | Queue configured; app-specific jobs not confirmed |

```text
accounts
  ├── sso_authorization_codes
  └── account_audit_events

trusted_clients
  └── sso_authorization_codes
  └── app-admin HTTP calls to app-local admin APIs

app-local users
  └── pbb_user_id -> accounts.pbb_user_id
```

### 7. API and Route Inventory

| Method | Path / Endpoint | Purpose | Auth | Handler / File | Notes |
|---|---|---|---|---|---|
| GET | `/` `/profile` `/accounts` `/clients` `/audit` `/sdk` | SPA/app surfaces | Browser/session | `routes\web.php` | Returns `resources\views\app.blade.php` |
| GET | `/up` | Health check | Public | `routes\web.php` | Returns app/status/local_only |
| GET | `/api/docs` | OpenAPI YAML | Public | `routes\web.php` | Serves `public\openapi\account.yaml` |
| GET | `/oauth/authorize` | Browser SSO authorization start | Account session; unauthenticated users see login app | `OAuthController@authorize` | Validates active trusted client and exact redirect URI |
| POST | `/oauth/token` | Authorization code exchange | Trusted client ID/secret | `OAuthController@token` | Code is hashed, one-time, expiring, redirect-bound |
| GET | `/oauth/logout` | SSO logout redirect | Account session/client/logout URI | `OAuthController@logout` | Validates post-logout redirect URI |
| GET | `/api/bootstrap` | App bootstrap | Public/session-aware | `BootstrapController` | Returns auth/config data |
| POST | `/api/login` | Login | Public, throttled | `AuthController@login` | Email/mobile/identifier plus password |
| POST | `/api/register` | Public registration | Public, throttled | `AuthController@register` | Supports avatar upload |
| POST | `/api/logout` | API logout | Auth | `AuthController@logout` | Clears Account session |
| GET | `/api/session/ping` | Session heartbeat | Auth | `SessionController` |  |
| GET/OPTIONS | `/api/session/identity` | Cross-app session identity | CORS-limited origin | `SessionIdentityController` | Allows `.pbb.ph` origin suffix by config |
| GET/OPTIONS | `/api/session/realtime-admission` | Account Realtime admission | CORS-limited origin | `RealtimeAdmissionController` | Guest/authenticated admission; disabled when not configured |
| GET/POST | `/api/user`, `/api/account/me` | Current account profile get/update | Auth | `UserController` | `/api/account/me` aliases current user |
| POST | `/api/user/password` | Change password | Auth | `UserController@password` |  |
| POST | `/api/settings` | Update Account settings | Auth | `AccountSettingController@update` | Admin checks should be reviewed in controller |
| POST | `/api/account-identities/resolve` | Trusted client identity lookup | Client ID/secret | `AccountIdentityController@resolve` | Up to 100 `pbb_user_ids` |
| PATCH | `/api/account-identities/{pbbUserId}` | Trusted client updates Account-owned identity fields | Client ID/secret | `AccountIdentityController@update` | Only name/email/mobile accepted; optional Realtime broadcast |
| GET/POST | `/api/admin/accounts*` | Account admin CRUD/update | Auth + admin assertion | `AdminController` |  |
| GET/POST | `/api/admin/trusted-clients*` | Trusted client CRUD/lifecycle | Auth + admin assertion | `AdminController` | Includes rotate-secret/enable/disable |
| GET/PUT/DELETE/PATCH | `/api/admin/trusted-clients/{client}/app-admin/*` | App-local admin provisioning bridge | Auth + admin assertion | `AdminController`; `AppAdminApiClient` | Calls configured app admin base URL/token |
| GET | `/api/admin/audit-events` | Recent audit events | Auth + admin assertion | `AdminController@auditEvents` | Latest 50 |

### 8. Data Flow and Operational Flow

SSO login flow:

```text
Consuming app
-> Account `/oauth/authorize?client_id=...&redirect_uri=...&response_type=code`
-> Account login/register surface if no session
-> Account validates trusted client + exact redirect URI
-> Account creates hashed `sso_authorization_codes` row
-> browser redirects to consuming app callback with one-time code
-> consuming app server posts `/oauth/token` with client secret
-> Account locks/consumes code and returns identity payload + account_session_id
-> consuming app creates app-local session/user linked by `pbb_user_id`
```

Trusted identity flow:

```text
Trusted app backend
-> `/api/account-identities/resolve` with client id/secret
-> Account returns canonical profile/status for requested `pbb_user_ids`
-> trusted app can update Account-owned identity fields through PATCH
-> optional Realtime account-session broadcast on profile update
```

### 9. Offline-First Behavior

PBB Account is local database-backed and can run on the node/LAN. Apps that depend on Account for new SSO login require Account availability. Existing app-local sessions in consuming apps are separate and depend on each app's implementation. No Relay sync, durable offline login cache, conflict handling, or browser service-worker/PWA behavior was confirmed in Account code.

### 10. Integration with Other PBB Apps

| Integration | Direction | Protocol / Method | Purpose | Code Evidence |
|---|---|---|---|---|
| Chat/Hotline/Landing and other trusted apps -> Account | Browser redirect + server-side token exchange | SSO login and app-local session creation | `routes\web.php`; `OAuthController.php`; `DatabaseSeeder.php`; `OAuthClientUsageTest.php` |
| Trusted apps -> Account | HTTP with `X-PBB-Account-Client-Id` and `X-PBB-Account-Client-Secret` | Resolve/update canonical account identity fields | `AccountIdentityController.php`; `AccountIdentityApiTest.php` |
| Account -> app-admin endpoints | HTTP bearer token | Probe app metadata, provision app users, update app role/status | `AdminController.php`; `AppAdminApiClient.php`; `TrustedClient.php`; `AdminSurfaceApiTest.php` |
| Account -> Realtime | Backend SDK/event publisher when enabled | Account login/logout/profile events and admission tokens | `AccountRealtimeAdmissionService.php`; `AccountRealtimeEventPublisher.php`; `vendor_pbb\realtime-sdk` |
| Account -> Hub/Relay hub JSON | HTTP configured URL | Node identity lookup/config context | `config\account.php`; `ACCOUNT_NODE_HUB_JSON_URL` |
| Hotline/Relay/Chat/Support/Realtime/Maestro -> Account | Browser redirect + callback + token exchange | App-local login through canonical Account identity | app `routes\web.php`; app `AccountSsoController`; app Account SSO tests |
| Account -> Hotline/Relay/Chat/Support/Realtime/Maestro app-admin APIs | HTTP bearer token + `X-PBB-Account-Client` | App-local user provisioning, role/status update, access removal | app `AccountAdminController`; app `VerifyAccountAdminService`; app Account admin tests |

### 11. Deployment and Runtime Architecture

Account is a Laravel 12 app under WAMP. README instructs using `C:\wamp64\bin\php\php8.2.29\php.exe` for `artisan migrate`, `artisan db:seed`, and `artisan test` because default `php` may point to PHP 5.6. `.env.example` uses `APP_URL=https://account.pbb.ph`, MySQL database `pbb_accounts`, DB sessions, DB queue, and `SESSION_COOKIE=pbb_account_session`. Composer scripts include setup/dev/test; npm/Vite build frontend assets. No Docker/systemd/PM2 files were found.

### 12. Security and Privacy Notes

| Risk | Severity | Evidence | Suggested Fix |
|---|---|---|---|
| Central credential store is high-value target | High | `accounts.password_hash`; login/register routes | Define backup/encryption/retention policy; restrict admin access; monitor audit events |
| Seeded development credentials and client secrets exist | High if reused outside dev | `README.md`; `DatabaseSeeder.php` | Rotate seeded test account password and `*-dev-secret` client secrets before shared deployment |
| Authorization-code misuse | High | `OAuthController.php`; `sso_authorization_codes` migration | Keep codes short-lived, one-time, hashed, and exact client/redirect-bound |
| Client secret leakage | High | `trusted_clients.client_secret_hash`; `rotateTrustedClientSecret` | Continue hashing client secrets and rotating through admin surface |
| App-admin API token exposure | High | `TrustedClient` encrypted cast; `AdminController` masks token in tests | Keep token encrypted/masked and rotate per app |
| Redirect URI abuse | High | `TrustedClient::allowsRedirectUri`; `allowsPostLogoutRedirectUri` | Keep exact pre-registered redirect/logout URI checks |
| Admin route authorization depends on controller assertion | Medium | Admin routes are inside `auth`; `AdminController::assertAdmin` | Keep tests for non-admin denial and consider explicit middleware |
| Realtime signing secret controls admission tokens | High when Realtime enabled | `config\account.php`; `ACCOUNT_REALTIME_TOKEN_SIGNING_SECRET` | Generate strong per-node secret and rotate on compromise |

### 13. Realtime Communication

Account has optional Realtime integration. `GET /api/session/realtime-admission` returns disabled/not configured when Realtime is off, and guest or authenticated admission payloads when enabled. Services publish login/logout/profile update events to Realtime when configured. Code evidence: `RealtimeAdmissionController.php`, `AccountRealtimeAdmissionService.php`, `AccountRealtimeEventPublisher.php`, `config\account.php`, and `OAuthClientUsageTest.php`.

### 14. Mapping and Geolocation

No mapping/geolocation functionality found / Not confirmed from code.

### 15. Background Jobs, Schedulers, and Maintenance Tasks

| Task | Schedule / Trigger | Purpose | Code Evidence |
|---|---|---|---|
| Database migration | Manual / install | Create Account, trusted client, SSO, audit, settings, session/cache/queue tables | `database\migrations`; README |
| Database seed | Manual / dev install | Seed test admin account and trusted clients `pbb-chat`, `pbb-hotline`, `pbb-landing` | `database\seeders\DatabaseSeeder.php`; README |
| Laravel queue listener | Dev script | Database queue listener in development | `composer.json` `dev` script |
| Tests | Manual | Feature/unit verification | `composer.json`; `tests\Feature`; `tests\Unit` |

### 16. Configuration and Environment Variables

| Variable / Config | Purpose | Required | Default / Example | Related Module |
|---|---|---|---|---|
| `APP_URL` | Account base URL | Yes | `https://account.pbb.ph` | SSO |
| `DB_DATABASE` | MySQL database | Yes | `pbb_accounts` | Persistence |
| `SESSION_DRIVER` | Session backend | Yes | `database` | Auth/session |
| `SESSION_COOKIE` | Account session cookie | Yes | `pbb_account_session` | Auth/session |
| `ACCOUNT_BASE_URL` | Account service base URL | Yes | `https://account.pbb.ph` | SDK/SSO |
| `ACCOUNT_SSO_CODE_TTL_SECONDS` | Authorization code TTL | Yes | `300` | OAuth |
| `ACCOUNT_NODE_HUB_JSON_URL` | Node identity hub JSON URL | No | `http://relay.pbb.ph/hub.json` | Node context |
| `ACCOUNT_SESSION_IDENTITY_ALLOWED_ORIGIN_SUFFIX` | CORS origin suffix for session identity | Yes | `.pbb.ph` | Session identity |
| `ACCOUNT_APP_ADMIN_VERIFY_TLS` | Verify TLS for app-admin calls | Yes | false in local env | App-admin bridge |
| `ACCOUNT_REALTIME_ENABLED` | Enable Account Realtime admission/events | No | `false` | Realtime |
| `ACCOUNT_REALTIME_WEBSOCKET_URL` | Realtime WebSocket URL | If enabled | `wss://realtime.pbb.ph/realtime` | Realtime |
| `ACCOUNT_REALTIME_TOKEN_SIGNING_SECRET` | Realtime token signing secret | If enabled | Masked/empty | Realtime |
| `ACCOUNT_REALTIME_ALLOWED_ORIGIN_SUFFIX` | Realtime CORS origin suffix | If enabled | `.pbb.ph` | Realtime |

### 17. Known Technical Debt and Gaps

| Area | Issue | Evidence | Recommended Next Step |
|---|---|---|---|
| Deployment credentials | README documents seeded test account and dev client secrets | `README.md`; `DatabaseSeeder.php` | Rotate or disable seeded credentials before shared deployment |
| Offline login policy | Account is local, but no durable offline identity cache for consuming apps was confirmed | routes/services scan | Define app behavior when Account is down |
| Admin middleware | Admin routes rely on controller `assertAdmin` inside authenticated route group | `routes\api.php`; `AdminController.php` | Add/keep explicit non-admin tests or middleware |
| App adoption | Several consuming apps now have Account SSO/app-admin code, but defaults and enablement differ by app | Hotline/Relay/Chat/Support/Realtime/Maestro Account integration routes/config/tests | Track per-app enablement, trusted client registration, callback URLs, and app-admin token rotation |
| Realtime optionality | Realtime admission/events are optional and disabled by default | `.env.example`; tests | Document enablement and shared secret rotation |

### 18. Testing Status

Account has PHPUnit/Laravel tests. Feature tests cover OAuth client usage, token exchange, authorize login resume, remembered login/bootstrap, public registration/avatar upload, SSO logout, API logout, session identity CORS, Realtime admission disabled/guest behavior, account identity resolve/update, Landing trusted client seeding, admin trusted-client lifecycle, app-admin metadata calls, and admin audit surface. Unit tests cover the PHP Account SDK. Tests were inspected but not run during this documentation correction.

### 19. Evidence Summary

Evidence:
- `C:\wamp64\www\pbb\account\README.md`
- `C:\wamp64\www\pbb\account\composer.json`
- `C:\wamp64\www\pbb\account\.env.example`
- `C:\wamp64\www\pbb\account\config\account.php`
- `C:\wamp64\www\pbb\account\routes\web.php`
- `C:\wamp64\www\pbb\account\routes\api.php`
- `C:\wamp64\www\pbb\account\app\Http\Controllers\OAuthController.php`
- `C:\wamp64\www\pbb\account\app\Http\Controllers\Api\AuthController.php`
- `C:\wamp64\www\pbb\account\app\Http\Controllers\Api\AdminController.php`
- `C:\wamp64\www\pbb\account\app\Http\Controllers\Api\AccountIdentityController.php`
- `C:\wamp64\www\pbb\account\app\Http\Controllers\Api\RealtimeAdmissionController.php`
- `C:\wamp64\www\pbb\account\app\Models\Account.php`
- `C:\wamp64\www\pbb\account\app\Models\TrustedClient.php`
- `C:\wamp64\www\pbb\account\database\migrations`
- `C:\wamp64\www\pbb\account\database\seeders\DatabaseSeeder.php`
- `C:\wamp64\www\pbb\account\sdk\php`
- `C:\wamp64\www\pbb\account\tests\Feature\OAuthClientUsageTest.php`
- `C:\wamp64\www\pbb\account\tests\Feature\AccountIdentityApiTest.php`
- `C:\wamp64\www\pbb\account\tests\Feature\AdminSurfaceApiTest.php`

---

## App: PBB Salus

### 1. Executive Technical Summary

PBB Salus is an in-progress Laravel 12 implementation of the Auxilus Mos module for local evacuation-center and relief-distribution operations. Local code now confirms a Laravel scaffold with routes, controllers, models, migrations, services, middleware, tests, frontend assets, and OpenAPI tooling. Implemented surfaces cover the V1 local barangay-node model: multiple evacuation centers per barangay, center activations, express and regular evacuee registration, individual and family recipient units, worker validation, tag/QR issuance, optional individual entry/exit tracking or aggregate gate counting, occupancy/capacity reporting, scheduled relief batches, receipt tracking, citizen QR surfaces, Realtime change notifications, Account SSO/app-admin integration, Landing launcher metadata, Kit Setup-facing health/readiness expectations, and Hotline/Vox aggregate-summary access.

### 2. Repository Overview

| Area | Details |
|---|---|
| App Name | PBB Salus |
| Local Path | `C:\wamp64\www\pbb\salus` |
| Product Family | Auxilus Mos |
| Product Role | Evacuation-center and relief-distribution operations |
| Implementation Status | In progress; Laravel foundation and operational APIs implemented through milestone 19 per checklist; release/installation not started |
| Target Main Framework | Laravel / PBB app standards baseline |
| Target Database | MySQL/MariaDB, proposed `pbb_salus` |
| Realtime Technology | PBB Realtime change notifications; database/REST remains authoritative |
| Queue / Worker System | V1 declares no persistent Salus queue worker, scheduler, or Maestro telemetry |
| Primary Docs | `docs\development-proposal.md`, `docs\v1-scope.md`, `docs\domain-database-design.md`, `docs\api-contract-design.md`, `docs\integration-design.md`, `docs\openapi\salus-v1.yaml` |
| Deployment Target | Local barangay PBB node over wired Ethernet / barangay Wi-Fi |

### 3. App Purpose and PBB Role

Salus fills the evacuation-center operations gap between emergency intake and operational reporting. It is intended to run locally at a barangay PBB node and remain usable without internet connectivity. Its V1 boundary keeps person-level evacuation records local while exposing privacy-safe aggregate operational summaries to Hotline/Vox on demand for SITREP use.

The design explicitly avoids direct V1 Relay, Support/Imperium, MapServer, and Maestro dependencies. Hotline remains the caller for aggregate Salus summaries and decides how those metrics are displayed, refreshed, revised, or included in Hotline SITREPs/support workflows.

### 4. User Roles and Permissions

| Role | Purpose | Capabilities |
|---|---|---|
| admin | App configuration and controls | Reference data, access eligibility, templates, archive/backup/purge controls |
| operator | Center and relief operations coordination | Center activation/closure, zones, capacity, worker assignment, relief-batch lifecycle, monitoring |
| worker | Field/center station work | Capability-scoped registration, validation, printing, gate tracking, relief release, corrections |
| command | Situational awareness | Primarily read-only dashboards and aggregate reporting |
| citizen | Limited self-service | Express registration and QR-based center announcements / relief status |

Worker capabilities are center-activation-scoped by default. Seeded station bundles include registration encoder, registration validator, tagging/printing, entrance/exit tracking, aggregate gate counter, relief release, receipt-correction approval, and occupancy reconciliation.

### 5. Main Features and Modules

### Multiple Evacuation Centers

Purpose: Manage one barangay-wide evacuation operation with multiple reusable center activations.
Main Design: `docs\v1-scope.md`, `docs\domain-database-design.md`, `docs\movement-design.md`, `docs\occupancy-capacity-design.md`.
Database Tables: `evacuation_operations`, `evacuation_centers`, `center_activations`, `center_zone_templates`, `activation_zones`, `activation_capacity_versions`.
Offline Behavior: Local LAN operation over the barangay node; internet loss should not interrupt core operations.
Integration: Account, Realtime, Landing, Kit Setup; Hotline aggregate summary pull.

### Registration, Validation, and Tagging

Purpose: Register individual or family/household recipient units through express QR or worker-encoded regular lanes.
Main Design: `docs\registration-design.md`, `docs\tagging-design.md`, `docs\domain-database-design.md`.
Database Tables: `people`, `person_photos`, `registrations`, `registration_members`, `registration_versions`, `evacuee_tags`, `print_jobs`.
Offline Behavior: Local operation with network printer diagnostics where available; handwritten evacuee ID fallback when printing fails.
Privacy Boundary: QR values are opaque and contain no personal information.

### Entry, Exit, Occupancy, and Capacity

Purpose: Track official center presence either by individual `in`/`out` boundary events or aggregate gate counts.
Main Design: `docs\movement-design.md`, `docs\occupancy-capacity-design.md`.
Database Tables: `tracking_periods`, `boundary_events`, `aggregate_movement_events`, `occupancy_reconciliations`.
Offline Behavior: Local server timestamps are authoritative; workstation disconnects must not claim uncommitted success.
Notes: Capacity is advisory and must not block entry. Geofencing and MapServer-based map rendering are deferred beyond V1.

### Relief Scheduling and Receipt

Purpose: Coordinate simple first-come, first-served relief-distribution batches and track receipt by individual or household recipient unit.
Main Design: `docs\relief-release-design.md`, `docs\v1-scope.md`.
Database Tables: `relief_batches`, `relief_receipts`, `relief_receipt_corrections`.
Offline Behavior: Local transactions protect receipt confirmation and package counter decrement.
V1 Boundary: Detailed relief-goods inventory, receiving, stock lots, storage locations, reservations, transfers, spoilage, loss, procurement, and accounting are deferred beyond V1.

### Citizen Surface

Purpose: Provide public center status/announcements and limited possession-based QR access for applicable relief schedules and received marks.
Main Design: `docs\privacy-access-design.md`, `docs\api-contract-design.md`.
Privacy Boundary: Citizen QR access does not expose registration details, household lists, photos, assistance information, movement history, missing-person information, or worker notes.
Integration: Account can support express registration, but the issued evacuee QR limited surface remains independent of Account after validation.

### Aggregate Operational Summary for Hotline/Vox

Purpose: Let Hotline request a privacy-safe Salus aggregate summary for SITREP use.
Main Design: `docs\operational-summary-design.md`, `docs\integration-design.md`, `docs\openapi\salus-v1.yaml`.
API: Candidate `GET /api/internal/sitrep-summary` with static per-node bearer secret.
Summary Includes: Center status/capacity/occupancy/tracking mode, registration counts, local/non-local counts, missing/located/unresolved counts, reportable assistance-category counts, relief distribution totals, freshness/completeness metadata.
Summary Excludes: Names, identifiers, photos, birthdates, household membership, individual movement history, assistance notes, missing-person identity, and worker notes.

### 6. Database Schema Summary

The design proposes ULID primary keys and append-only/versioned history for operational facts. Major groups:

| Group | Tables / Concepts |
|---|---|
| Node and centers | `nodes`, `evacuation_centers`, `evacuation_operations`, `center_activations`, zone templates/versions |
| People and registration | `people`, `person_photos`, `registrations`, `registration_members`, `registration_versions`, assistance/cancellation/reference tables |
| Missing persons | `missing_reports` |
| Tags and printing | `evacuee_tags`, `network_printers`, `print_templates`, `print_jobs` |
| Movement and occupancy | `tracking_periods`, `boundary_events`, `aggregate_movement_events`, `occupancy_reconciliations`, capacity history |
| Relief receipt | `relief_batches`, `relief_receipts`, `relief_receipt_corrections` |
| Authorization and audit | `users`, roles/capabilities/grants, `audit_events` |
| Integration and maintenance | `integration_settings`, `hotline_api_clients`, `summary_snapshots`, `manual_backups`, `purge_requests`, diagnostics |

### 7. API and Route Inventory

The detailed API inventory is version-controlled in `docs\api-contract-design.md` and `docs\openapi\salus-v1.yaml`. Local `route:list` confirms 113 routes, and `npm run openapi:check` synchronized and validated 87 implemented API paths. Confirmed implemented/contract surfaces include:

| Surface | Purpose |
|---|---|
| `/api/bootstrap` | Standard PBB app bootstrap/session/data contract |
| `/api/realtime/session` | Salus-authorized Realtime browser admission |
| `/api/internal/sitrep-summary` | Hotline/Vox aggregate summary pull |
| `/api/account-admin/*` | Account app-admin metadata, provision, role/status, and access-removal contract |
| `/up`, `/api/health`, `/api/ready` | Liveness, health, and readiness for Kit Setup/Landing |

### 8. Data Flow and Operational Flow

```text
Center devices / worker stations / citizen QR scans
-> PBB Salus on the local barangay node
-> local Salus database and protected photo storage
-> optional LAN printers / scanners / cameras
-> PBB Realtime change notifications
-> surfaces refetch authorized Salus REST state

Hotline/Vox
-> authenticated on-demand request to Salus aggregate summary API
-> Hotline decides SITREP/support use of returned aggregate metrics
```

### 9. Offline-First Behavior

Salus V1 is designed for LAN/local-node operation. Internet loss should have no operational effect. Existing Salus-local sessions continue until normal expiration during temporary Account outage, while new SSO sessions are unavailable until Account recovers. Realtime outage degrades live updates but does not block core HTTP operations. Printer failure falls back to handwritten IDs, scanner failure to manual ID entry, and camera failure requires another capture device before validation.

### 10. Integration with Other PBB Apps

| Integration | Direction | Protocol / Method | Purpose | Evidence |
|---|---|---|---|---|
| Salus -> Account | Browser/server SSO | OAuth-style authorize/callback/token exchange | App login and `pbb_user_id` linking | `docs\integration-design.md` |
| Account -> Salus | HTTP app-admin API | Bearer token + `X-PBB-Account-Client: pbb-account` | App-local user provision/role/status/access management | `docs\integration-design.md` |
| Salus -> Realtime | HTTP backend publish + browser admission | Dedicated Salus client/projects/rooms | Live change notifications followed by REST refetch | `docs\integration-design.md` |
| Hotline/Vox -> Salus | HTTP internal API | Bearer-protected summary pull | Aggregate evacuation/relief metrics for Hotline SITREP use | `docs\integration-design.md`; `docs\operational-summary-design.md` |
| Kit Setup -> Salus | App installer/Data Prep contracts | Bundle install, config, checks, Account/Realtime/Hotline settings | Local deployment and repair/update support | `docs\integration-design.md` |
| Landing -> Salus | Local launcher registry | `pbb.ph` local launcher record | App launch and health badge | `docs\integration-design.md` |

### 11. Deployment and Runtime Architecture

Salus V1 targets a production-pruned offline Laravel bundle with prebuilt frontend assets and production Composer dependencies included. Kit Setup is expected to handle WAMP/PHP/MySQL prerequisites, DNS/vhost/HTTPS/cert setup, database credentials, Account trusted-client seeding, Realtime Data Prep, Landing registry sync, and smoke checks. Salus V1 declares no persistent app-owned queue worker, scheduler, or automatic scheduled task.

### 12. Security and Privacy Notes

| Risk | Severity | Evidence | Suggested Fix |
|---|---|---|---|
| Person-level evacuee records/photos/movement history are sensitive | High | `privacy-access-design.md`; `domain-database-design.md` | Keep local-only by default, store photos outside public web root, enforce role/capability checks |
| Citizen QR possession grants limited access | High | `tagging-design.md`; `privacy-access-design.md` | Use opaque high-entropy hashed tokens, expose only schedules/received marks/announcements |
| Account app-admin token controls local user access | High | `integration-design.md` | Separate OAuth secret and app-admin token; runtime auth reads Salus DB settings only |
| Hotline summary secret controls aggregate metric access | Medium | `integration-design.md` | Generate per-node secret in Kit Setup, keep server-side only, rotate when needed |
| Purge can permanently remove person-level activation data | High | `retention-backup-design.md`; `domain-database-design.md` | Require archived activation, valid backup, request/approval by distinct authorized users, and audit trail |

### 13. Realtime Communication

Salus Realtime events are contract-defined change notifications only. Rooms are separated into public center, center operations, and node command scopes. Event payloads contain resource references, change version, and change time, not sensitive evacuee records. Business state is committed in Salus before Realtime publish; publish failure must not roll back committed work.

### 14. Mapping and Geolocation

MapServer, map rendering, and geofencing are deferred beyond V2 in the current Salus V1 scope. Center records should not require MapServer geometry in V1.

### 15. Background Jobs, Schedulers, and Maintenance Tasks

Salus V1 declares no persistent queue worker, scheduler service, automatic scheduled task, or Maestro integration. Printing, Realtime publish, summary generation, backup, purge, and restore are request-time or explicit administrator-invoked operations according to the current design.

### 16. Configuration and Environment Variables

Design-stage configuration includes:

| Variable / Config | Purpose | Required | Related Module |
|---|---|---|---|
| `SALUS_HOTLINE_SHARED_SECRET` | Verify Hotline/Vox summary requests | Yes for Hotline integration | Aggregate summary |
| `SALUS_BASE_URL` / `SALUS_SHARED_SECRET` in Hotline | Let Hotline call Salus | Yes in Hotline when enabled | Hotline integration |
| Account SSO settings | Trusted client, callback, logout, secret state | Yes for SSO | Account |
| Account app-admin DB settings | Runtime app-admin enablement/token/client | Optional/enabled by Data Prep | Account admin |
| Realtime settings | URL, client/project codes, signing/backend ingress secrets | Yes for live updates | Realtime |
| `PBB_CA_BUNDLE` | Verified local HTTPS calls | Yes | Cross-app HTTPS |

### 17. Known Technical Debt and Gaps

| Area | Issue | Evidence | Recommended Next Step |
|---|---|---|---|
| Release/installation | Runtime app implementation has advanced, but release packaging/installer integration is not started | `salus\docs\implementation-checklist.md`; `salus\docs\integration-design.md` | Build release metadata, installer, Data Prep, package, and Kit Setup checks |
| Relief inventory | Full inventory/receiving/storage/lots are explicitly deferred despite product interest in relief operations | `v1-scope.md`; `development-proposal.md` | Decide whether V1 remains batch/receipt-only or adds a phased inventory slice |
| Hotline ownership | Hotline's storage/display/SITREP behavior after receiving Salus summary is outside Salus design | `integration-design.md` | Add a Hotline-side Salus summary ingestion/display proposal |
| Geofencing/maps | MapServer/geofencing are deferred | `v1-scope.md` | Keep V1 UI free of map/geofence assumptions |
| Privacy operations | Retention, backup, purge, and access controls are high-stakes | `retention-backup-design.md`; `privacy-access-design.md` | Turn designs into implementation tests before pilot use |

### 18. Testing Status

Salus has feature/unit test files under `tests`, and `docs\implementation-checklist.md` reports 13 tests / 76 assertions passing plus MariaDB migrations/idempotent seeds passing. In this local recheck, `npm run openapi:check` passed and confirmed OpenAPI coverage for 87 implemented API paths. `php artisan test` was not available in the local Artisan command list, and `vendor\bin\phpunit` was not present in the current vendor install, so the PHP test suite was not rerun during this recheck.

### 19. Evidence Summary

Evidence:
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

---

## App: PBB Kit Setup

### 1. Executive Technical Summary

PBB Kit Setup is an Electron/Node desktop installer shell plus PHP runner that orchestrates installation, package staging, DNS, SSL/vhost, firewall, service management, data prep, smoke checks, updates, and bundled PBB app packages. It is setup/provisioning-only and targets Windows local node machines. Current local `package.json` reports version `0.1.164`; the bundled manifest includes Landing, MapServer, Maestro, Realtime, Relay, Hotline, and Support as first-class app packages, plus the Cebu MapServer boundary supplemental pack. Owner clarification: node installation validates an admin-provided Hub HQ hub ID and hub token, registers all PBB processes as Windows services, and runs Data Prep to populate MapServer tiles for the Hub HQ-defined boundary. Current Kit docs/code also confirm the finalized Hotline/Relay/Support Data Prep contract and Support source-heartbeat webhook seeding.

### 2. Repository Overview

| Area | Details |
|---|---|
| App Name | PBB Kit Setup |
| Local Path | `C:\wamp64\www\pbb\kit-setup` |
| Main Language | JavaScript, PHP, PowerShell |
| Main Framework | Electron desktop app plus custom PHP runner |
| Frontend Framework | Electron renderer/plain JS |
| Backend Framework | Custom PHP CLI runner |
| Database | No direct database schema found; app installers may initialize their own DBs |
| Realtime Technology | None; installs Realtime bundle |
| Queue / Worker System | Service planning/start/stop/verify for app workers |
| Package Manager | npm |
| Runtime Requirements | Electron/Node for desktop shell, PHP for runner, PowerShell, Windows admin rights, WAMP stack |
| Main Entry Points | `desktop/main.js`, `desktop/renderer.js`, `bin/kit-setup.php`, `src/KitSetupRunner.php` |
| Important Config Files | `package.json`, `schema/kit-config.schema.json`, `packages/packages.bundled.json`, `examples/*.json`, `build/*.nsh` |
| Important Environment Variables | Unknown / Not confirmed from code |
| Deployment Target | Windows installer (`electron-builder` NSIS), local PBB node setup |

### 3. App Purpose and PBB Role

Kit Setup orchestrates installing multiple PBB apps on a target machine, using each app's own bundle/installer. It currently bundles Landing, MapServer, Maestro, Realtime, Relay, Hotline, and Support package ZIPs per `package.json` and `packages/packages.bundled.json`. It is responsible for local node provisioning rather than runtime emergency operations. Owner clarification: it validates Hub HQ hub ID/token information during installation, registers all processes as Windows services, and runs Data Prep for MapServer tile population. If unavailable, existing installed apps may run, but initial setup/update/service orchestration becomes manual.

### 4. User Roles and Permissions

| Role | Purpose | Capabilities | Code Evidence |
|---|---|---|---|
| installer operator | Runs desktop installer | Configure and execute install/update workflow | `desktop/*`; `package.json` |
| local CLI operator | Runs PHP runner | Run actions such as preflight, plan, prepare packages, DNS, service, SSL, smoke checks | `bin\kit-setup.php`; `src\KitSetupRunner.php` |

No multi-user auth system was found.

### 5. Main Features and Modules

### Desktop Installer Shell

Purpose: Electron UI for setup workflow.
Main Code: `desktop\main.js`, `desktop\preload.js`, `desktop\renderer.js`, `desktop\config-builder.js`.
Database Tables: None.
APIs / Routes: Electron IPC, not HTTP.
Offline Behavior: Local desktop app; bundled packages can install offline if present.
Sync Behavior: Unknown / Not confirmed from code.
Related PBB Apps: All bundled apps.
Evidence: `package.json`; `desktop`.

### PHP Setup Runner

Purpose: Execute install actions and produce reports/checkpoints.
Main Code: `bin\kit-setup.php`, `src\KitSetupRunner.php`.
Database Tables: None directly.
APIs / Routes: CLI actions.
Offline Behavior: Local. Remote checks/actions may need network.
Sync Behavior: Unknown / Not confirmed from code.
Related PBB Apps: MapServer, Maestro, Realtime, Relay, Hotline.
Evidence: `README.md`; `src\KitSetupRunner.php`.

### Bundled Package Management

Purpose: Stage/verify bundled app packages.
Main Code: `scripts\build-bundled-packages.ps1`, `packages\packages.bundled.json`.
Database Tables: None.
APIs / Routes: CLI/script.
Offline Behavior: Bundled ZIPs support offline install.
Sync Behavior: Unknown.
Related PBB Apps: Landing, MapServer, Maestro, Realtime, Relay, Hotline, Support.
Evidence: `package.json`; `packages`.

### Relay, Hotline, Support Data Prep Contract

Purpose: Generate the machine identities, handler mappings, tokens, and webhook seed data needed for Hotline-to-Support flows through Relay.
Main Code: `src\KitSetupRunner.php`, `docs\relay-hotline-support-data-prep-contract.md`.
Database Tables: None directly; Relay Data Prep writes `hub_relay_clients`, `hub_relay_handlers`, `relay_webhook_subscribers`; Support Data Prep writes encrypted settings.
APIs / Routes: Data Prep config generation, app installer/data-prep tools.
Offline Behavior: Local config generation works offline; generated HTTPS endpoints and CA bundle validation depend on local TLS/DNS setup.
Sync Behavior: Seeds Relay identities for `hotline.command`, `sitrep.ingestor`, and `support.dispatch`; seeds Support Source Heartbeats webhook subscriber for `source.heartbeat.updated`.
Related PBB Apps: Hotline, Relay, Support, Realtime.
Evidence: `C:\wamp64\www\pbb\kit-setup\docs\relay-hotline-support-data-prep-contract.md`; `C:\wamp64\www\pbb\kit-setup\src\KitSetupRunner.php`.

### App Bundle Update Policy

Purpose: Apply app bundle updates while guarding local node data.
Main Code: `src\KitSetupRunner.php`, `docs\updater-workflow.md`, `docs\app-bundle-versioning-and-update-contract.md`.
Database Tables: None directly in Kit Setup; app-owned migrations are delegated to each app.
APIs / Routes: `update-plan`, `update-apply` runner actions.
Offline Behavior: Works with local/bundled update packages when available.
Sync Behavior: None.
Related PBB Apps: Hotline, Relay, Realtime, Maestro, MapServer.
Evidence: `src\KitSetupRunner.php`; `docs\updater-workflow.md`; `docs\app-bundle-versioning-and-update-contract.md`.

Policy confirmed from code/docs: `requires_database_migration=true` is allowed only when the bundle declares an additive/preservative app-owned migration policy. Kit Setup passes migration intent to the app installer, blocks destructive/unsupported/dropped-table-or-column/overwrite-style updates by policy, and treats file rollback separately from database rollback/repair.

### Runtime Service Management

Purpose: Plan/start/stop/verify app services and clean WinSW services.
Main Code: `KitSetupRunner.php`, `bin\cleanup-winsw-services.ps1`, `assets\winsw`.
Database Tables: None.
APIs / Routes: runner actions `service-plan`, `service-start`, `service-stop`, `service-verify`.
Offline Behavior: Local.
Sync Behavior: None.
Related PBB Apps: Relay queue, Realtime gateway, Hotline workers, Maestro.
Evidence: `src\KitSetupRunner.php`; `assets\winsw`.

### 6. Database Schema Summary

No direct database usage found / Not confirmed from code. Kit Setup delegates database initialization to app installers.

Relationship map:

```text
kit-config JSON
  -> KitSetupRunner
       -> app package manifests
       -> app installers
       -> service/DNS/SSL/firewall/data-prep reports
```

### 7. API and Route Inventory

| Method | Path / Endpoint | Purpose | Auth | Handler / File | Notes |
|---|---|---|---|---|---|
| CLI | `bin\kit-setup.php --config ... --action preflight` | Preflight | local | `KitSetupRunner` | README example |
| CLI | actions `prepare-packages`, `dns-plan`, `dns-apply`, `dns-client-apply`, `dns-verify` | Package/DNS | local | `KitSetupRunner` | |
| CLI | actions `firewall-apply`, `service-plan`, `service-start`, `service-stop`, `service-verify` | Runtime services/firewall | local/admin | `KitSetupRunner` | |
| CLI | actions `ssl-plan`, `ssl-apply`, `remote-check`, `smoke-check`, `stage-report`, `update-plan`, `update-apply` | Install/update validation | local/admin | `KitSetupRunner` | |
| npm | `npm run desktop` | Start Electron shell | local | `desktop/main.js` | |
| npm | `npm run package:desktop:win` | Build Windows installer | local | electron-builder | |

### 8. Data Flow and Operational Flow

```text
Installer operator
-> Electron desktop UI / config builder
-> admin-provided Hub HQ hub ID/token validation
-> Hub HQ node identity / boundary information
-> kit config JSON
-> `bin/kit-setup.php`
-> package staging and app installer calls
-> DNS/SSL/firewall/service/data-prep/smoke reports
-> Windows service registration for PBB processes
-> MapServer tile population for Hub HQ-defined boundary
-> installed PBB node apps
```

### 9. Offline-First Behavior

Kit Setup can install from bundled packages listed in `package.json` and `packages/packages.bundled.json`, including Landing, MapServer, Maestro, Realtime, Relay, Hotline, and Support ZIPs plus the Cebu MapServer boundary pack. Owner clarification and code evidence confirm WampServer and Technitium DNS as node installation prerequisites; Kit Setup validates/probes WAMP services and has Technitium DNS apply/verify actions. Remote dependency checks, SSL/remote-check, PBB-managed FRP tunnel registration, upstream tile population, or Hub validation may require network depending on config. Owner clarification: node installation validates admin-provided Hub HQ hub ID/token data and Setup Data Prep populates MapServer tiles from the Hub HQ-defined boundary. Exact offline guarantee is Unknown / Not confirmed from code.

### 10. Integration with Other PBB Apps

| Integration | Direction | Protocol / Method | Purpose | Code Evidence |
|---|---|---|---|---|
| Kit Setup -> app bundles | Local files/ZIPs | Install/update apps | `package.json`; `packages\packages.bundled.json` |
| Kit Setup -> app installers | CLI/PHP installer contracts | Configure individual apps | `docs\app-installer-template.md`; `KitSetupRunner.php` |
| Kit Setup -> WinSW / Windows services | Local Windows service wrapper | Register/manage long-running PBB processes; owner clarification says process lifecycle belongs here, not Maestro | `assets\winsw`; `bin\cleanup-winsw-services.ps1` |
| Kit Setup -> DNS/SSL/firewall | Local system operations | Provision node access | `KitSetupRunner.php` action names |
| Kit Setup -> Hub HQ hub information | HTTP/config validation | Validate admin-provided hub ID/token and derive node identity/boundary inputs | Owner clarification; implementation details should remain documented with Kit Setup installer flow |
| Kit Setup -> MapServer Data Prep | CLI/data prep | Populate tiles for Hub HQ-defined boundary | `tools\data-prep`; owner clarification |
| Kit Setup -> Technitium DNS | HTTP API / local DNS client actions | Apply/verify local DNS records for node app domains | `src\KitSetupRunner.php`; `docs\implementation-decisions.md`; `docs\desktop-installer-shell.md` |
| Kit Setup -> WampServer | Local platform probes/config | Validate Apache/MySQL/PHP runtime prerequisites | `examples\kit-config.local-all.example.json`; `docs\desktop-installer-shell.md`; `docs\installer-readiness-checklist.md` |
| Kit Setup -> PBB-managed FRP tunnel | Planned service/provisioning integration | Register/configure FRPC on local node for cloud-visible Relay endpoint via Hub HQ/FRPS | Owner clarification; code-level implementation not confirmed in reviewed files |

Domain model note from post-briefing docs: local operational app access stays on Technitium-managed LAN names such as `relay.pbb.ph` and `hotline.pbb.ph`. Proposed public app proxy URLs are derived subdomains under `hub.json.domain`, such as `relay.{hub.domain}` or `hotline.{hub.domain}`. The exact FRP config generation/verification path remains an implementation item.

### 11. Deployment and Runtime Architecture

Electron Builder creates an NSIS Windows installer with `requestedExecutionLevel: requireAdministrator`. It packages desktop, PHP runner, schema, examples, Helper assets, WinSW, certs, and bundled PBB app ZIPs. `asar` is false. It targets x64 Windows.

### 12. Security and Privacy Notes

| Risk | Severity | Evidence | Suggested Fix |
|---|---|---|---|
| Installer runs with administrator rights | High | `package.json` `requestedExecutionLevel: requireAdministrator` | Keep signed releases and audited package manifests |
| Package signing assets present in fixtures | Medium | `fixtures\package-signing` | Ensure production signing keys are never bundled |
| Config may carry app secrets | High | Runner passes app installer config; docs mention installer contracts | Mask secrets in reports/checkpoints |

### 13. Realtime Communication

No realtime functionality found / Not confirmed from code. Kit Setup installs/configures the Realtime package.

### 14. Mapping and Geolocation

No direct mapping UI found. Kit Setup bundles MapServer and a province boundary package per `package.json`. Owner clarification: Setup Data Prep auto-populates MapServer tiles for the boundary dictated by Hub HQ hub information.

### 15. Background Jobs, Schedulers, and Maintenance Tasks

| Task | Schedule / Trigger | Purpose | Code Evidence |
|---|---|---|---|
| `scripts\build-bundled-packages.ps1` | Manual build | Build bundled packages | `package.json` |
| `bin\cleanup-winsw-services.ps1` | Manual maintenance | Clean Windows services | file inventory |
| Runner service actions | Manual/install workflow | Start/stop/verify services | `src\KitSetupRunner.php` |
| Updater migration policy | Update plan/apply | Allow additive app-owned migrations and block destructive patterns | `src\KitSetupRunner.php`; `docs\updater-workflow.md` |

### 16. Configuration and Environment Variables

| Variable / Config | Purpose | Required | Default / Example | Related Module |
|---|---|---|---|---|
| `schema\kit-config.schema.json` | Kit config schema | Yes | local schema | Runner/config builder |
| `examples\*.json` | Example configs | Optional | stub/local all examples | Tests/manual |
| `packages\packages.bundled.json` | Bundled package manifest | Yes for bundle installs | local package list | Package staging |
| `package.json build.files` | Electron packaging manifest | Yes | includes PBB package ZIPs | Desktop build |

### 17. Known Technical Debt and Gaps

| Area | Issue | Evidence | Recommended Next Step |
|---|---|---|---|
| Deployment assumptions | Windows/WAMP/admin rights are strongly assumed | `package.json`; docs | Document Linux/manual alternatives if supported |
| Network-dependent setup | WampServer and Technitium DNS are confirmed local prerequisites; owner clarified PBB-managed FRP tunnel topology, but code-level FRPS/FRPC provisioning was not confirmed here | `KitSetupRunner.php` action names; Technitium docs; owner clarification | Document Hub HQ control-plane, FRPS server, FRPC node-client, credentials, and service lifecycle |
| App endpoint/domain split | Local DNS names and proposed public subdomains are documented, but FRP generation/verification and app endpoint metadata remain draft/open | `docs\draft-public-local-domain-and-landing-surfaces.md`; chat-log updates | Finalize local/public URL contract and FRP config verification |
| Database migration updates | Additive app-owned migrations are now policy-supported; app-owned rollback/repair remains outside Kit Setup | `src\KitSetupRunner.php`; updater docs | Ensure every app bundle declares explicit migration policy and rollback expectations |
| Secret handling | Reports/checkpoints may include config-derived data | Runner writes JSON reports | Audit report masking for all app secrets |

### 18. Testing Status

Scripts include `npm run check:desktop` for JS syntax checks. README gives a PHP runner smoke test using `examples\kit-config.stub.json`. No PHPUnit suite was found for Kit Setup itself.

### 19. Evidence Summary

Evidence:
- `C:\wamp64\www\pbb\kit-setup\README.md`
- `C:\wamp64\www\pbb\kit-setup\package.json`
- `C:\wamp64\www\pbb\kit-setup\bin\kit-setup.php`
- `C:\wamp64\www\pbb\kit-setup\src\KitSetupRunner.php`
- `C:\wamp64\www\pbb\kit-setup\desktop`
- `C:\wamp64\www\pbb\kit-setup\packages`
- `C:\wamp64\www\pbb\kit-setup\assets\winsw`
- `C:\wamp64\www\pbb\kit-setup\docs\updater-workflow.md`
- `C:\wamp64\www\pbb\kit-setup\docs\app-bundle-versioning-and-update-contract.md`
- `C:\wamp64\www\pbb\kit-setup\docs\draft-public-local-domain-and-landing-surfaces.md`
