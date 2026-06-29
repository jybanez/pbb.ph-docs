# PBB Account V1 Implementation Plan

Status: Implementation plan based on revised Account proposal and checklist as of 2026-06-29.

## 1. V1 Decision

PBB Account V1 is a local-node identity service.

```text
Each PBB node has its own local Account Service.
Account owns local-node identity and credentials.
Apps own their own sessions, roles, permissions, and domain records.
```

HQ is not the V1 login authority for local apps.

## 2. Evidence

- `C:\wamp64\www\pbb\documentations\PBB_ACCOUNT_SERVICE_REVISED_PROPOSAL.md`
- `C:\wamp64\www\pbb\documentations\PBB_ACCOUNT_SERVICE_IMPLEMENTATION_CHECKLIST.md`
- `C:\wamp64\www\pbb\documentations\PBB_ACCOUNT_SERVICE_PROPOSAL.md`
- `C:\wamp64\www\pbb\chat\release.json`
- `C:\wamp64\www\pbb\chat\app\Http\Controllers\Api\HotlineEscalationController.php`

## 3. Target Deployment

| Area | V1 Decision |
|---|---|
| App path | `C:\wamp64\www\pbb\account` |
| Local domain | `https://account.pbb.ph` |
| Framework | Laravel app |
| Database | Local MySQL |
| Database name | `pbb_accounts` |
| Installer owner | Kit Setup in a later packaging phase |
| DNS | Technitium local DNS |
| Sessions | App-local sessions remain separate |
| Roles | App-local roles remain app-owned |

## 4. Identity Model

V1 canonical ID:

```text
pbb_user_id: ULID string
storage: CHAR(26) or VARCHAR(26)
scope: canonical within one local PBB node
```

V1 must not assume cross-node identity federation.

## 5. Core Tables

| Table | Purpose |
|---|---|
| `accounts` | Canonical local-node account, password hash, status, verified contacts |
| `account_login_identifiers` | Optional multi-identifier support |
| `trusted_clients` | Chat, Hotline, Support, Landing, Maestro clients |
| `sso_authorization_codes` | Short-lived one-time code exchange |
| `account_audit_events` | Login, failure, SSO issue/consume, profile/status/password events |

## 6. V1 API Surface

Browser:

```text
GET /oauth/authorize
POST /login
POST /logout
GET /profile
PATCH /profile
```

Trusted app server-to-server:

```text
POST /oauth/token
GET /api/accounts/{pbb_user_id}
POST /api/accounts/lookup
POST /api/accounts/sync
```

Exact routes can change during implementation, but the contract must preserve:

- exact redirect URI matching
- short-lived one-time authorization code
- hashed authorization code storage
- hashed client secret storage
- app-local session creation after token exchange

## 7. First Integration Milestone

Proof point:

```text
Citizen logs in once through Account from Chat
-> Chat creates local session and local user linked to pbb_user_id
-> Citizen clicks Hotline from Chat
-> Hotline opens without another password prompt
-> Hotline creates local session and local citizen linked to same pbb_user_id
```

## 8. Implementation Phases

### Phase 1: Scaffold

- Scaffold Laravel app under `C:\wamp64\www\pbb\account`.
- Configure `.env`.
- Configure `pbb_accounts` database.
- Configure local vhost and DNS for `account.pbb.ph`.
- Add health endpoint.
- Add release metadata for Kit/Landing later.

### Phase 2: Core Identity

- Create `accounts` model/migration.
- Generate `pbb_user_id` as ULID.
- Add password hashing.
- Add status: pending, active, suspended, disabled.
- Add verified email/mobile fields.
- Add audit events.

### Phase 3: Trusted Clients And SSO

- Add `trusted_clients`.
- Seed Chat and Hotline clients.
- Implement `/oauth/authorize`.
- Implement `/oauth/token`.
- Store only `code_hash` and `client_secret_hash`.
- Add tests for expiry, one-time use, redirect URI mismatch, disabled accounts.

### Phase 4: Chat Integration

- Add `pbb_user_id` to Chat users.
- Add Account login redirect/callback.
- Exchange code server-to-server.
- Find/create Chat user.
- Keep Chat local session.
- Keep current Chat login behind feature flag during migration.

### Phase 5: Hotline Integration

- Add `pbb_user_id` to Hotline users.
- Add Account callback.
- Find/create Hotline citizen.
- Update Chat Hotline button/handoff to use Account authorize flow.
- Keep current Hotline login during transition.

### Phase 6: Profile Sync

- Centralize name/mobile/email/avatar updates.
- Keep app-only fields local.
- Add app profile refresh service.
- Define conflict and verification behavior.

### Phase 7: Packaging

- Add Kit Setup package metadata.
- Add Landing launcher metadata if needed.
- Add service/health checks.
- Add installer validation.

## 9. Security Requirements

- Account owns password hashes; apps never receive password hashes.
- Authorization codes are one-time, short-lived, and stored hashed.
- Client secrets are stored hashed.
- Redirect URIs are exact-match.
- Suspended/disabled accounts cannot receive new app sessions.
- Audit login success/failure, SSO issue/consume, password reset, profile update, account status changes.
- Use HTTPS locally where practical.
- Do not place long-lived credentials in URLs.

## 10. Open Decisions

- Development HTTPS certificate approach.
- Whether Mail/OTP is stubbed in V1.
- Initial seeded admin/test citizen.
- Final Chat callback URL.
- Final Hotline callback URL.
- Whether V1 is PBB-internal OAuth-like flow or full OIDC later.
- Behavior when local Account Service is unavailable after an app already has a local session.

