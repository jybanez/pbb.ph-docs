# Relay / Hotline / Support Runtime Contract

Status: Draft runtime contract based on local code, Kit Setup contract docs, and selected-app briefing as of 2026-06-29.

## 1. Purpose

This document defines how Hotline, Relay, and Support System communicate at runtime for SITREPs, support requests, support lifecycle updates, and Relay source heartbeat telemetry.

Important design rule:

```text
Incidents are not synced as editable records across nodes.
Nodes consolidate SITREPs from sources plus their own SITREP and send periodic SITREPs upstream.
```

## 2. Evidence

- `C:\wamp64\www\pbb\kit-setup\docs\relay-hotline-support-data-prep-contract.md`
- `C:\wamp64\www\pbb\hotline\app\Support\SupportRequests`
- `C:\wamp64\www\pbb\hotline\app\Support\Sitreps`
- `C:\wamp64\www\pbb\support\routes\api.php`
- `C:\wamp64\www\pbb\support\app\Http\Controllers\Api\RelaySitrepHandlerController.php`
- `C:\wamp64\www\pbb\support\app\Http\Controllers\Api\RelaySupportRequestHandlerController.php`
- `C:\wamp64\www\pbb\support\app\Http\Controllers\Api\RelaySupportRequestLifecycleHandlerController.php`
- `C:\wamp64\www\pbb\support\app\Http\Controllers\Api\RelaySourceHeartbeatController.php`
- `C:\wamp64\www\pbb\relay\tools\data-prep\apply-settings.php`

## 3. Machine Identities

Kit Setup Data Prep should seed/reconcile these logical Relay client identities:

| App | Display Name | `system_code` | Purpose |
|---|---|---|---|
| Hotline | `Hotline` | `hotline.command` | Support request producer and Hotline command surface |
| Support | `Support SITREP Ingestor` | `sitrep.ingestor` | Receives `sitrep.record` messages |
| Support | `Support Dispatch` | `support.dispatch` | Receives support request messages and sends lifecycle callbacks |

Hotline may still use one authenticated Relay client row with `source_systems=["sitrep.app","hotline.command"]`, but message contracts must remain distinct.

## 4. Message Types And Endpoints

| Flow | Source System | Target System | Message Type | Handler Endpoint |
|---|---|---|---|---|
| Hotline SITREP | `sitrep.app` | `sitrep.ingestor` | `sitrep.record` | Support `POST /api/relay/sitreps` |
| Hotline support request | `hotline.command` | `support.dispatch` | `support.request` | Support `POST /api/relay/support-requests` |
| Hotline support cancellation | `hotline.command` | `support.dispatch` | `support.request.cancelled` | Support `POST /api/relay/support-request-lifecycle` |
| Support lifecycle callback | `support.dispatch` | Hotline handler | `support.request.*` | Hotline internal support-request update ingress |
| Relay source heartbeat | Relay operational webhook | Support webhook receiver | `source.heartbeat.updated` | Support `POST /api/relay/source-heartbeats` |

## 5. Support Lifecycle Events

Confirmed lifecycle events that should be used:

- `support.request`
- `support.request.received`
- `support.request.under_review`
- `support.request.accepted`
- `support.request.rejected`
- `support.request.assigned`
- `support.request.en_route`
- `support.request.fulfilled`
- `support.request.closed`
- `support.request.cancelled`

Do not emit `support.request.completed` as an outbound Relay event. Support UI may have a local `complete` action, but the outbound lifecycle event is `support.request.fulfilled`.

## 6. Runtime Data Flow

SITREP handoff:

```text
Hotline command/SITREP state
-> Hotline SITREP relay outbox/delivery service
-> Relay local `/api/v1/messages`
-> Relay delivery queue
-> Support `/api/relay/sitreps`
-> Support latest-source staging
-> Support consolidated current SITREP
-> optional upstream Support/Relay SITREP submission
```

Support request flow:

```text
Hotline support request
-> Hotline support request relay submission service
-> Relay local `/api/v1/messages`
-> Relay delivery queue
-> Support `/api/relay/support-requests`
-> Support operator receive/accept/reject/assign/en-route/complete actions
-> Support lifecycle relay service
-> Relay
-> Hotline internal support-request update ingress
```

Source heartbeat webhook flow:

```text
Relay source heartbeat status changes
-> relay_webhook_subscribers
-> relay_webhook_deliveries
-> Support `/api/relay/source-heartbeats`
-> token validation
-> event_id dedupe
-> Support source heartbeat storage
-> Realtime heartbeat snapshot publish
```

## 7. Authentication And Tokens

| Boundary | Auth Method | Notes |
|---|---|---|
| Local app -> Relay client API | Relay client token/key | Token owned by Relay/Data Prep |
| Relay -> Support handlers | Handler bearer/header token | Support rejects invalid handler tokens |
| Relay source heartbeat -> Support | Bearer or `X-Relay-Webhook-Key` | Dedicated Support source-heartbeat webhook token |
| Support -> Relay outbound | Relay client token | Stored in Support settings |
| Support -> Hotline lifecycle callback | Hotline handler token | Hotline accepts configured internal relay token/header |

All tokens must be generated per node, masked in diagnostics, stored server-side, and rotated on compromise.

## 8. Queues, Retry, And Idempotency

Confirmed:

- Relay stores messages, deliveries, receipts, handler dispatches, and webhook deliveries in its database.
- Relay delivery/handler processing uses queue workers and retry/backoff.
- Support receives source-heartbeat events with `event_id` dedupe behavior.
- Support has database queue usage for SITREP relay delivery and support lifecycle delivery.

Required:

- Envelope IDs and correlation IDs must be stable for retry.
- Handlers must be idempotent.
- Retry failures must be visible in Relay/Support operations surfaces.

## 9. Failure Behavior

| Failure | Expected Behavior |
|---|---|
| Relay unavailable from Hotline | Hotline keeps local records; outbound sync waits/fails locally until Relay returns |
| Support unavailable | Relay delivery retries; Hotline local incident handling continues |
| Upstream unavailable | Local node continues; Relay queues delivery |
| Duplicate handler delivery | Handler should dedupe by event/message/correlation IDs |
| Source heartbeat webhook delivery fails | Relay tracks `relay_webhook_deliveries`; Support dashboard may show stale heartbeat state |

## 10. Open Decisions

- Final production SITREP cadence and retention.
- Final exact SITREP envelope payload schema.
- Whether app endpoint metadata such as `apps.hotline.base_url` belongs in Hub, Relay, or Landing topology.
- Operational UI for retry/dead-letter inspection.
- Exact token rotation runbook.

