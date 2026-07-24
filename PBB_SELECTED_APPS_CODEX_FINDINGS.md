# PBB Selected Apps: Codex Findings

## Reviewed Apps

| App | Path | Reviewed? | Notes |
|---|---|---|---|
| PBB Hotline / Vox | `C:\wamp64\www\pbb\hotline` | Yes | Laravel emergency/call/incident app with citizen/operator/command/admin roles, SITREP Relay handoff, Support Request services, and Realtime/MapServer integration |
| PBB Helper | `C:\wamp64\www\hotline-helpers` | Yes | Static JS/CSS UI helper library, not a responder backend |
| PBB Relay | `C:\wamp64\www\pbb\relay` | Yes | Laravel store-and-forward relay with outbox/delivery/inbox/uploads/handlers and relationship resolver |
| PBB Hub / Hub HQ | `C:\wamp64\www\pbb\hub.ph` | Yes | Laravel hub registry, heartbeat, tokens, geodata, recovery |
| PBB Maestro | `C:\wamp64\www\pbb\maestro` | Yes | Laravel worker telemetry monitor; owner clarified observer-only scope |
| PBB Realtime Server | `C:\wamp64\www\pbb\realtime` | Yes | Laravel/Ratchet WebSocket gateway shared by node apps |
| PBB MapServer | `C:\wamp64\www\mapserver` | Yes | Plain PHP tile proxy/cache and boundary overlay server |
| PBB Chatviewer / Syndicatum | `C:\wamp64\www\pbb\chatviewer` | Yes | Plain PHP/JS DB-backed agent chat with Markdown fallback, token-authenticated posting/claiming, and newest-first entry query API |
| PBB Kit Setup | `C:\wamp64\www\pbb\kit-setup` | Yes | Electron/PHP/PowerShell setup tool; current local version `0.1.164` bundles Landing, MapServer, Maestro, Realtime, Relay, Hotline, and Support |
| PBB Support System / Imperium | `C:\wamp64\www\pbb\support` | Yes | Laravel SITREP/support operations app for Relay SITREP intake, support requests, source heartbeat webhooks, Realtime heartbeat publish, and lifecycle updates |
| PBB Landing | `C:\wamp64\www\pbb\landing` | Yes | Lightweight PHP LAN launcher, public hub metadata projection, Kit-managed app registry, and Relay gateway surface |
| PBB Chat / Civitas | `C:\wamp64\www\pbb\chat` | Yes | Laravel 12 local barangay chat app with rooms, direct conversations, message requests, reports/blocks/moderation, badges, Realtime admission/publishing, and Hotline handoff |
| PBB Games / Tabulus | `C:\wamp64\www\pbb\games` | Yes | Plain PHP optional local citizen engagement/preparedness-learning app with no database and no confirmed operational API calls in version 1 |
| PBB Healthcare / PBB Natalium | `C:\wamp64\www\pbb\natalium` | Yes | Laravel 12 local health app with product name Natalium; Account SSO/admin sync, patient registry, health programs, care workflow, maternal/child health, birth-defect surveillance, referrals, prescriptions, citizen/staff surfaces, reporting/export, operations, and tests |
| PBB Utility / Vena | `C:\wamp64\www\pbb\utility` | Yes | Laravel 12 utility-operator app with roles, assets/teams/settings, MapLibre config, inbound-only Relay intake for `hotline.incident.upserted` targeted to `utility.vena`, normalized incidents, quarantine/stale handling, and operator/responder missions |
| PBB Account | `C:\wamp64\www\pbb\account` | Yes | Laravel 12 local node identity and SSO service with canonical accounts, trusted clients, hashed one-time authorization codes, admin surface, app-admin provisioning hooks, session identity APIs, optional Realtime admission, and PHP SDK |
| PBB Salus | `C:\wamp64\www\pbb\salus` | Implementation in progress | Laravel 12 Auxilus Mos evacuation-center and relief-distribution app with routes/controllers/models/migrations for multiple centers, registration, movement/occupancy, relief-batch receipts, citizen QR status, Account, Realtime, Landing, Kit Setup-facing health/readiness, and Hotline aggregate-summary access |
| PBB Library / Libria | `C:\wamp64\www\pbb\library` | Yes | Laravel 12 Library Cloud and Library Node implementations plus shared PHP/JS packages. Cloud handles content governance/ingestion/canonical storage/signing/releases/node distribution/fleet reports; Node handles offline catalog/search/resource serving, SDK/embed, Account hooks, trust/release/storage state, and local content delivery |
| PBB Learning / Lumaria | `C:\wamp64\www\pbb\learning` | Yes | Laravel 12 local LMS-agnostic learning gateway with Account SSO/app-admin, provider/catalog/instance APIs, admin diagnostics/settings, emergency-state APIs, OpenAPI/docs routes, Vite assets, `pbb_learning` migrations, and V1 implementation status docs |

## Product Names and Suite Grouping

Owner-provided naming clarification, 2026-07-24:

| Product Group | App / Repository | Product Name / Alias |
|---|---|---|
| Citizen Facing Apps | PBB Library | Libria |
| Citizen Facing Apps | PBB Games | Tabulus |
| Citizen Facing Apps | PBB Chat | Civitas |
| Citizen Facing Apps | PBB Healthcare / PBB Natalium | Natalium |
| Citizen Facing Apps / Learning | PBB Learning | Lumaria |
| Wizaya Server Suite - Deployment Modules | Kit Setup | Kit Setup |
| Wizaya Server Suite - Deployment Modules | PBB Landing | PBB Landing |
| Wizaya Server Suite - Core Services | PBB Account | PBB Account |
| Wizaya Server Suite - Core Services | PBB Relay | PBB Relay |
| Wizaya Server Suite - Core Services | PBB Realtime | PBB Realtime |
| Wizaya Server Suite - Core Services | PBB Maestro | PBB Maestro |
| Wizaya Server Suite - Core Services | PBB MapServer | PBB MapServer |
| Auxilos Mos bundle system | PBB Hotline | Vox |
| Auxilos Mos bundle system | PBB Support System | Imperium |
| Auxilos Mos bundle system | PBB Utility | Vena |
| Development Tools | PBB Helper | PBB Helper |
| Development Tools | PBB Chatviewer | Syndicatum |

## Most Important Findings

1. The current PBB stack is local-node-oriented: WAMP/Apache/PHP apps, local MySQL databases, local queues/workers, local map cache, and Kit Setup-managed installation/service wiring.
2. Hotline remains the main emergency operations app. It handles local incident/call/media/location/SITREP workflows and now has confirmed Relay-facing SITREP and Support Request services.
3. Relay is the core store-and-forward service. It handles local app messages, hub-to-hub delivery, handler dispatch, uploads, receipts, retries, and backend-only relationship resolution.
4. Support System is a reviewed first-class app. It receives Relay-delivered SITREPs/support requests, stages latest SITREPs, consolidates current SITREP data, receives Relay `source.heartbeat.updated` webhooks, publishes heartbeat snapshots to Realtime, and sends support lifecycle updates back through Relay.
5. Landing is now a reviewed first-class infrastructure app. It serves `https://pbb.ph` launcher behavior, exposes sanitized public hub metadata, accepts Kit Setup registry writes, and can gateway `/relay/api/v1/*` to registered Relay targets.
6. Realtime is expected to run as one shared gateway instance per node. It provides WebSocket admission, rooms, presence, chat, calls, media chunk coordination, and app events.
7. Maestro is observer-only. Kit Setup/Windows services own service lifecycle and recovery.
8. MapServer offline behavior depends on Setup Data Prep successfully preloading tiles and boundaries for the Hub HQ-defined boundary.
9. Broad editable incident sync across nodes is not the current design. Owner clarification says nodes consolidate SITREPs from sources plus their own SITREP and send periodic SITREPs upstream.
10. The planned responder/helper mobile workflow and PBB-managed FRP tunnel gateway are owner-confirmed designs, but implementation details are not fully confirmed from reviewed code.
11. The finalized Hotline/Relay/Support contract uses distinct machine identities: `hotline.command`, `sitrep.ingestor`, and `support.dispatch`; `sitrep.record` and `support.request` should not be collapsed into one flow.
12. Relay source heartbeat updates are now operational webhooks (`source.heartbeat.updated`) stored/tracked separately from normal `hub_relay_messages`.
13. PBB Chat is now a reviewed local/LAN-only community chat app. `release.json` disables public gateway exposure; code confirms Realtime admission/publishing and handoff-only Hotline escalation.
14. PBB Games is now a reviewed optional local games/learning app. Code confirms mode-based active-incident/emergency behavior and no database.
15. PBB Natalium is now reviewed in its current Laravel form. Code confirms Account SSO/admin sync, patient registry/profile applications, health programs, care workflow, maternal/child health, birth-defect surveillance, referrals, prescriptions, citizen/staff surfaces, reporting/export, operations backup/restore/export hooks, local document/backup paths, and Relay hub JSON URL context.
16. PBB Utility / Vena is now reviewed. Code confirms inbound-only Relay consumption of Hotline incident snapshots targeted to `utility.vena`, with raw retention, normalized incidents, stale update protection, quarantine handling, and metadata-only media refs.
17. Helper active loader cache revisions are `0.21.117`, while Helper `package.json` still reports `0.21.83`.
18. PBB Account is now implementation-confirmed. Code confirms Laravel 12 Account Service behavior: local `pbb_accounts` DB, `accounts`, `trusted_clients`, `sso_authorization_codes`, `account_audit_events`, `/oauth/authorize`, `/oauth/token`, `/oauth/logout`, Account admin APIs, trusted identity APIs, optional Realtime admission, seeded Chat/Hotline/Landing clients, and PHP SDK.
19. Older apps have also moved toward Account integration. Hotline, Relay, Chat, Support, Realtime, and Maestro now have Account SSO and/or Account app-admin endpoints using local `users.pbb_user_id`; Landing has Account launcher/session UI behavior; Kit Setup docs define Account SSO/app-admin installer conventions.
20. PBB Salus is now implementation-in-progress. Local code confirms a Laravel 12 app scaffold with routes, controllers, models, migrations, middleware, services, tests, frontend assets, and OpenAPI tooling. Docs/checklist report implementation through milestone 19, Laravel foundation complete, operational modules implemented, Account/Realtime/Hotline contracts implemented, and release/installation not started. Local route listing showed 113 routes; `npm run openapi:check` passed and confirmed OpenAPI coverage for 87 implemented API paths.
21. PBB Library is now implementation-confirmed. Code confirms separate Laravel Cloud and Node apps, shared PHP/JS packages, Cloud node APIs for profile/offers/manifests/objects/trust/withdrawals/rollback/inventory/deployment events, and Node APIs for bootstrap/search/catalog/resource/content/payload/citation/related/admin health/settings/storage/release state. Routine large Library content transfer remains outside normal PBB Relay messages.
22. PBB Learning / Lumaria is now implementation-confirmed but still V1-incomplete. Code confirms Laravel routes/migrations, Account SSO/app-admin hooks, provider/catalog/instance APIs, admin diagnostics/settings/platform toggle/instance probe routes, emergency-state APIs, OpenAPI/docs routes, and tests/status docs. Complete Learning-side Library adapter behavior and deep LMS/provider sync are not fully confirmed.

## Confirmed Architecture

```text
Hotline
  -> local DB for incidents/calls/messages/media/SITREPs/support requests
  -> Relay-facing SITREP and Support Request delivery services
  -> internal SITREP media manifest/download API
  -> Realtime for live rooms/presence/calls/chat/media/app events
  -> MapServer for map tiles/boundaries
  -> Helper UI bundle

Relay
  -> local DB for messages/deliveries/receipts/uploads/handlers
  -> operational webhook subscribers/deliveries for source heartbeat events
  -> queue workers for delivery and handler dispatch
  -> peer Relay `/api/v1/receive`
  -> backend-only relationship resolver
  -> Hub/HQ heartbeat/registry when configured

Support System
  -> Relay handler endpoints for SITREPs and support requests
  -> Relay source heartbeat webhook receiver
  -> local DB staging/current consolidated SITREPs
  -> support request receive/accept/reject/assign/en-route/complete actions
  -> Realtime publish path for source heartbeat snapshots
  -> outbound Relay messages for consolidated SITREPs and support lifecycle updates

Landing
  -> local launcher and app registry
  -> Relay `public/hub.json` file read
  -> public `/.well-known/pbb-hub.json` projection
  -> allowed peer `/relay/api/v1/*` gateway to local Relay

PBB Chat
  -> local `pbb_chat` database
  -> PBB Realtime admission/backend publishing
  -> Helper-backed chat UI
  -> Hotline handoff endpoint returning configured Hotline URL

PBB Games
  -> local PHP/static game registry and mode policy
  -> Helper-backed UI/game components
  -> no confirmed operational API calls in version 1

PBB Natalium
  -> PBB Account SSO authorize/callback/logout and Account-admin user sync guard
  -> Laravel 12 local `pbb_natalium` database for users, patient registry, health programs, care workflows, maternal/child health, birth-defect surveillance, referrals, prescriptions, reporting, operations, audit, and settings
  -> local document and backup storage
  -> Relay hub JSON URL node context

PBB Utility / Vena
  -> Laravel 12 app with admin/operator/command/responder roles
  -> Relay handler `POST /api/relay/incidents`
  -> accepts `hotline.incident.upserted` targeted to `utility.vena`
  -> stores raw inbound records and normalized utility incidents
  -> rejects/quarantines wrong type, wrong target, missing version, bad identity, stale version, and media refs containing paths/URLs
  -> operator missions and responder mission acceptance
  -> MapLibre config through `/vena-map.json`

PBB Salus
  -> implemented local `pbb_salus` Laravel domain/migration model
  -> multiple evacuation centers per barangay evacuation operation
  -> express/regular individual and family registration, validation, opaque tags/QRs, and worker-driven printing
  -> optional individual `in`/`out` tracking or aggregate gate counts for occupancy
  -> relief distribution batches, receipt tracking, and correction approvals; full inventory is deferred from V1
  -> PBB Account SSO/app-admin with Salus-local roles/capabilities
  -> PBB Realtime privacy-safe change notifications followed by REST refetch
  -> Hotline/Vox bearer-protected pull of aggregate operational summaries
  -> Landing local launcher and Kit Setup installation/Data Prep; public gateway disabled

PBB Account
  -> central canonical `pbb_user_id`
  -> Laravel 12 app with local `pbb_accounts` database
  -> central password/account status/role/profile ownership
  -> implemented `/oauth/authorize`, `/oauth/token`, `/oauth/logout` SSO flow
  -> trusted client admin and secret rotation
  -> app-admin provisioning bridge
  -> session identity and optional Realtime admission
  -> apps keep local sessions and local user rows linked by `pbb_user_id`

PBB Library
  -> Library Cloud Laravel app: source ingestion, normalization, provenance/licensing, canonical content storage, release construction, signed manifests, node assignment, release offers, deployment reports, fleet reports
  -> Library Node Laravel app: offline catalog/search/resource serving, signed package verification, staging/activation/rollback, local DB/search/object store, SDK/embed, health/admin/settings, Learning reference-provider API surface
  -> public use should not require login
  -> routine multi-terabyte release transfer should not use PBB Relay

PBB Learning / Lumaria
  -> Laravel 12 LMS-agnostic learning gateway/control plane
  -> local `pbb_learning` database for providers, platform types, servers, instances, catalog, packages, jobs, health checks, settings, audit, and users
  -> PBB Account SSO/app-admin identity hooks are implemented
  -> PBB Library is consumed as a reference provider, not treated as an LMS; full Learning-side adapter behavior was not confirmed
  -> Learning V1 status docs still list remaining admin workflows, adapter decisions, Kit/Data Prep, and release/security work

Account-consuming older apps
  -> Hotline, Relay, Chat, Support, Realtime, Maestro have Account SSO and/or app-admin code paths
  -> app-admin endpoints use bearer token plus `X-PBB-Account-Client: pbb-account`
  -> app-local users preserve local roles/history while linking/unlinking `pbb_user_id`
  -> Landing exposes Account launcher/session UI behavior
  -> Kit Setup docs define installer conventions for Account env names, callback URLs, and app-admin tokens

Kit Setup
  -> bundled packages and app installers
  -> WampServer and Technitium DNS validation
  -> Relay/Hotline/Support Data Prep identities and webhook token generation
  -> DNS/SSL/vhost/firewall/service/data-prep/smoke/update actions
```

## Unconfirmed / Unknown

- Exact field mapping for the owner-confirmed barangay -> city/municipality -> province -> cloud HQ topology.
- Final production SITREP envelope/content cadence and retention rules.
- Cross-app endpoint metadata contract for resolving a remote Hotline media base URL. Relay resolver returns topology/credentials, not a Hotline app URL.
- Implementation/code location for the planned responder/helper mobile workflow.
- Code-level implementation details for PBB-managed FRP tunnel control plane, FRPS/FRPC provisioning, credentials, endpoint lifecycle, and Hub HQ APIs.
- Whether Support System should be deployed only at upper-level/HQ-side support nodes or also on barangay node kits.
- Whether PBB Chat should remain strictly local/LAN-only or eventually synchronize selected messages/moderation data upstream.
- Whether PBB Games mode should be controlled only by config or by Kit Setup/Landing/local incident state.
- Whether Natalium health records should ever sync upstream through Relay or remain strictly local to a node.
- Natalium local/offline login behavior when PBB Account is unavailable for new SSO sessions.
- Whether Vena should emit utility mission/status/lifecycle updates back to Hotline/Relay/Support, or remain inbound-only in V1.
- Whether Vena's web responder mission endpoints should become part of the planned mobile helper workflow.
- Which consuming apps have Account enabled in production versus code-present but disabled/default-off.
- Which trusted clients are registered in Account for Relay, Support, Realtime, Maestro, Natalium, and other non-seeded apps.
- Consuming app local/offline login behavior when Account Service is unavailable.
- Whether Account Service V1 should remain PBB-internal OAuth-like SSO or implement full OIDC later.
- Data retention/encryption policy for incident, media, chat, location, relay payload, support request, and recovery data.
- Salus release metadata, installer, package, Data Prep, and Kit Setup integration are not yet confirmed; runtime implementation has started and advanced.
- Whether Salus V1 should keep relief inventory/receiving/storage deferred or bring a phased inventory slice into the first implementation.
- How Hotline will store, refresh, display, revise, and submit Salus aggregate summaries in its SITREP flow.
- Library installer/Data Prep/Landing packaging, field release transfer runbook, and end-to-end failure/rollback operations are not fully confirmed from the focused scan.
- Learning full Library adapter behavior, deep LMS/provider sync scope, installer/Data Prep wiring, and final V1 release/security signoff are not fully confirmed from the focused scan.
- Whether Library Cloud and Library Node should become separate deployable repositories/apps or remain subfolders under `library`.
- The exact V1 Learning provider scope: local Moodle only, external LMS links, or multiple adapters.

## High-Risk Technical Issues

| Issue | Affected Apps | Severity | Evidence | Recommended Fix |
|---|---|---|---|---|
| Support System README documents default local admin credentials | Support System | High if retained in shared deployment | `support\README.md` | Force password change or disable seeded default before shared/non-dev deployment |
| Support admin/settings routes are auth-protected, but role middleware was not confirmed | Support System | High | `support\routes\web.php`; `support\app\Models\User.php` | Add or document route-level admin authorization for user/settings endpoints |
| Landing public gateway forwards Relay API paths when registry and peer checks pass | Landing, Relay | High | `landing\src\Gateway.php`; `landing\src\RegistryStore.php`; `landing\tests\run.php` | Keep peer allowlist strict, document FRP/public gateway threat model, and test rejected peers |
| Landing registry token controls internal app registry writes | Landing, Kit Setup | High | `landing\src\Auth.php`; `landing\README.md`; `landing\config\landing.php` | Generate per-node token, store only hash, keep `/internal/*` local-only |
| Support source heartbeat webhook token controls operational telemetry ingress | Support System, Relay, Kit Setup | High | `support\app\Http\Controllers\Api\RelaySourceHeartbeatController.php`; `kit-setup\docs\relay-hotline-support-data-prep-contract.md`; `relay\tools\data-prep\apply-settings.php` | Generate per-node token, rotate on compromise, keep only server-side |
| PBB Chat stores local community/direct-message/report/block data | PBB Chat | High | `chat\database\migrations`; `chat\routes\web.php` | Define retention, moderation access, backup, and purge policy |
| PBB Chat public gateway must remain disabled unless a separate security model is designed | PBB Chat, Landing, Kit Setup | Medium | `chat\release.json` | Keep Chat LAN-only and add installer/registry validation for `public_gateway.enabled=false` |
| PBB Games emergency mode depends on local mode configuration | PBB Games | Medium | `games\config\games.php`; `games\src\ModePolicy.php` | Document or automate mode switching during active incidents |
| Natalium stores sensitive patient/clinical/document/access/reporting data locally | PBB Natalium | High | `natalium\database\migrations`; `natalium\routes\api.php`; `natalium\.env.example` | Define retention, backup, encryption-at-rest, document access, and audit policy before live health deployment |
| Account app-admin tokens control local user provisioning across multiple apps | Hotline, Relay, Chat, Support, Realtime, Maestro, Natalium, PBB Account | High | app `VerifyAccountAdminService`; app Account admin tests; `kit-setup\docs\app-installer-template.md` | Use app-scoped tokens only, rotate per app, and do not rely on generic shared env fallbacks |
| Vena retains raw Relay incident payloads | PBB Utility / Vena, Relay, Hotline | High | `utility\app\Http\Controllers\Api\RelayIncidentHandlerController.php`; `utility\database\migrations` | Define retention, audit access, purge policy, and local backup encryption |
| Vena incident media refs must remain metadata-only | PBB Utility / Vena, Hotline, Relay | High | `utility\tests\Feature\VenaRelayIncidentIntakeTest.php`; `RelayIncidentHandlerController.php` | Keep rejecting direct URLs/paths and document proper backend media retrieval |
| Vena Relay handler token controls incident ingestion | PBB Utility / Vena, Relay | High | `utility\.env.example`; `RelayIncidentHandlerController.php` | Generate per-node/per-source token and rotate on compromise |
| Salus stores proposed sensitive evacuee/person/photo/movement data locally | PBB Salus, PBB Account, Hotline | High | `salus\docs\privacy-access-design.md`; `salus\docs\domain-database-design.md` | Keep person-level records local, store photos outside public web root, enforce capabilities, and test summary privacy exclusions |
| Salus citizen QR tokens grant limited possession-based local access | PBB Salus | High | `salus\docs\tagging-design.md`; `salus\docs\privacy-access-design.md` | Use opaque high-entropy hashed QR tokens and expose only schedules, received marks, and announcements |
| Salus Hotline summary shared secret controls aggregate access | PBB Salus, Hotline | Medium | `salus\docs\integration-design.md`; `salus\docs\operational-summary-design.md` | Generate a per-node server-side secret through Kit/Data Prep and rotate on compromise |
| Account Service is the central credential authority | PBB Account, Chat, Hotline, Landing, Natalium, future apps | High | `account\database\migrations`; `account\app\Http\Controllers\OAuthController.php`; `account\app\Models\TrustedClient.php` | Rotate seeded dev credentials, restrict admin access, keep hashed secrets/codes, and define backup/encryption/retention policy before rollout |
| Account seeded development credentials exist | PBB Account | High if reused outside dev | `account\README.md`; `account\database\seeders\DatabaseSeeder.php` | Rotate/remove seeded test account and `*-dev-secret` clients before shared deployment |
| Library package signing/trust/release operations need hardening docs and tests | PBB Library Cloud, PBB Library Node, Kit Setup | High | `library\cloud\routes\web.php`; `library\node\database\migrations`; Library release/trust migrations | Publish signed manifest/verification/rollback runbook and keep end-to-end release tests |
| Library public search/resource APIs must remain path-opaque | PBB Library Node, Learning | High | `library\node\routes\api.php`; `library\node\routes\web.php` | Keep public APIs credential-free/path-opaque and separate from admin/package endpoints |
| Learning identity/provider boundaries are implemented but V1 is incomplete | PBB Learning, PBB Account, LMS providers | High | `learning\routes\web.php`; `learning\routes\api.php`; `learning\docs\PBB_LEARNING_V1_IMPLEMENTATION_STATUS.md` | Finish provider/LMS adapter decisions, admin workflows, installer wiring, and security review |
| Library citizen search privacy needs policy | PBB Library Cloud, PBB Library Node | Medium | `library\node\database\migrations`; search history/saved search routes | Do not upload raw citizen search queries to Cloud in V1 unless a privacy policy and consent model exist |
| Cross-app endpoint metadata not yet confirmed for remote media access | Hotline, Relay, Support System, Landing/Hub context | High | Relay resolver returns topology `domain`; Hotline media SDK requires explicit API base URL | Define app endpoint metadata such as `apps.hotline.base_url` in Hub/Landing/Relay topology |
| Relay relationship resolver returns shared credentials to backend clients | Relay, backend consumers | High | `relay\routes\api.php`; `RelayRelationshipResolver.php` | Keep resolver backend-only and add tests that browser/frontends never call it |
| Sensitive emergency/support data stored locally | Hotline, Relay, Support System, Realtime, Hub | High | App migrations and storage paths | Define retention, encryption-at-rest/backups, role access review, and purge procedures |
| Map offline coverage depends on setup data prep | MapServer, Hotline, Support System, Kit Setup | High | `mapserver\config.php`; owner clarification on Setup Data Prep | Add or keep a tile/boundary preflight after data prep |
| Realtime rooms/presence are in-memory | Realtime | Medium | `realtime\app\Realtime\WebSocket\RealtimeGateway.php` | Keep one gateway instance per node unless shared state/fanout is implemented |
| Chatviewer read APIs are unauthenticated while write APIs use project tokens | Chatviewer | Low if personal/local-only; Medium if exposed | `chatviewer\api\chat-log.php`; `chatviewer\api\chat-entries.php`; `ChatRepository.php` | Keep local/private or add read auth before any shared deployment |

## Missing Documentation

| Missing Doc | Affected Apps | Why It Matters |
|---|---|---|
| Hub HQ `hub.json` node identity contract | Hub, Relay, Kit Setup, MapServer, Landing, Support System | Node identity drives install validation, topology, map prep, public projection, and SITREP routing |
| Landing registry/public projection/gateway runbook | Landing, Kit Setup, Relay, Hub/HQ | Defines who writes registry data, what is public, and how Relay gateway exposure is secured |
| Relay source heartbeat webhook runbook | Relay, Support System, Kit Setup, Realtime | Webhook token ownership, retry/dead-letter behavior, and Support Realtime publish expectations are now implemented and need operator docs |
| PBB-managed FRP tunnel gateway spec | Hub/HQ, Relay, Landing, Kit Setup | Owner confirmed design, but provisioning/control-plane/credential lifecycle needs implementation docs |
| Support System deployment and authorization guide | Support System | Placement, users, role boundaries, default credential handling, and token ownership need clear operations rules |
| Relay/Hotline/Support Data Prep contract | Hotline, Relay, Support System, Kit Setup | The two Support identities and three handler flows are now confirmed and need to stay canonical |
| Support Request lifecycle contract | Hotline, Relay, Support System | Prevents event/status drift, especially local `completed` versus outbound `fulfilled` |
| SITREP upstream reporting contract | Hotline, Relay, Support System, Hub/HQ | Production envelope, schedule, consolidation rules, and idempotency need stable documentation |
| SITREP media access and retention runbook | Hotline, Support System | Consumers need auth, cache, audit, and purge rules for evidence media |
| Responder/helper mobile app spec | Hotline, Relay, Realtime, planned responder app | Owner-confirmed workflow needs assignment/status/field-report/sync API contracts |
| Offline map setup/preflight standard | MapServer, Hotline, Support System, Kit Setup | Field kits need objective pass/fail checks after tile/boundary preparation |
| Service lifecycle ownership runbook | Maestro, Kit Setup, Relay, Realtime, Hotline | Maestro observes only; Windows services/Kit Setup own recovery |
| PBB Chat offline/sync boundary | PBB Chat, Realtime, Landing | LAN-only operation is confirmed, but durable offline queues and Relay sync are not |
| PBB Games emergency-mode operations | PBB Games, Kit Setup/Landing context | Operators need to know how/when games are hidden or disabled |
| Natalium health privacy and deployment guide | PBB Natalium, PBB Account | Patient/document/access/audit data requires clear local operations, backup, retention, and Account dependency rules |
| Vena Relay intake and utility mission contract | PBB Utility / Vena, Hotline, Relay | Inbound contract exists in code/tests, but operator docs should define token ownership, idempotency, status handling, and whether outbound lifecycle exists |
| PBB Account consuming-app integration matrix | PBB Account, Chat, Hotline, Landing, Natalium, Relay, Support, Realtime, Maestro | Account implementation exists and older apps have integration code; docs should track enabled/disabled status, trusted clients, callback URLs, and app-admin tokens |
| Salus release metadata and installer | PBB Salus, Account, Realtime, Hotline, Landing, Kit Setup | Runtime app code/migrations/routes are present, but release metadata, package, installer, Data Prep, and Kit Setup integration still need to be built |
| Hotline-side Salus summary usage design | Hotline, PBB Salus | Salus defines the aggregate summary API, but Hotline owns how the summary is stored, refreshed, displayed, revised, and included in SITREPs |
| Salus V1 relief inventory decision | PBB Salus | Relief receiving/inventory/storage are product needs but explicitly deferred from current V1 docs |
| Library Cloud/Node release and operations contract | PBB Library, Kit Setup, Landing, Learning | Implementation exists; package manifest, APIs, installer, storage layout, rollback, and failure handling need deployable operator docs |
| Learning V1 completion guide | PBB Learning, PBB Account, PBB Library, Landing | Implementation exists; LMS adapter depth, Library adapter, admin workflows, emergency mode, and local/offline behavior need completion criteria |
| Library-to-Learning adapter OpenAPI/schema | PBB Library, PBB Learning | Library Node APIs exist and the addendum describes adapter concepts, but the focused scan did not confirm complete Learning-side calls |

## Suggested Immediate Actions

| Action | Reason | Priority |
|---|---|---|
| Publish the Hotline/Support/Relay Data Prep and runtime contract as canonical operator docs | The two Support identities, handler mappings, and source heartbeat webhook are active cross-app flows | P1 |
| Document Hub HQ `hub.json` node identity fields and topology examples | Kit Setup, Landing, Relay, Support, MapServer, and upstream routing depend on this data | P1 |
| Formalize Landing registry, public projection, and Relay gateway behavior | Landing is now a public-facing infrastructure surface and needs a clear threat/deployment model | P1 |
| Harden Support System credentials and authorization boundaries | Default credential and admin/settings access need explicit shared-deployment safeguards | P1 |
| Document and test Relay source heartbeat webhook delivery end-to-end | Relay, Kit, Support, and Realtime now depend on this path for current heartbeat dashboard updates | P1 |
| Define app endpoint metadata for remote Hotline media access | Prevents misuse of Relay topology `domain` as an app API base URL | P1 |
| Add/keep offline map preflight checks after Setup Data Prep | Verifies required cached tiles and boundaries before field deployment | P1 |
| Write the responder/helper mobile workflow API and sync spec | Converts the planned field response loop into implementable contracts | P1 |
| Document PBB-managed FRP control-plane and node-client lifecycle | Clarifies cloud-visible Relay/Landing exposure without external tunnel providers | P1 |
| Document Maestro as observer-only and Windows service recovery as Kit Setup/service-layer responsibility | Avoids expanding Maestro beyond the confirmed design | P2 |
| Decide/document PBB Chat offline and upstream sync boundary | Prevents over-assuming Relay sync for local community chat | P2 |
| Add PBB Games emergency-mode runbook or control hook | Ensures optional games do not compete with emergency operations | P2 |
| Document Natalium patient/document privacy, backup, and Account dependency rules | Health records require stricter deployment guidance before live use | P1 |
| Publish the Vena inbound Relay incident contract and decide outbound lifecycle scope | Utility operators now have a confirmed inbound app, but return/status behavior is not confirmed | P1 |
| Decide whether Vena responder routes are part of the future mobile helper workflow | Prevents mixing current web Vena behavior with planned offline mobile responder design | P2 |
| Document Account consuming-app integration status and rotate seeded/dev/app-admin credentials | Account exists and older apps have integration code; deployment risk is now app adoption, per-app secrets, and local/offline behavior | P1 |
| Finish Salus release packaging and Kit Setup integration | Salus runtime implementation is in progress, but release/installation remains not started in the checklist | P1 |
| Define Hotline's Salus aggregate-summary ingestion and SITREP behavior | Salus intentionally makes Hotline the active caller and downstream owner of use | P1 |
| Decide whether full relief inventory stays deferred or becomes a phased Salus implementation slice | Current V1 relief is scheduled batches and receipt tracking only | P2 |
| Turn Library proposal into concrete V1 contracts | Library introduces signed releases, large offline storage, public APIs, and Learning dependency, but has no implementation code yet | P1 |
| Publish Library Cloud/Node release transfer, rollback, and installer runbook | Library implementation now has Cloud/Node APIs and trust/release/storage state, but field operations need concrete docs | P1 |
| Finish Learning V1 admin/provider/Library/Kit hardening | Learning implementation exists but status docs still leave admin editing, provider sync decisions, emergency transitions, Kit/Data Prep, and security review open | P1 |
| Keep Chatviewer local/private | It is personal development coordination tooling, not an operational app | P3 |

## Key Evidence Files

- `C:\wamp64\www\pbb\hotline\routes\api.php`
- `C:\wamp64\www\pbb\hotline\routes\console.php`
- `C:\wamp64\www\pbb\hotline\database\migrations`
- `C:\wamp64\www\pbb\relay\routes\api.php`
- `C:\wamp64\www\pbb\relay\config\relay.php`
- `C:\wamp64\www\pbb\hub.ph\routes\api.php`
- `C:\wamp64\www\pbb\maestro\routes\api.php`
- `C:\wamp64\www\pbb\realtime\app\Realtime\WebSocket\RealtimeGateway.php`
- `C:\wamp64\www\mapserver\index.php`
- `C:\wamp64\www\pbb\kit-setup\src\KitSetupRunner.php`
- `C:\wamp64\www\pbb\kit-setup\packages\packages.bundled.json`
- `C:\wamp64\www\pbb\kit-setup\docs\relay-hotline-support-data-prep-contract.md`
- `C:\wamp64\www\pbb\support\routes\api.php`
- `C:\wamp64\www\pbb\support\app\Http\Controllers\Api\RelaySourceHeartbeatController.php`
- `C:\wamp64\www\pbb\support\routes\web.php`
- `C:\wamp64\www\pbb\support\routes\console.php`
- `C:\wamp64\www\pbb\support\database\migrations`
- `C:\wamp64\www\pbb\landing\README.md`
- `C:\wamp64\www\pbb\landing\config\landing.php`
- `C:\wamp64\www\pbb\landing\src\App.php`
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
- `C:\wamp64\www\pbb\account\sdk\php`
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
- `C:\wamp64\www\pbb\natalium\.env.example`
- `C:\wamp64\www\pbb\natalium\config\natalium.php`
- `C:\wamp64\www\pbb\natalium\routes\web.php`
- `C:\wamp64\www\pbb\natalium\routes\api.php`
- `C:\wamp64\www\pbb\natalium\database\migrations`
- `C:\wamp64\www\pbb\natalium\docs\PBB_NATALIUM_IMPLEMENTATION_CHECKLIST.md`
- `C:\wamp64\www\pbb\library\cloud\composer.json`
- `C:\wamp64\www\pbb\library\cloud\routes\web.php`
- `C:\wamp64\www\pbb\library\cloud\database\migrations`
- `C:\wamp64\www\pbb\library\node\composer.json`
- `C:\wamp64\www\pbb\library\node\routes\web.php`
- `C:\wamp64\www\pbb\library\node\routes\api.php`
- `C:\wamp64\www\pbb\library\node\database\migrations`
- `C:\wamp64\www\pbb\learning\composer.json`
- `C:\wamp64\www\pbb\learning\routes\web.php`
- `C:\wamp64\www\pbb\learning\routes\api.php`
- `C:\wamp64\www\pbb\learning\database\migrations`
- `C:\wamp64\www\pbb\learning\docs\PBB_LEARNING_V1_IMPLEMENTATION_STATUS.md`
- `C:\wamp64\www\pbb\utility\README.md`
- `C:\wamp64\www\pbb\utility\.env.example`
- `C:\wamp64\www\pbb\utility\config\vena.php`
- `C:\wamp64\www\pbb\utility\routes\web.php`
- `C:\wamp64\www\pbb\utility\routes\api.php`
- `C:\wamp64\www\pbb\utility\app\Http\Controllers\Api\RelayIncidentHandlerController.php`
- `C:\wamp64\www\pbb\utility\database\migrations`
- `C:\wamp64\www\pbb\utility\tests\Feature\VenaRelayIncidentIntakeTest.php`
- `C:\wamp64\www\pbb\landing\src\Gateway.php`
- `C:\wamp64\www\pbb\landing\src\RegistryStore.php`
- `C:\wamp64\www\pbb\chat\README.md`
- `C:\wamp64\www\pbb\chat\release.json`
- `C:\wamp64\www\pbb\chat\routes\web.php`
- `C:\wamp64\www\pbb\chat\config\chat.php`
- `C:\wamp64\www\pbb\chat\app\Services\ChatRealtimeEventPublisher.php`
- `C:\wamp64\www\pbb\chat\app\Http\Controllers\Api\HotlineEscalationController.php`
- `C:\wamp64\www\pbb\chat\database\migrations`
- `C:\wamp64\www\pbb\games\README.md`
- `C:\wamp64\www\pbb\games\health.php`
- `C:\wamp64\www\pbb\games\config\games.php`
- `C:\wamp64\www\pbb\games\src\ModePolicy.php`
- `C:\wamp64\www\hotline-helpers\js\ui\ui.loader.js`
- `C:\wamp64\www\pbb\documentations\PBB_ACCOUNT_SERVICE_PROPOSAL.md`
- `C:\wamp64\www\pbb\chatviewer\src\ChatRepository.php`
- `C:\wamp64\www\pbb\chatviewer\api\chat-entries.php`
- `C:\wamp64\www\pbb\relay\docs\relay-source-heartbeat-webhooks-implementation-checklist.md`
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
- `C:\wamp64\www\pbb\library\docs\PBB_LIBRARY_DEVELOPMENT_PROPOSAL.md`
- `C:\wamp64\www\pbb\library\node\docs\PBB_LIBRARY_NODE_DEVELOPMENT_PROPOSAL.md`
- `C:\wamp64\www\pbb\library\cloud\docs\PBB_LIBRARY_CLOUD_DEVELOPMENT_PROPOSAL.md`
- `C:\wamp64\www\pbb\learning\docs\PBB_LEARNING_DEVELOPMENT_PROPOSAL_UPDATED.md`
- `C:\wamp64\www\pbb\learning\docs\PBB_LEARNING_PBB_LIBRARY_INTEGRATION_ADDENDUM.md`
