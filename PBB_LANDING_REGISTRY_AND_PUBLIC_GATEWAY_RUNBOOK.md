# Landing Registry and Public Gateway Runbook

Status: Draft runbook based on current Landing code and Kit Setup docs as of 2026-06-29.

## 1. Purpose

PBB Landing serves two separate surfaces:

1. Local launcher at `https://pbb.ph`.
2. Public hub-safe metadata and machine gateway behavior on the assigned hub domain.

Landing must not infer that an app route is safe for public exposure. App teams and Kit Setup must declare and validate gateway intent before Landing forwards any public machine traffic.

## 2. Evidence

- `C:\wamp64\www\pbb\landing\README.md`
- `C:\wamp64\www\pbb\landing\config\landing.php`
- `C:\wamp64\www\pbb\landing\release.json`
- `C:\wamp64\www\pbb\landing\src\Gateway.php`
- `C:\wamp64\www\pbb\landing\src\RegistryStore.php`
- `C:\wamp64\www\pbb\landing\src\HubProjection.php`
- `C:\wamp64\www\pbb\kit-setup\docs\landing-registry-sync-contract.md`
- `C:\wamp64\www\pbb\kit-setup\docs\proposal-landing-public-gateway-exposure-protocol.md`

## 3. Runtime Inputs

| Resource | Purpose |
|---|---|
| `storage\registry.json` | Private app launcher, health, and gateway registry |
| Relay `hub.json` | Source of node identity and public hub metadata projection |
| `PBB_LANDING_REGISTRY_TOKEN_HASH` | SHA-256 hash of Kit-generated registry token |
| `PBB_LANDING_RELAY_HUB_JSON` | Optional override for Relay hub JSON path/URL |
| `PBB_LANDING_CA_BUNDLE` | Gateway cURL CA bundle |

Current `landing\config\landing.php` default hub JSON source is `https://relay.pbb.ph/hub.json`; local installs may override it.

## 4. Registry Ownership

Kit Setup owns registry writes.

Apps own:

- release metadata
- launcher visibility intent
- public gateway intent
- health endpoints
- machine API prefixes safe for public routing

Landing owns:

- registry persistence
- registry audit log
- gateway audit log
- public projection from hub JSON
- enforcement of peer/gateway rules

## 5. Registry Sync Flow

```text
Kit Setup installs or repairs app
-> reads app release/install metadata
-> validates launcher and gateway declarations
-> computes registry payload
-> sends token-authenticated internal registry request
-> Landing writes storage/registry.json
-> Landing app launcher and gateway use registry state
```

## 6. Public Metadata

Landing public endpoint:

```text
https://{hub.domain}/.well-known/pbb-hub.json
```

This endpoint must be a sanitized projection of Relay `hub.json`.

Do not expose:

- registry token
- internal file paths
- app client secrets
- handler tokens
- local database settings
- private API keys
- raw secrets from Relay/support settings

## 7. Gateway Rules

Confirmed current gateway scope:

```text
/relay/api/v1/*
```

Gateway behavior must remain machine-to-machine. Browser-facing public app exposure should not be added without a separate security review.

Requirements:

- request host must match the assigned public hub domain
- peer domain allowlist must pass
- target app must be registered
- target app must explicitly enable gateway behavior
- path prefix must be allowed
- request body size must stay under configured `max_body_bytes`
- gateway request/response must be logged without secrets

## 8. Current App Exposure Defaults

| App | Launcher | Public Gateway | Evidence |
|---|---|---|---|
| Landing | hidden in its own release metadata | disabled in release metadata | `landing\release.json` |
| Chat | visible launcher | disabled, local LAN only | `chat\release.json` |
| Games | local app; registry evidence not confirmed from Games code | not confirmed | `games\README.md` |
| Relay | gateway candidate for `/relay/api/v1/*` | registry-controlled | `landing\src\Gateway.php`; Kit docs |

## 9. Operator Procedure

Install/update:

1. Run Kit Setup install/update.
2. Confirm app package release metadata is present.
3. Confirm registry token hash is configured.
4. Confirm Relay `hub.json` is reachable/readable.
5. Confirm Landing health endpoint.
6. Confirm launcher entries are correct on `https://pbb.ph`.
7. Confirm public `/.well-known/pbb-hub.json` does not expose private fields.
8. Test gateway rejection for unallowed hosts/paths.
9. Test gateway success only for explicitly allowed M2M path.

## 10. Security Notes

| Risk | Severity | Control |
|---|---|---|
| Registry token leak | High | Store only hash; rotate per node |
| Public gateway overexposure | High | Require app declaration + Kit validation + path allowlist |
| Peer spoofing | High | Enforce assigned hub domain and peer allowlist |
| Sensitive hub projection | Medium | Strict allowlist projection tests |
| Logs containing secrets | Medium | Redact Authorization and token-like fields |

## 11. Open Decisions

- Exact registry JSON schema to publish as a stable contract.
- Final FRP-generated public host mapping.
- Whether future public gateways include Hotline media and Support attachment machine APIs.
- Whether public endpoint should also serve `/hub.json` or only `/.well-known/pbb-hub.json`.

