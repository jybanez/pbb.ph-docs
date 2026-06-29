# PBB Node Identity and hub.json Contract

Status: Draft contract based on local code and owner clarifications as of 2026-06-29.

## 1. Purpose

`hub.json` is the node identity and topology document consumed by PBB node services. It tells local apps what node they are running on, what administrative level the node represents, what public domain belongs to it, and where the node sits in the barangay -> city/municipality -> province -> cloud HQ topology.

Owner-confirmed model:

- Hub HQ provides node identity details.
- Kit Setup validates installation from an admin-provided hub ID and hub token.
- Kit Setup Data Prep uses hub information to populate MapServer tiles for the Hub HQ-defined boundary.
- Production topology is barangay -> city/municipality -> province -> cloud PBB HQ for now.

## 2. Evidence

- `C:\wamp64\www\pbb\relay\public\hub.json`
- `C:\wamp64\www\pbb\landing\config\landing.php`
- `C:\wamp64\www\pbb\support\.env.example`
- `C:\wamp64\www\pbb\documentations\PBB_SELECTED_APPS_ECOSYSTEM_MAP.md`

External sample endpoint identified by the project owner:

```text
https://relay.pbb.ph/hub.json
```

Live external fetch was not verified during this pass. Local Relay `public\hub.json` was inspected.

## 3. Confirmed Local Shape

Local `C:\wamp64\www\pbb\relay\public\hub.json` contains:

| Field | Meaning | Example from local file | Required |
|---|---|---|---|
| `base_url` | HQ/base API URL for hub context | `https://hub.pbb.ph` | Yes |
| `hub_id` | Hub HQ numeric ID | `12` | Yes |
| `relay_hub_id` | PBB/PSGC-like node code used by Relay | `072217029` | Yes |
| `name` | Human-readable node name | `Guadalupe, CEBU CITY, CEBU` | Yes |
| `deployment` | Administrative deployment level | `barangay` | Yes |
| `domain` | Public hub domain | `guadalupe-cebu-cebu.pbb.ph` | Yes |
| `status` | Node status | `active` | Yes |
| `country_code` | Country code | `PH` | Yes |
| `reg_code` | Region code | `07` | Yes |
| `prov_code` | Province code | `0722` | Yes for province/city/barangay |
| `citymun_code` | City/municipality code | `072217` | Yes for city/barangay |
| `brgy_code` | Barangay code | `072217029` | Yes for barangay |
| `uplinks` | Parent/peer upstream hub list | array | Yes, may be empty for top-level |
| `sources` | Downstream/source hub list | array | Optional/empty allowed |
| `hydrated_at` | Last hydration timestamp | ISO timestamp | Yes for generated snapshots |
| `hydrated_from` | Snapshot source | `hq_heartbeat` | Yes for generated snapshots |
| `snapshot_version` | Version label/hash | `hub-12:...` | Yes |
| `snapshot_hash` | Local snapshot hash | SHA-256-like string | Yes |
| `hq_snapshot_hash` | HQ source snapshot hash | SHA-256-like string | Yes when hydrated from HQ |

## 4. Uplink Object Contract

Confirmed local uplink object:

| Field | Meaning | Example |
|---|---|---|
| `id` | Relationship row ID | `48` |
| `uplink_hub_id` | Parent hub numeric ID | `11` |
| `uplink_type` | Relationship type | `hierarchy` |
| `uplink_domain` | Parent public domain | `cebu-cebu.pbb.ph` |
| `priority` | Routing priority | `1` |
| `is_primary` | Whether primary upstream | `true` |
| `hub` | Nested public parent hub metadata | object |

Nested `uplinks[].hub` confirmed fields include `id`, `name`, `code`, `deployment`, `domain`, `status`, and administrative codes.

## 5. Deployment Levels

Confirmed/owner-defined levels:

```text
barangay
city / municipality
province
cloud PBB HQ
```

Current local sample is a barangay node with a city uplink:

```text
Guadalupe barangay node
-> Cebu City node
-> province node
-> cloud PBB HQ
```

Province-level and HQ-level exact field requirements are not fully confirmed from code. Use `Unknown / Not confirmed from code` for fields not present in a generated `hub.json` sample.

## 6. Consumers

| Consumer | Usage | Evidence |
|---|---|---|
| Kit Setup | Validates admin-provided hub ID/token and uses hub boundary for data prep | owner clarification; Kit Setup docs |
| Landing | Reads Relay hub JSON and serves sanitized public projection | `landing\config\landing.php`; `landing\src\HubProjection.php` |
| Relay | Stores/serves local hub identity and uses topology for relationship/context | `relay\public\hub.json`; Relay docs |
| Support System | Reads hub identity/uplink data for SITREP/support context | `support\.env.example`; support services in briefing |
| MapServer Data Prep | Uses hub boundary context to preload tiles/boundaries | owner clarification |

## 7. Public Projection Rules

Landing should expose only public-safe hub identity through `/.well-known/pbb-hub.json`.

Public-safe candidates:

- `relay_hub_id`
- `name`
- `deployment`
- `domain`
- `status`
- administrative codes
- safe uplink public metadata

Do not expose secrets. Local docs indicate `snapshot_hash`, `base_url`, and similar operational/internal fields must be reviewed before public projection.

## 8. Validation Requirements

Kit Setup should validate:

- hub ID and hub token provided by admin
- fetched/generated `hub.json` parses as JSON
- required identity fields exist
- `status` allows installation, normally `active`
- `deployment` is supported
- administrative code chain is internally consistent
- `domain` is present when public gateway/FRP exposure is enabled
- boundary input needed by MapServer Data Prep is available

## 9. Unknowns / Open Decisions

- Exact full schema returned by live `https://relay.pbb.ph/hub.json`.
- Exact schema differences for city, municipality, province, and HQ-level nodes.
- Whether `sources[]` should list downstream child nodes, peer nodes, or both.
- Whether `base_url` should be public-projected or remain private/internal.
- Final FRP tunnel endpoint fields, if any, to add to `hub.json`.

