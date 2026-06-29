# PBB Account Service Revised Proposal

**Project:** Project Bantay Bayan (PBB)  
**Module:** Account Service / Local Node Identity  
**Date:** 2026-06-29  
**Status:** Revised implementation proposal  

---

## 1. Executive Summary

PBB should introduce an Account Service on every local PBB node. The Account Service is the local credential and identity authority for that node's apps, including Chat, Hotline, Support, Landing, Maestro, and future local apps.

The recommended V1 model is:

```text
Each PBB node has its own local Account Service.
Account owns local-node identity and credentials.
Apps own their own sessions, roles, and domain records.
```

This means Account Service is not an HQ/cloud login dependency. HQ remains a cloud or upper-level PBB entity for registry, topology, coordination, and future upstream contracts. Local user login should continue to work as long as the local node, local database, and local Account Service are running.

## 2. Core Decision

Account Service will be installed per node, most likely under:

```text
C:\wamp64\www\pbb\account
```

It will serve the node's local app domains, such as Chat and Hotline, through local DNS/vhost configuration managed by Kit Setup.

Account Service is the source of truth for:

- Local node canonical `pbb_user_id`
- Login credentials
- Password reset
- Account status
- Verified email/mobile ownership
- Global display name and optional avatar
- Local-node SSO authorization
- Identity/security audit events

Each app remains the source of truth for:

- App-local user row
- App-local session
- App-local role and permissions
- App-specific profile fields
- App-specific moderation state
- Domain records such as chat messages, incidents, SITREPs, support requests, assignments, media, and settings

## 3. Identity Scope

For V1, `pbb_user_id` is canonical within the local node.

Recommended type:

```text
ULID string
```

Recommended storage:

```text
CHAR(26) or VARCHAR(26)
```

ULID keeps IDs sortable, portable across Laravel apps, and practically safe for future federation if PBB later decides to synchronize selected identities across nodes. However, V1 should not assume cross-node identity sync.

Important V1 rule:

```text
pbb_user_id identifies one person within one local PBB node.
Cross-node identity federation is a future design, not part of V1.
```

## 4. Deployment Model

Account Service should be a first-class local PBB app.

Expected runtime:

- Laravel app
- Local MySQL database
- WAMP/Apache vhost
- Local DNS entry through Technitium
- Installed and configured by Kit Setup in a later packaging phase
- Local HTTPS where available

Confirmed local domain:

```text
https://account.pbb.ph
```

Confirmed development database:

```text
DB_HOST=127.0.0.1
DB_DATABASE=pbb_accounts
DB_USERNAME=root
DB_PASSWORD=
```

Account Service should not depend on HQ availability for normal local login.

## 5. Availability And Offline Behavior

Because Account Service is local per node, the normal offline behavior is:

- If the local node is running, users can authenticate locally.
- If the internet or HQ is unavailable, local SSO still works.
- If Account Service is down, new SSO logins are unavailable.
- Existing app-local sessions may continue until their normal expiry.
- Profile edits, password changes, password resets, and new app login are unavailable while Account Service is down.

V1 should not implement cached-password fallback inside each app. That would reintroduce multiple credential authorities and weaken the main design.

Suggested V1 policy:

```text
Existing app sessions continue locally.
New login requires local Account Service.
Apps do not keep independent password login for migrated users after cutover.
```

During migration, app-local login can remain behind a feature flag until Account Service is proven.

## 6. Recommended Architecture

### 6.1 Authentication Flow

Apps authenticate users through Account Service using a local OAuth-like authorization-code flow.

V1 does not need full OpenID Connect. It should preserve the important security properties:

- Short-lived authorization codes
- One-time code consumption
- Hashed authorization code storage
- Hashed client secret storage
- Exact redirect URI matching
- App-to-Account token exchange over server-side HTTP
- Account status check during every token exchange
- Audit events for login and SSO actions

### 6.2 Session Model

Apps must keep separate sessions.

Examples:

```text
Chat    -> pbb_chat_session
Hotline -> hotline app session
Support -> support app session
Account -> account service session
```

Do not use one shared `.pbb.ph` session cookie.

### 6.3 Local User Mapping

Every integrated app adds:

```text
users.pbb_user_id
```

Suggested migration rule:

```text
If local user exists by pbb_user_id:
    update cached identity fields.
Else if migration mapping safely matches by verified mobile/email:
    attach pbb_user_id to existing local user.
Else:
    create a local user with default app role/profile.
```

## 7. V1 Data Model

### 7.1 `accounts`

Canonical local-node account.

Suggested fields:

- `id`
- `pbb_user_id`
- `name`
- `email`
- `mobile`
- `password_hash`
- `status`
- `email_verified_at`
- `mobile_verified_at`
- `avatar_url`
- `last_login_at`
- `created_at`
- `updated_at`

Recommended constraints:

- Unique `pbb_user_id`
- Unique normalized email when present
- Unique normalized mobile when present
- At least one login identifier required for normal password login

### 7.2 `account_login_identifiers`

Optional V1 table if multiple identifiers per account are needed.

Suggested fields:

- `id`
- `pbb_user_id`
- `type`
- `value`
- `normalized_value`
- `verified_at`
- `created_at`
- `updated_at`

### 7.3 `trusted_clients`

Registered local PBB apps.

Suggested fields:

- `id`
- `client_id`
- `client_secret_hash`
- `name`
- `allowed_redirect_uris_json`
- `allowed_scopes_json`
- `status`
- `created_at`
- `updated_at`

Initial trusted clients:

- `pbb-chat`
- `pbb-hotline`

Later clients:

- `pbb-support`
- `pbb-landing`
- `pbb-maestro`

### 7.4 `sso_authorization_codes`

Short-lived one-time authorization codes.

Suggested fields:

- `id`
- `code_hash`
- `pbb_user_id`
- `client_id`
- `redirect_uri`
- `scopes_json`
- `state_hash` if needed
- `expires_at`
- `consumed_at`
- `created_at`

### 7.5 `account_audit_events`

Identity/security audit trail.

Suggested fields:

- `id`
- `pbb_user_id`
- `event_type`
- `actor_pbb_user_id`
- `client_id`
- `ip_address`
- `user_agent`
- `metadata_json`
- `created_at`

## 8. API Surface

### 8.1 Browser SSO

```http
GET /oauth/authorize
```

Expected query parameters:

- `client_id`
- `redirect_uri`
- `response_type=code`
- `scope`
- `state`

```http
POST /oauth/token
```

Expected body:

- `grant_type=authorization_code`
- `code`
- `client_id`
- `client_secret`
- `redirect_uri`

Suggested response:

```json
{
  "token_type": "Bearer",
  "expires_in": 300,
  "user": {
    "pbb_user_id": "01J...",
    "name": "Juan Dela Cruz",
    "email": "juan@example.test",
    "mobile": "09170000000",
    "status": "active"
  }
}
```

For V1, returning signed JSON or a signed short-lived identity assertion is acceptable if code exchange security is enforced.

### 8.2 Account Profile

```http
GET /api/account/me
PATCH /api/account/me
```

Patchable central identity fields:

- `name`
- `email`
- `mobile`
- `avatar`

Email/mobile changes should not become trusted identifiers until verified.

### 8.3 Trusted App Server API

```http
GET /api/accounts/{pbb_user_id}
POST /api/accounts/lookup
POST /api/accounts/sync
```

These endpoints must require trusted app authentication.

## 9. First Milestone

The first milestone should prove local-node SSO between Chat and Hotline.

Target flow:

```text
Citizen opens Chat.
Chat redirects to local Account Service.
Citizen logs in once.
Account redirects back to Chat with a code.
Chat exchanges code server-to-server.
Chat creates local session and local user linked by pbb_user_id.
Citizen clicks Hotline icon.
Chat routes browser through Account authorization for Hotline.
Account sees existing local Account session.
Account issues Hotline authorization code.
Hotline exchanges code server-to-server.
Hotline creates local session and citizen user linked by same pbb_user_id.
Citizen lands in Hotline without another password prompt.
```

## 10. Chat To Hotline Handoff

Current Chat has:

```text
POST /api/chat/escalate-to-hotline
```

This should evolve to return or redirect to:

```text
https://account.pbb.ph/oauth/authorize?client_id=pbb-hotline&...
```

Do not include chat message bodies by default.

Allowed handoff metadata for V1:

- `source_app=chat`
- `destination=hotline.citizen`
- `reason=emergency_escalation`
- Optional safe reference ID if later needed

## 11. Security Requirements

Minimum V1 requirements:

- Password hashes stay private to Account Service.
- Authorization code values are stored hashed.
- Authorization codes expire quickly and are one-time use.
- Client secrets are stored hashed.
- Redirect URIs are pre-registered and exact matched.
- Trusted app APIs require app authentication.
- Account status is checked during every SSO exchange.
- Suspended or disabled users cannot receive new app sessions.
- Login, login failure, SSO issue, SSO consume, password reset, profile update, and status changes are audited.
- Login and token endpoints have rate limits.
- Deployed local app traffic should use HTTPS when available.
- Long-lived credentials are never passed in query strings.

## 12. Migration Strategy

Migration should be app-by-app.

Recommended order:

1. Account Service skeleton
2. Chat SSO integration
3. Hotline citizen SSO integration
4. Chat-to-Hotline SSO handoff
5. Profile sync
6. Password cutover
7. Support and other staff apps
8. Kit Setup packaging and smoke checks

During early migration:

- Keep app-local login behind feature flags.
- Do not remove existing login until SSO is tested.
- Match existing users conservatively.
- Prefer verified mobile over email for citizen matching.
- Keep manual admin mapping available for conflicts.

## 13. Kit Setup Requirements

Account Service should eventually be added to Kit Setup as a first-class package.

Kit Setup should handle:

- App package installation
- `.env` generation
- App key generation
- Database creation/migration
- Local DNS/vhost config
- HTTPS/certificate config where applicable
- Trusted client seeding for Chat and Hotline
- Service/smoke checks
- Update policy using additive app-owned migrations

Initial smoke checks:

- Account health endpoint responds
- Database connection works
- Trusted clients exist
- `/oauth/authorize` renders or redirects correctly
- `/oauth/token` rejects invalid client secret
- `/oauth/token` rejects invalid redirect URI

## 14. Open Decisions

These should be finalized before or during initial implementation:

| Decision | Recommendation |
|---|---|
| Local app path | `C:\wamp64\www\pbb\account` |
| Local domain | `https://account.pbb.ph` |
| `pbb_user_id` type | ULID |
| Citizen primary identifier | Mobile |
| Citizen email | Optional |
| V1 protocol | PBB-internal OAuth-like authorization code flow |
| Full OIDC | Later only if needed |
| App roles | Keep local in V1 |
| Existing sessions when Account is down | Continue until normal expiry |
| New login when Account is down | Blocked until Account recovers |
| Cross-node identity | Future design, not V1 |

## 15. Acceptance Criteria

The initial implementation is accepted when:

- Account Service runs locally on the node.
- A citizen can log in through Account Service from Chat.
- Chat creates or updates a local user linked to `pbb_user_id`.
- Chat creates its own local app session.
- The same citizen can open Hotline from Chat without another password prompt.
- Hotline creates or updates a local citizen user linked to the same `pbb_user_id`.
- Hotline creates its own local app session.
- Chat and Hotline sessions remain separate.
- Disabled/suspended Account users cannot receive new Chat or Hotline sessions.
- Authorization codes expire and cannot be reused.
- Redirect URI validation is exact.
- Client secrets and authorization codes are stored hashed.
- Identity/security audit events are written.
