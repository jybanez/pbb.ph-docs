# PBB Selected Apps: Codex Findings

## Reviewed Apps

| App | Path | Reviewed? | Notes |
|---|---|---|---|
| PBB Hotline | `C:\wamp64\www\pbb\hotline` | Yes | Laravel emergency/call/incident app with citizen/operator/command/admin roles, SITREP Relay handoff, Support Request services, and Realtime/MapServer integration |
| PBB Helper | `C:\wamp64\www\hotline-helpers` | Yes | Static JS/CSS UI helper library, not a responder backend |
| PBB Relay | `C:\wamp64\www\pbb\relay` | Yes | Laravel store-and-forward relay with outbox/delivery/inbox/uploads/handlers and relationship resolver |
| PBB Hub / Hub HQ | `C:\wamp64\www\pbb\hub.ph` | Yes | Laravel hub registry, heartbeat, tokens, geodata, recovery |
| PBB Maestro | `C:\wamp64\www\pbb\maestro` | Yes | Laravel worker telemetry monitor; owner clarified observer-only scope |
| PBB Realtime Server | `C:\wamp64\www\pbb\realtime` | Yes | Laravel/Ratchet WebSocket gateway shared by node apps |
| PBB MapServer | `C:\wamp64\www\mapserver` | Yes | Plain PHP tile proxy/cache and boundary overlay server |
| PBB Chatviewer | `C:\wamp64\www\pbb\chatviewer` | Yes | Plain PHP/JS DB-backed agent chat with Markdown fallback, token-authenticated posting/claiming, and newest-first entry query API |
| PBB Kit Setup | `C:\wamp64\www\pbb\kit-setup` | Yes | Electron/PHP/PowerShell setup tool; current local version `0.1.164` bundles Landing, MapServer, Maestro, Realtime, Relay, Hotline, and Support |
| PBB Support System | `C:\wamp64\www\pbb\support` | Yes | Laravel SITREP/support operations app for Relay SITREP intake, support requests, source heartbeat webhooks, Realtime heartbeat publish, and lifecycle updates |
| PBB Landing | `C:\wamp64\www\pbb\landing` | Yes | Lightweight PHP LAN launcher, public hub metadata projection, Kit-managed app registry, and Relay gateway surface |
| PBB Chat | `C:\wamp64\www\pbb\chat` | Yes | Laravel 12 local barangay chat app with rooms, direct conversations, message requests, reports/blocks/moderation, badges, Realtime admission/publishing, and Hotline handoff |
| PBB Games | `C:\wamp64\www\pbb\games` | Yes | Plain PHP optional local citizen engagement/preparedness-learning app with no database and no confirmed operational API calls in version 1 |
| PBB Natalium | `C:\wamp64\www\pbb\natalium` | Yes | Custom PHP/Composer local health-center foundation with Account SSO/admin sync hooks, patient registry, practitioner/capability management, document review, access grants, and audit/search logs |
| PBB Utility / Vena | `C:\wamp64\www\pbb\utility` | Yes | Laravel 12 utility-operator app with roles, assets/teams/settings, MapLibre config, inbound-only Relay intake for `hotline.incident.upserted` targeted to `utility.vena`, normalized incidents, quarantine/stale handling, and operator/responder missions |
| PBB Account | `C:\wamp64\www\pbb\account` | Yes | Laravel 12 local node identity and SSO service with canonical accounts, trusted clients, hashed one-time authorization codes, admin surface, app-admin provisioning hooks, session identity APIs, optional Realtime admission, and PHP SDK |

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
15. PBB Natalium is now reviewed. Code confirms local health registry foundations, Account SSO/admin sync hooks, local document storage, patient/practitioner/capability/access/audit tables, and Relay `public\hub.json` node context.
16. PBB Utility / Vena is now reviewed. Code confirms inbound-only Relay consumption of Hotline incident snapshots targeted to `utility.vena`, with raw retention, normalized incidents, stale update protection, quarantine handling, and metadata-only media refs.
17. Helper active loader cache revisions are `0.21.117`, while Helper `package.json` still reports `0.21.83`.
18. PBB Account is now implementation-confirmed. Code confirms Laravel 12 Account Service behavior: local `pbb_accounts` DB, `accounts`, `trusted_clients`, `sso_authorization_codes`, `account_audit_events`, `/oauth/authorize`, `/oauth/token`, `/oauth/logout`, Account admin APIs, trusted identity APIs, optional Realtime admission, seeded Chat/Hotline/Landing clients, and PHP SDK.

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
  -> custom PHP/Composer app
  -> PBB Account SSO authorize/callback/logout and Account-admin user sync guard
  -> local `pbb_natalium` database for users, capabilities, practitioners, patient applications, patients, access grants, documents, audits, and search logs
  -> local document storage
  -> Relay `public/hub.json` node context read

PBB Utility / Vena
  -> Laravel 12 app with admin/operator/command/responder roles
  -> Relay handler `POST /api/relay/incidents`
  -> accepts `hotline.incident.upserted` targeted to `utility.vena`
  -> stores raw inbound records and normalized utility incidents
  -> rejects/quarantines wrong type, wrong target, missing version, bad identity, stale version, and media refs containing paths/URLs
  -> operator missions and responder mission acceptance
  -> MapLibre config through `/vena-map.json`

PBB Account
  -> central canonical `pbb_user_id`
  -> Laravel 12 app with local `pbb_accounts` database
  -> central password/account status/role/profile ownership
  -> implemented `/oauth/authorize`, `/oauth/token`, `/oauth/logout` SSO flow
  -> trusted client admin and secret rotation
  -> app-admin provisioning bridge
  -> session identity and optional Realtime admission
  -> apps keep local sessions and local user rows linked by `pbb_user_id`

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
- Which consuming apps have completed Account callback/logout/local-user mapping integration beyond the Account Service itself.
- Consuming app local/offline login behavior when Account Service is unavailable.
- Whether Account Service V1 should remain PBB-internal OAuth-like SSO or implement full OIDC later.
- Data retention/encryption policy for incident, media, chat, location, relay payload, support request, and recovery data.

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
| Natalium stores sensitive patient/document/access data locally | PBB Natalium | High | `natalium\database\schema.sql`; `natalium\config\app.php` | Define retention, backup, encryption-at-rest, document access, and audit policy before live health deployment |
| Natalium Account-admin sync token controls local user provisioning | PBB Natalium, PBB Account | High | `natalium\config\app.php`; `natalium\routes\api.php`; `natalium\bin\test.php` | Keep disabled unless needed, generate per-node token, rotate on compromise |
| Vena retains raw Relay incident payloads | PBB Utility / Vena, Relay, Hotline | High | `utility\app\Http\Controllers\Api\RelayIncidentHandlerController.php`; `utility\database\migrations` | Define retention, audit access, purge policy, and local backup encryption |
| Vena incident media refs must remain metadata-only | PBB Utility / Vena, Hotline, Relay | High | `utility\tests\Feature\VenaRelayIncidentIntakeTest.php`; `RelayIncidentHandlerController.php` | Keep rejecting direct URLs/paths and document proper backend media retrieval |
| Vena Relay handler token controls incident ingestion | PBB Utility / Vena, Relay | High | `utility\.env.example`; `RelayIncidentHandlerController.php` | Generate per-node/per-source token and rotate on compromise |
| Account Service is the central credential authority | PBB Account, Chat, Hotline, Landing, Natalium, future apps | High | `account\database\migrations`; `account\app\Http\Controllers\OAuthController.php`; `account\app\Models\TrustedClient.php` | Rotate seeded dev credentials, restrict admin access, keep hashed secrets/codes, and define backup/encryption/retention policy before rollout |
| Account seeded development credentials exist | PBB Account | High if reused outside dev | `account\README.md`; `account\database\seeders\DatabaseSeeder.php` | Rotate/remove seeded test account and `*-dev-secret` clients before shared deployment |
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
| PBB Account consuming-app integration matrix | PBB Account, Chat, Hotline, Landing, Natalium, Support, Maestro | Account implementation exists; docs should track which apps have callback/logout/session mapping and which remain pending |

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
| Document Account consuming-app integration status and rotate seeded dev credentials | Account exists; deployment risk is now app adoption, secrets, and local/offline behavior | P1 |
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
- `C:\wamp64\www\pbb\natalium\composer.json`
- `C:\wamp64\www\pbb\natalium\config\app.php`
- `C:\wamp64\www\pbb\natalium\routes\api.php`
- `C:\wamp64\www\pbb\natalium\database\schema.sql`
- `C:\wamp64\www\pbb\natalium\bin\test.php`
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
