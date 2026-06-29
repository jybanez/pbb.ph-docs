# PBB Account Service Proposal

**Project:** Project Bantay Bayan (PBB)  
**Module:** Account Service / Central Identity  
**Date:** 2026-06-29  
**Status:** Draft proposal for implementation planning  

---

## 1. Executive Summary

PBB should introduce a central Account Service that owns canonical user identity across PBB apps. Chat, Hotline, Support, Maestro, Landing, and future apps should no longer maintain independent password-backed identities for the same person.

The recommended model is:

```text
Centralize identity and credentials.
Keep app sessions and app-specific data local.
```

The Account Service should own registration, password verification, password reset, account status, verified contact details, and global identity fields. Each PBB app should keep its own local user/profile row linked by a stable `pbb_user_id`.

This enables single sign-on between PBB apps without sharing session cookies or directly sharing a mutable `users` table.

## 2. Problem

Current PBB apps can each have their own users, sessions, roles, and passwords. This creates several operational problems:

- Citizens may need to log in separately to Chat and Hotline.
- A user can change details in one app without other apps seeing the update.
- Password reset and account recovery can diverge by app.
- Blocking or disabling an account in one app may not affect others.
- Cross-app workflows, such as Chat to Hotline escalation, need a trusted identity bridge.
- Future MFA, OTP, verification, and audit policies would need to be duplicated.

PBB needs one source of truth for "who this person is" while still letting each app own its domain data.

## 3. Goals

- Provide one canonical PBB user identity for citizens, operators, command users, admins, and support users.
- Centralize password storage and authentication.
- Support single sign-on across PBB apps.
- Let apps create local sessions after Account Service authentication.
- Let apps keep local domain-specific user records linked to `pbb_user_id`.
- Support Chat to Hotline auto-login for active citizens.
- Support central profile updates for identity fields such as name, mobile, and email.
- Keep app-specific permissions, operational roles, and domain records in the owning app unless intentionally centralized later.
- Provide auditable account lifecycle events.
- Allow phased migration from existing app-owned users.

## 4. Non-Goals

- Do not make every app directly read and write the Account Service database.
- Do not share one Laravel session cookie across all PBB apps.
- Do not move Chat messages, Hotline incidents, Support requests, or app-specific operational records into Account Service.
- Do not require every app role to become global on day one.
- Do not force a big-bang migration of existing users.
- Do not store plaintext passwords or expose password hashes through APIs.

## 5. Recommended Architecture

### 5.1 Service Boundary

The Account Service owns:

- Canonical user ID: `pbb_user_id`
- Login identifiers: email, mobile, optional username
- Password hash
- Password reset flow
- Account status: active, pending, suspended, disabled
- Verification state: email verified, mobile verified
- Global display name
- Optional global avatar
- Central audit log for identity/security events
- SSO authorization codes and token exchange
- Optional MFA/OTP in a later phase

Apps own:

- Local app user row linked by `pbb_user_id`
- App-specific role and permissions
- App-specific preferences
- App-specific moderation state
- Domain records such as chat messages, incidents, call sessions, support requests, assignments, and SITREPs
- App-local Laravel sessions

### 5.2 Identity Model

Every user should have a globally stable ID:

```text
pbb_user_id: UUID or ULID
```

Each app keeps a local mapping:

```text
local_users.id
local_users.pbb_user_id
```

The local app user row can cache name, email, mobile, and avatar for display, but Account Service remains authoritative for those fields.

### 5.3 Session Model

Each app should keep its own session cookie:

- Chat keeps `pbb_chat_session`.
- Hotline keeps its own host-only session.
- Other apps keep their own app session.

The Account Service should not force a shared `.pbb.ph` session cookie across all apps. Shared cookies increase coupling and make logout, session expiry, and security controls harder to reason about.

Instead, Account Service provides SSO. Apps authenticate through Account Service and then create their own local sessions.

## 6. User Experience Flow

### 6.1 First Login Through Chat

1. Citizen opens `chat.pbb.ph`.
2. Chat checks for an active Chat session.
3. If no session exists, Chat sends the citizen to Account Service login.
4. Citizen enters mobile/email and password.
5. Account Service verifies credentials and account status.
6. Account Service creates its own central login session.
7. Account Service redirects back to Chat with a short-lived authorization code.
8. Chat exchanges the code server-to-server.
9. Account Service returns the canonical user identity.
10. Chat finds or creates a local Chat user linked to `pbb_user_id`.
11. Chat starts a local Chat session.
12. Citizen lands in Chat.

### 6.2 Chat to Hotline Auto-Login

1. Citizen is active in Chat.
2. Citizen clicks the Hotline icon.
3. Chat redirects the citizen through Account Service for Hotline authorization.
4. Account Service sees the citizen already has a central login session.
5. Account Service does not ask for the password again.
6. Account Service redirects to Hotline with a short-lived authorization code.
7. Hotline exchanges the code server-to-server.
8. Account Service returns `pbb_user_id` and identity claims.
9. Hotline finds or creates a local Hotline citizen linked to `pbb_user_id`.
10. Hotline starts its own local Hotline session.
11. Citizen lands in `/citizen`.

### 6.3 Profile Update From Hotline

1. Citizen edits profile in Hotline.
2. Hotline separates central identity fields from Hotline-only fields.
3. Hotline sends identity fields to Account Service.
4. Account Service validates the update.
5. If mobile/email changes, Account Service may require verification.
6. Account Service saves the canonical profile.
7. Hotline refreshes its local cached copy.
8. Other apps see the update through profile refresh, SSO claims, webhook, or scheduled sync.

## 7. Proposed Core Data Model

### 7.1 `accounts`

Canonical user account.

Suggested fields:

- `id` - local database primary key
- `pbb_user_id` - globally stable UUID or ULID, unique
- `name`
- `email` - nullable, unique when present
- `mobile` - nullable, unique when present
- `password_hash`
- `status` - pending, active, suspended, disabled
- `email_verified_at`
- `mobile_verified_at`
- `avatar_url` - nullable
- `last_login_at`
- `created_at`
- `updated_at`

### 7.2 `account_login_identifiers`

Optional table if multiple identifiers per user are needed.

- `id`
- `pbb_user_id`
- `type` - email, mobile, username
- `value`
- `normalized_value`
- `verified_at`
- `created_at`
- `updated_at`

### 7.3 `sso_authorization_codes`

Short-lived one-time codes used by apps.

- `id`
- `code_hash`
- `pbb_user_id`
- `client_id`
- `redirect_uri`
- `scopes_json`
- `expires_at`
- `consumed_at`
- `created_at`

Store only a hash of the code.

### 7.4 `trusted_clients`

Registered PBB apps.

- `id`
- `client_id`
- `client_secret_hash`
- `name`
- `allowed_redirect_uris_json`
- `allowed_scopes_json`
- `status`
- `created_at`
- `updated_at`

### 7.5 `account_audit_events`

Identity/security audit trail.

- `id`
- `pbb_user_id`
- `event_type`
- `actor_pbb_user_id` - nullable
- `client_id` - nullable
- `ip_address`
- `user_agent`
- `metadata_json`
- `created_at`

## 8. API Surface

### 8.1 Browser SSO Endpoints

```http
GET /oauth/authorize
```

Starts app login or silent app authorization.

Expected query parameters:

- `client_id`
- `redirect_uri`
- `response_type=code`
- `scope`
- `state`

```http
POST /oauth/token
```

App exchanges an authorization code for identity claims.

Expected body:

- `grant_type=authorization_code`
- `code`
- `client_id`
- `client_secret`
- `redirect_uri`

Response:

```json
{
  "token_type": "Bearer",
  "expires_in": 300,
  "id_token": "...",
  "user": {
    "pbb_user_id": "01J...",
    "name": "Juan Dela Cruz",
    "email": "juan@example.test",
    "mobile": "09170000000",
    "status": "active"
  }
}
```

The first implementation can return signed JSON directly from `/oauth/token` instead of a full OAuth/OIDC stack, as long as it preserves the same security properties: short-lived code, client authentication, redirect URI validation, and one-time use.

### 8.2 Account Profile API

```http
GET /api/account/me
PATCH /api/account/me
```

Used by apps or first-party UI to read/update central identity fields.

Patchable fields:

- `name`
- `email`
- `mobile`
- `avatar`

Email/mobile updates should support verification workflows.

### 8.3 App Server API

```http
GET /api/accounts/{pbb_user_id}
POST /api/accounts/lookup
POST /api/accounts/sync
```

Used by trusted apps to resolve account data, perform controlled lookups, or refresh local cached profile data.

These endpoints must require app authentication.

## 9. Security Requirements

- Password hashes are private to Account Service.
- Apps must never receive password hashes.
- Authorization codes must be short-lived and one-time use.
- Authorization code values must be stored hashed.
- Redirect URIs must be pre-registered and matched exactly.
- Client secrets must be stored hashed.
- App-to-Account calls must be authenticated.
- Account status must be checked during every SSO exchange.
- Suspended/disabled users must not receive app sessions.
- Profile updates to mobile/email should require verification before becoming trusted identifiers.
- Log login success, login failure, SSO issue, SSO consume, password reset, profile update, and account status changes.
- Use HTTPS in deployed environments.
- Do not pass long-lived credentials in query strings.

## 10. App Integration Contract

Each app should add:

- `pbb_user_id` column on local users table.
- Unique index on `pbb_user_id`.
- SSO callback route.
- Server-side code exchange client.
- Local user provisioning/updating service.
- Local session creation after successful code exchange.
- Optional profile refresh job or webhook consumer.

Suggested local user sync rule:

```text
If local user exists by pbb_user_id:
    update cached identity fields
else if migration mapping exists by email/mobile:
    attach pbb_user_id to existing local user
else:
    create local user with default app role/profile
```

## 11. Role Strategy

Do not centralize all app roles immediately.

Recommended split:

- Account Service owns global account status.
- Account Service may own broad global account category later, such as citizen, staff, system.
- Apps own operational permissions.

Examples:

- Chat owns room admin, moderator, badge permissions, blocked-login state if it is Chat-specific.
- Hotline owns citizen/operator/command/admin roles until a deliberate central role model is designed.
- Support owns support operator/admin roles.

Later, Account Service can add global role assignments if PBB needs a single RBAC control plane.

## 12. Migration Plan

### Phase 1: Account Service Skeleton

- Create new Laravel app or service directory for Account Service.
- Create core account tables.
- Add registration/login/password reset basics.
- Add trusted client registry.
- Add authorization code issue/exchange.
- Add test client entries for Chat and Hotline.

### Phase 2: Chat SSO

- Add `pbb_user_id` to Chat users.
- Add Account Service login redirect and callback.
- On successful SSO, find or create Chat user.
- Keep current Chat login available behind a feature flag during migration.
- Add tests for login callback, inactive account rejection, and local user provisioning.

### Phase 3: Hotline SSO

- Add `pbb_user_id` to Hotline users.
- Add Account Service callback route.
- On successful SSO, find or create Hotline citizen user.
- Update Chat Hotline icon to route through Account Service SSO instead of only opening Hotline URL.
- Keep current Hotline email/password login during transition.

### Phase 4: Profile Sync

- Move central profile edits to Account Service APIs.
- Update Chat and Hotline profile forms to call Account Service for name/email/mobile/avatar.
- Keep app-only profile fields local.
- Add sync/audit behavior for conflicts.

### Phase 5: Password Cutover

- Stop allowing app-local password changes for migrated users.
- Account Service becomes the only password owner.
- Remove or hide app-specific password reset flows.
- Keep emergency admin fallback documented and audited.

### Phase 6: Wider App Adoption

- Integrate Support, Maestro, Landing, and other PBB apps.
- Add optional global role/policy service only if needed.
- Add MFA/OTP and verified-mobile workflows.

## 13. Chat to Hotline Implementation Notes

Current Chat already has a Hotline escalation endpoint:

```text
POST /api/chat/escalate-to-hotline
```

That endpoint currently returns a handoff payload and Hotline URL. It should evolve to either:

- return an Account Service authorize URL for Hotline, or
- redirect the browser to Account Service directly.

Preferred user-facing flow:

```text
Chat Hotline icon
-> Account Service /oauth/authorize?client_id=hotline...
-> Hotline /auth/account/callback?code=...
-> Hotline /citizen
```

The handoff can include optional context such as:

- source app: `chat`
- requested destination: `hotline.citizen`
- reason: `emergency_escalation`
- optional Chat room/conversation reference if safe and intended

Do not include chat message bodies by default.

## 14. Open Questions

- Should Account Service live as a new top-level app under `C:\wamp64\www\pbb\account`, or inside an existing workspace until stabilized?
- Should `pbb_user_id` be UUID, ULID, or database integer? Recommendation: ULID or UUID.
- Should mobile number be mandatory for citizens?
- Should email be optional for citizens?
- Should existing Chat and Hotline users be matched by email, mobile, or manual admin mapping?
- Should Account Service own global avatars from day one?
- Should disabled users be logged out from apps immediately through webhook/event broadcast, or only blocked on next SSO/session refresh?
- Should Account Service expose full OIDC later, or keep a PBB-internal OAuth-like flow for V1?

## 15. Initial Agent Task List

1. Inventory current `users` schemas in Chat, Hotline, Support, Maestro, and Landing.
2. Identify common identity fields and conflicts.
3. Propose the exact `pbb_user_id` type and migration shape.
4. Scaffold Account Service as a Laravel app.
5. Implement core Account model, migrations, and seed trusted clients.
6. Implement `/oauth/authorize` and `/oauth/token` with one-time authorization codes.
7. Implement Account login UI/API.
8. Add account status and audit event logging.
9. Add Chat SSO callback behind a feature flag.
10. Add Hotline SSO callback behind a feature flag.
11. Implement Chat Hotline icon SSO handoff.
12. Add tests for code expiry, code reuse prevention, redirect URI validation, inactive account rejection, and local user provisioning.
13. Draft migration instructions for existing app users.

## 16. Acceptance Criteria

The first successful milestone should prove:

- A citizen logs in once through Account Service from Chat.
- Chat creates a local session and local user linked to `pbb_user_id`.
- The same citizen clicks Hotline from Chat.
- Hotline opens without another password prompt.
- Hotline creates its own local session and local citizen user linked to the same `pbb_user_id`.
- Updating central name/mobile/email through an approved flow updates Account Service first.
- App-local sessions remain separate.
- Disabled Account Service users cannot receive new app sessions.

## 17. Recommended V1 Decision

Build Account Service as the central credential and identity authority. Use SSO token exchange for app login. Do not share session cookies. Do not let apps directly mutate the central account database. Link each app's local user records through `pbb_user_id`, and migrate app-by-app with feature flags.

