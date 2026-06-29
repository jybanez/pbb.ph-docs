# PBB Games Emergency Mode Runbook

Status: Draft runbook based on PBB Games code as of 2026-06-29.

## 1. Purpose

PBB Games is an optional local citizen engagement and emergency-preparedness learning app. It must not compete with emergency operations. During active incidents or emergencies, operators must be able to reduce or disable game availability.

## 2. Evidence

- `C:\wamp64\www\pbb\games\README.md`
- `C:\wamp64\www\pbb\games\config\games.php`
- `C:\wamp64\www\pbb\games\config\games.registry.php`
- `C:\wamp64\www\pbb\games\src\ModePolicy.php`
- `C:\wamp64\www\pbb\games\health.php`

## 3. Modes

Confirmed modes in `ModePolicy.php`:

| Mode | Behavior |
|---|---|
| `normal` | All enabled categories can show if configured |
| `monitoring` | Games remain available with warning banner |
| `active_incident` | Only `learning` category is allowed; Quick Games and Retro Corner are hidden |
| `emergency` | No game category is allowed; emergency banner shown |

Invalid mode falls back to `normal`.

## 4. Config Fields

`config\games.php` controls:

| Field | Purpose |
|---|---|
| `enabled` | Global app enabled flag |
| `mode` | One of `normal`, `monitoring`, `active_incident`, `emergency` |
| `show_quick_games` | Quick game category visibility |
| `show_learning_games` | Learning game category visibility |
| `show_retro_corner` | Retro category visibility |
| `show_local_games` | Local category visibility |
| `landing_url` | Local PBB hub link |
| `hotline_url` | Hotline link in emergency guidance |
| `emergency_message` | Emergency mode text |

## 5. Operator Procedure

Monitoring state:

1. Edit `config\games.php`.
2. Set:
   ```php
   'mode' => 'monitoring',
   ```
3. Confirm `/health.php` shows `mode: monitoring`.

Active incident state:

1. Edit `config\games.php`.
2. Set:
   ```php
   'mode' => 'active_incident',
   ```
3. Confirm `/health.php` shows only learning games visible.
4. Confirm Quick Games and Retro Corner are hidden in browser.

Emergency state:

1. Edit `config\games.php`.
2. Set:
   ```php
   'mode' => 'emergency',
   ```
3. Confirm `/health.php` shows `games_visible: 0`.
4. Confirm browser shows emergency guidance and Hotline/PBB local hub path.

Return to normal:

1. Set:
   ```php
   'mode' => 'normal',
   ```
2. Confirm visible game counts match expected registry.

## 6. Health Check

Endpoint:

```text
GET /health.php
```

Returns:

- `app`
- `status`
- `enabled`
- `mode`
- `version`
- `games_total`
- `games_visible`
- `categories`

Use this endpoint for Kit/Landing/operator checks.

## 7. Safety Rules

- `emergency` mode must disable all games.
- `active_incident` mode must allow learning-only content.
- Games must remain optional; Hotline, Relay, Support, Realtime, MapServer, and core operations must not depend on Games.
- Do not add commercial ROMs/BIOS/copyrighted assets.

## 8. Current Gap

No runtime admin UI or API for changing mode was confirmed. Mode changes are config-file driven.

Recommended next step:

- Add a Kit/operator control hook or documented script to set mode.
- Add a smoke test that checks `emergency` returns zero visible games.

