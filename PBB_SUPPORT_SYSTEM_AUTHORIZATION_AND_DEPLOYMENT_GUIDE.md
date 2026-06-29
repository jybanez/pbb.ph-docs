# Support System Authorization and Deployment Guide

Status: Draft guide based on current Support System code as of 2026-06-29.

## 1. Purpose

PBB Support System is the support/SITREP operations app. It receives Relay-delivered SITREPs and Hotline support requests, stages/consolidates SITREPs, displays current support context, processes support request lifecycle actions, receives Relay source-heartbeat webhooks, and publishes heartbeat snapshots to Realtime.

## 2. Evidence

- `C:\wamp64\www\pbb\support\routes\web.php`
- `C:\wamp64\www\pbb\support\routes\api.php`
- `C:\wamp64\www\pbb\support\README.md`
- `C:\wamp64\www\pbb\support\.env.example`
- `C:\wamp64\www\pbb\support\database\migrations`
- `C:\wamp64\www\pbb\support\app\Http\Controllers\Api\RelaySourceHeartbeatController.php`
- `C:\wamp64\www\pbb\support\app\Http\Controllers\Api\SupportRequestsController.php`
- `C:\wamp64\www\pbb\support\app\Support\Settings\SupportSettings.php`

## 3. Deployment Placement

Confirmed code supports a local Laravel deployment with MySQL and queues.

Operational placement still needs owner decision:

- upper-level support node
- city/municipality node
- province node
- HQ-side support deployment
- possibly barangay node kits if support handling is needed locally

Use `Unknown / Not confirmed from code` for final production placement until explicitly decided.

## 4. Runtime Requirements

| Requirement | Notes |
|---|---|
| PHP/Laravel | Laravel 12 app |
| MySQL | `pbb_support` database in local docs/briefing |
| Queue worker | Required for outbound SITREP/support lifecycle jobs |
| Scheduler | Used for periodic relay/latest SITREP tasks |
| Relay connectivity | Required for inbound/outbound Relay flows |
| Realtime connectivity | Required for source heartbeat snapshot publishing |
| MapServer URLs | Used by support map/boundary views |

## 5. User Authorization

Current browser app routes:

- `POST /api/login` is public with throttle.
- `/api/user`, `/api/admin/users`, `/api/settings`, `/api/support-requests`, `/api/sitreps/current`, `/api/source-heartbeats`, `/api/logout`, and `/api/session/ping` are under Laravel `auth` middleware.
- Sitrep media web routes are under `auth`.

Risk:

```text
Route-level role middleware for admin/users/settings was not confirmed from code.
```

Required next control:

- Add or document admin/role middleware for `/api/admin/users` and `/api/settings`.
- Separate support operator capabilities from support admin capabilities.
- Keep default/dev credentials out of shared deployment.

## 6. Machine Authorization

Support API routes in `routes\api.php`:

| Endpoint | Purpose | Required Auth |
|---|---|---|
| `POST /api/relay/sitreps` | Inbound Relay SITREP handler | Relay handler token in controller |
| `POST /api/relay/support-requests` | Inbound Hotline support request handler | Relay handler token in controller |
| `POST /api/relay/support-request-lifecycle` | Inbound lifecycle/cancel handler | Relay handler token in controller |
| `POST /api/relay/source-heartbeats` | Relay operational heartbeat webhook | Bearer or `X-Relay-Webhook-Key` token |

All machine tokens must be generated per node and stored server-side only.

## 7. Deployment Checklist

1. Configure `.env`.
2. Configure local database.
3. Run migrations only during controlled install/update.
4. Configure Relay URL and outbound Relay client token.
5. Configure inbound handler tokens.
6. Configure source heartbeat webhook token.
7. Configure MapServer URLs.
8. Configure Realtime URL/signing/backend secrets.
9. Seed or create initial support admin.
10. Change any default/local-development password before shared deployment.
11. Start queue worker and scheduler as Windows services or managed processes.
12. Verify inbound handler health using Relay test delivery.
13. Verify source heartbeat webhook delivery.
14. Verify Realtime heartbeat admission and publish.
15. Verify support request lifecycle callback reaches Hotline.

## 8. Support Request Permissions

Current authenticated support actions:

- list/show support requests
- receive
- accept
- reject
- assign
- mark en route
- complete

Recommended V1 roles:

| Role | Capabilities |
|---|---|
| support_operator | View SITREPs/source heartbeats/support requests; perform lifecycle actions |
| support_admin | Operator capabilities plus users/settings/token configuration |
| machine_relay_handler | Inbound Relay handler only; no browser session |

Implementation not confirmed from code. Add middleware/policies before shared deployment.

## 9. Security Notes

| Risk | Severity | Required Control |
|---|---|---|
| Default admin credentials retained | High | Force password change or disable seed in shared deployment |
| Admin/settings auth-only without role checks | High | Add role middleware/policies |
| Handler token leak | High | Rotate token, redact logs, store encrypted |
| Source heartbeat spoofing | High | Dedicated token and event dedupe |
| Sensitive SITREP/media/support data | High | Retention, backup, and access policy |

## 10. Unknowns / Open Decisions

- Final production placement of Support.
- Final role model and middleware names.
- Token rotation UI or CLI.
- Retention period for SITREPs, support requests, media manifests, and heartbeat history.
- Whether Account Service will replace Support's local login in V1 or later.

