# PBB Chat Local-Only Policy and Hotline Handoff Contract

Status: Draft policy based on PBB Chat code and Account proposal as of 2026-06-29.

## 1. Policy

PBB Chat V1 is a local/LAN-only barangay chat app.

It must not be exposed through Landing public gateway routes.

## 2. Evidence

- `C:\wamp64\www\pbb\chat\release.json`
- `C:\wamp64\www\pbb\chat\README.md`
- `C:\wamp64\www\pbb\chat\routes\web.php`
- `C:\wamp64\www\pbb\chat\config\chat.php`
- `C:\wamp64\www\pbb\chat\app\Http\Controllers\Api\HotlineEscalationController.php`
- `C:\wamp64\www\pbb\chat\app\Http\Controllers\Api\ChatRealtimeController.php`
- `C:\wamp64\www\pbb\chat\app\Services\ChatRealtimeEventPublisher.php`

## 3. Release Metadata Rule

`chat\release.json` confirms:

```json
"landing": {
  "launcher": {
    "visible": true,
    "primary_launch_url": "https://chat.pbb.ph"
  },
  "public_gateway": {
    "enabled": false,
    "reason": "PBB Chat V1 is a local LAN app and must not be exposed through public Landing gateway routes."
  }
}
```

Kit Setup and Landing registry sync must preserve this rule.

## 4. Local Runtime Dependencies

| Dependency | Purpose |
|---|---|
| Local PHP/Laravel app | Chat API and frontend |
| Local MySQL `pbb_chat` | Rooms, messages, users, direct conversations, reports, badges |
| PBB Realtime | Room/direct/notification live updates |
| Helper UI | Chat UI, composer, login/account/password modal components |
| Hotline URL | Handoff target |

## 5. Current Hotline Handoff

Current endpoint:

```text
POST /api/chat/escalate-to-hotline
```

Current request fields:

| Field | Type | Notes |
|---|---|---|
| `summary` | nullable string, max 1000 | Optional user-provided handoff summary |
| `include_context` | nullable boolean | Whether caller asks to include context |

Current response status:

```text
202 Accepted
```

Current response shape:

```json
{
  "handoff": {
    "hotlineUrl": "https://hotline.pbb.ph",
    "user": {},
    "summary": null,
    "includeContext": false,
    "createdAt": "ISO-8601 timestamp"
  }
}
```

The current endpoint returns a handoff payload. Direct Hotline incident creation is not confirmed from code.

## 6. Privacy Rule For Handoff

Do not include chat message bodies by default.

Allowed V1 handoff fields:

- source app: `chat`
- destination: `hotline.citizen`
- optional summary
- optional include-context flag
- local chat user identity payload
- created timestamp

Not allowed by default:

- full room history
- direct message history
- private message bodies
- report/block data
- attachments

## 7. Account Service Evolution

Planned preferred flow from Account proposal:

```text
Chat Hotline icon
-> Account Service /oauth/authorize?client_id=hotline...
-> Hotline /auth/account/callback?code=...
-> Hotline /citizen
```

Chat handoff should evolve to either:

- return an Account Service authorize URL for Hotline, or
- redirect browser to Account Service directly.

## 8. Realtime Boundary

Chat uses Realtime for:

- room admission
- direct conversation admission
- notification admission
- backend event publish

Realtime room prefixes include:

```text
chat.thread.
chat.direct.
```

No Relay sync for Chat messages was confirmed.

## 9. Operational Controls

Required controls:

- Keep public gateway disabled.
- Keep Chat reachable on LAN/local node only.
- Generate Realtime signing/backend ingress secrets per install.
- Define retention/purge for local chat messages and direct messages.
- Define moderator/admin access.
- Decide whether durable offline message queueing is in scope. Current code does not confirm it.

## 10. Open Decisions

- Whether Chat messages ever sync upstream.
- Whether Chat uses Account Service in V1 or after Account V1 proves Hotline SSO.
- Whether emergency handoff should create a Hotline pre-intake record.
- Whether `include_context=true` can include selected metadata, and exactly which fields are safe.

