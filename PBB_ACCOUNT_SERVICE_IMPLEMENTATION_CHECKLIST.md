# PBB Account Service Implementation Checklist

**Project:** Project Bantay Bayan (PBB)  
**Module:** Account Service / Local Node Identity  
**Date:** 2026-06-29  
**Status:** Planning checklist  

---

## Status Legend

- `[ ]` Not started
- `[~]` In progress
- `[x]` Done
- `[!]` Blocked / needs decision

## 1. Preparation

- [x] Confirm Account Service deployment model: one Account Service per local PBB node.
- [x] Confirm HQ is not the V1 login authority for local apps.
- [x] Confirm Account Service should be local-only for V1.
- [x] Confirm app-local sessions remain separate.
- [x] Confirm app-local roles remain app-owned for V1.
- [x] Prepare local Account Service database.
- [x] Prepare local Account Service domain/vhost.
- [x] Decide final local Account domain.
- [ ] Decide local HTTPS certificate approach for development.

Notes:

```text
Confirmed local domain: https://account.pbb.ph
Recommended app path: C:\wamp64\www\pbb\account
```

## 2. Local Environment Decisions

- [x] Confirm database name.
- [x] Confirm database username/password for development.
- [x] Confirm Account Service base URL.
- [ ] Confirm Chat local base URL for SSO callback.
- [ ] Confirm Hotline local base URL for SSO callback.
- [x] Confirm whether development uses HTTP or HTTPS.
- [ ] Confirm whether Mail/OTP will be stubbed in V1.
- [ ] Confirm initial seeded admin or test citizen account.

Suggested development values:

```text
ACCOUNT_BASE_URL=https://account.pbb.ph
DB_HOST=127.0.0.1
DB_DATABASE=pbb_accounts
DB_USERNAME=root
DB_PASSWORD=
CHAT_BASE_URL=https://chat.pbb.ph
HOTLINE_BASE_URL=https://hotline.pbb.ph
```

## 3. App Inventory

- [ ] Inventory Chat `users` schema.
- [ ] Inventory Hotline `users` schema.
- [ ] Inventory Support `users` schema.
- [ ] Inventory Maestro user/auth schema if applicable.
- [ ] Inventory Landing user/auth schema if applicable.
- [ ] Identify shared identity fields across apps.
- [ ] Identify conflicting field names/types.
- [ ] Identify existing citizen matching candidates: mobile, email, username.
- [ ] Identify role fields that must remain app-local.

Expected first integration apps:

```text
C:\wamp64\www\pbb\chat
C:\wamp64\www\pbb\hotline
```

## 4. Account Service Scaffold

- [ ] Scaffold Laravel app under `C:\wamp64\www\pbb\account`.
- [ ] Configure `.env`.
- [ ] Configure database connection.
- [ ] Configure app key.
- [ ] Configure session cookie name.
- [ ] Configure trusted local app URLs.
- [ ] Add health endpoint.
- [ ] Add basic local development README.
- [ ] Verify app loads through local domain.

Recommended session cookie:

```text
pbb_account_session
```

## 5. Core Database Schema

- [ ] Create `accounts` migration.
- [ ] Create `trusted_clients` migration.
- [ ] Create `sso_authorization_codes` migration.
- [ ] Create `account_audit_events` migration.
- [ ] Decide whether `account_login_identifiers` is needed in V1.
- [ ] Add unique index for `accounts.pbb_user_id`.
- [ ] Add unique normalized email index where present.
- [ ] Add unique normalized mobile index where present.
- [ ] Add indexes for token/code lookup fields.
- [ ] Run migrations.

Recommended `pbb_user_id`:

```text
ULID stored as CHAR(26) or VARCHAR(26)
```

## 6. Account Model And Auth

- [ ] Implement Account model.
- [ ] Implement status enum or constants: `pending`, `active`, `suspended`, `disabled`.
- [ ] Implement login identifier normalization.
- [ ] Implement password hashing through Laravel hasher.
- [ ] Implement login form.
- [ ] Implement login POST handling.
- [ ] Implement logout.
- [ ] Implement password reset placeholder or V1 flow.
- [ ] Implement rate limiting for login.
- [ ] Implement failed login audit events.
- [ ] Implement successful login audit events.

V1 login identifiers:

```text
mobile or email
```

## 7. Trusted Clients

- [ ] Implement TrustedClient model.
- [ ] Store only hashed client secrets.
- [ ] Seed `pbb-chat` trusted client.
- [ ] Seed `pbb-hotline` trusted client.
- [ ] Add exact redirect URI validation.
- [ ] Add allowed scope validation.
- [ ] Add client status check.
- [ ] Add development command or seeder for rotating client secrets.

Initial clients:

```text
pbb-chat
pbb-hotline
```

## 8. SSO Authorization Code Flow

- [ ] Implement `GET /oauth/authorize`.
- [ ] Validate `client_id`.
- [ ] Validate exact `redirect_uri`.
- [ ] Validate `response_type=code`.
- [ ] Validate allowed scopes.
- [ ] Require Account login session before issuing code.
- [ ] Preserve and return `state`.
- [ ] Generate high-entropy authorization code.
- [ ] Store only authorization code hash.
- [ ] Set short expiry.
- [ ] Redirect back to app callback with `code` and `state`.
- [ ] Log SSO issue audit event.

Recommended code TTL:

```text
5 minutes or less
```

## 9. Token Exchange

- [ ] Implement `POST /oauth/token`.
- [ ] Validate `grant_type=authorization_code`.
- [ ] Authenticate client by `client_id` and `client_secret`.
- [ ] Validate exact `redirect_uri`.
- [ ] Hash incoming code and find stored code.
- [ ] Reject expired code.
- [ ] Reject consumed code.
- [ ] Mark code consumed atomically.
- [ ] Check account status.
- [ ] Return identity claims.
- [ ] Log SSO consume audit event.
- [ ] Add rate limiting.

Required rejection tests:

- [ ] Invalid client secret.
- [ ] Invalid redirect URI.
- [ ] Expired code.
- [ ] Reused code.
- [ ] Disabled account.
- [ ] Suspended account.

## 10. Account Profile API

- [ ] Implement `GET /api/account/me`.
- [ ] Implement `PATCH /api/account/me`.
- [ ] Validate name updates.
- [ ] Validate mobile updates.
- [ ] Validate email updates.
- [ ] Decide verification behavior for changed mobile/email.
- [ ] Log profile update audit events.
- [ ] Add app-facing profile lookup endpoint if needed.
- [ ] Add trusted app authentication for app server APIs.

V1 can defer verified email/mobile workflows if login fields are seeded manually for testing.

## 11. Chat Integration

- [ ] Add `pbb_user_id` column to Chat users.
- [ ] Add unique index for Chat `users.pbb_user_id`.
- [ ] Add Account SSO config to Chat `.env`.
- [ ] Add Chat SSO feature flag.
- [ ] Add Chat login redirect to Account `/oauth/authorize`.
- [ ] Add Chat SSO callback route.
- [ ] Add Chat server-side token exchange client.
- [ ] Add Chat local user provisioning service.
- [ ] Match existing Chat user by `pbb_user_id`.
- [ ] Optionally match existing Chat user by verified mobile/email during migration.
- [ ] Create Chat session after successful exchange.
- [ ] Reject inactive Account users.
- [ ] Add Chat callback tests.

Suggested Chat callback:

```text
https://chat.pbb.ph/auth/account/callback
```

## 12. Hotline Integration

- [ ] Add `pbb_user_id` column to Hotline users.
- [ ] Add unique index for Hotline `users.pbb_user_id`.
- [ ] Add Account SSO config to Hotline `.env`.
- [ ] Add Hotline SSO feature flag.
- [ ] Add Hotline citizen Account callback route.
- [ ] Add Hotline server-side token exchange client.
- [ ] Add Hotline local citizen provisioning service.
- [ ] Match existing Hotline user by `pbb_user_id`.
- [ ] Optionally match existing Hotline citizen by verified mobile/email during migration.
- [ ] Create Hotline session after successful exchange.
- [ ] Redirect successful citizen login to `/citizen`.
- [ ] Reject inactive Account users.
- [ ] Add Hotline callback tests.

Suggested Hotline callback:

```text
https://hotline.pbb.ph/auth/account/callback
```

## 13. Chat To Hotline SSO Handoff

- [ ] Review current Chat endpoint `POST /api/chat/escalate-to-hotline`.
- [ ] Decide whether endpoint returns authorize URL or performs redirect.
- [ ] Build Hotline authorize URL through Account Service.
- [ ] Include `client_id=pbb-hotline`.
- [ ] Include Hotline callback redirect URI.
- [ ] Include `state`.
- [ ] Do not include chat message bodies by default.
- [ ] Add optional safe handoff metadata only if needed.
- [ ] Update Chat Hotline icon behavior.
- [ ] Verify Account does not ask for password again when Account session exists.
- [ ] Verify Hotline session is created.
- [ ] Verify user lands in Hotline citizen surface.

Target flow:

```text
Chat Hotline icon
-> Account /oauth/authorize for pbb-hotline
-> Hotline callback
-> Hotline /citizen
```

## 14. Security Hardening

- [ ] Store password hashes only in Account Service.
- [ ] Store client secrets hashed.
- [ ] Store authorization codes hashed.
- [ ] Enforce exact redirect URI matching.
- [ ] Enforce one-time authorization code use.
- [ ] Enforce short code expiry.
- [ ] Rate limit login.
- [ ] Rate limit token exchange.
- [ ] Add CSRF protection to browser forms.
- [ ] Validate and preserve OAuth `state`.
- [ ] Add audit logging for identity/security events.
- [ ] Mask secrets in logs.
- [ ] Avoid long-lived credentials in URLs.
- [ ] Document local backup/encryption expectations.

## 15. Tests

- [ ] Account login success.
- [ ] Account login failure.
- [ ] Account disabled login rejection.
- [ ] Account suspended SSO rejection.
- [ ] Authorization code issue.
- [ ] Authorization code exchange.
- [ ] Authorization code expiry rejection.
- [ ] Authorization code reuse rejection.
- [ ] Invalid client secret rejection.
- [ ] Invalid redirect URI rejection.
- [ ] Unknown client rejection.
- [ ] Chat local user provisioning.
- [ ] Chat existing user by `pbb_user_id`.
- [ ] Hotline local citizen provisioning.
- [ ] Hotline existing citizen by `pbb_user_id`.
- [ ] Chat-to-Hotline SSO without second password prompt.

## 16. Kit Setup Packaging

- [ ] Add Account Service package manifest.
- [ ] Add Account Service to bundled package list.
- [ ] Add installer config schema fields.
- [ ] Generate Account `.env`.
- [ ] Generate Account app key.
- [ ] Create/migrate Account database.
- [ ] Seed trusted clients for Chat and Hotline.
- [ ] Configure local DNS/vhost.
- [ ] Add health smoke check.
- [ ] Add SSO smoke check.
- [ ] Add update policy for additive migrations.
- [ ] Mask Account secrets in reports.

This phase can wait until manual development/testing proves the first milestone.

## 17. Documentation

- [x] Create revised Account Service proposal.
- [x] Create implementation checklist.
- [ ] Add local developer setup guide.
- [ ] Add Chat integration notes.
- [ ] Add Hotline integration notes.
- [ ] Add Kit Setup packaging notes.
- [ ] Add migration runbook for existing users.
- [ ] Add security and recovery runbook.

## 18. First Milestone Acceptance

- [ ] Account Service runs locally.
- [ ] Citizen logs in once through Account from Chat.
- [ ] Chat creates local session.
- [ ] Chat user has `pbb_user_id`.
- [ ] Citizen clicks Hotline icon in Chat.
- [ ] Account uses existing Account session.
- [ ] Hotline opens without another password prompt.
- [ ] Hotline creates local session.
- [ ] Hotline citizen user has same `pbb_user_id`.
- [ ] Chat and Hotline sessions are separate.
- [ ] Disabled Account users cannot receive new Chat or Hotline sessions.
- [ ] Authorization codes cannot be reused.
- [ ] Redirect URI validation is exact.
- [ ] Core audit events are recorded.

## 19. Current Blockers / Waiting On

- [x] Local database prepared.
- [x] Local domain/vhost prepared.
- [x] Final development base URLs confirmed for Account Service.
- [ ] Initial trusted client redirect URIs confirmed.
