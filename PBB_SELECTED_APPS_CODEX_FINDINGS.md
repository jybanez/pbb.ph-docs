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
| PBB Account | `C:\wamp64\www\pbb\account` | No app files found | Folder exists, but no code/config/routes/package files were found during scan; behavior is Unknown / Not confirmed from code |

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
15. Helper active loader cache revisions are `0.21.117`, while Helper `package.json` still reports `0.21.83`.

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
- Whether `C:\wamp64\www\pbb\account` is intended to become a shared account/identity app.
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
| PBB Account intent | Future identity/account app, Chat | Folder exists but no implementation was found; shared account behavior should not be inferred |

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
- `C:\wamp64\www\pbb\chatviewer\src\ChatRepository.php`
- `C:\wamp64\www\pbb\chatviewer\api\chat-entries.php`
- `C:\wamp64\www\pbb\relay\docs\relay-source-heartbeat-webhooks-implementation-checklist.md`
