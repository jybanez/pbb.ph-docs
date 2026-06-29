# Wizaya Server Suite Product Architecture

Status: Draft architecture based on local folders inspected under `C:\wamp64\www` as of 2026-06-29.

## 1. Scope

Local Wizaya folders found:

```text
C:\wamp64\www\wizaya
C:\wamp64\www\cdn.wizaya
C:\wamp64\www\cron.wizaya.com
```

This document describes only what was confirmed from local files. It does not assume a current production deployment.

## 2. High-Level Product Shape

```text
Wizaya main web app
  -> Joomla/PHP application
  -> MySQL database
  -> files/media/templates/components/plugins/modules

cdn.wizaya
  -> PHP asset/data serving and cache layer
  -> data folder with cached/generated assets
  -> CORS-enabled static/dynamic asset responses

cron.wizaya.com
  -> PHP long-running/lock-file cron runner
  -> report/data generator libraries
  -> FTP/SFTP/remote client libraries
```

## 3. Evidence

- `C:\wamp64\www\wizaya\index.php`
- `C:\wamp64\www\wizaya\configuration.php`
- `C:\wamp64\www\wizaya\components`
- `C:\wamp64\www\wizaya\modules`
- `C:\wamp64\www\wizaya\plugins`
- `C:\wamp64\www\cdn.wizaya\index.php`
- `C:\wamp64\www\cdn.wizaya\app.php`
- `C:\wamp64\www\cdn.wizaya\data`
- `C:\wamp64\www\cron.wizaya.com\index.php`
- `C:\wamp64\www\cron.wizaya.com\app.php`
- `C:\wamp64\www\cron.wizaya.com\libraries\generators`

## 4. Main Web App

`C:\wamp64\www\wizaya\index.php` is a Joomla front controller. It defines `_JEXEC`, sets `JPATH_BASE`, loads `includes\defines.php` and `includes\framework.php`, then creates and initializes the Joomla site application.

Confirmed app characteristics:

| Area | Details |
|---|---|
| Framework | Joomla/PHP, legacy Joomla front controller |
| Database | MySQL/MariaDB via Joomla `configuration.php` |
| App folders | `components`, `modules`, `plugins`, `templates`, `administrator`, `media`, `images`, `files` |
| Cache/log/tmp | `cache`, `logs`, `tmp` |
| Mail | SMTP settings present in config |

Secrets exist in local config and are intentionally not reproduced here.

## 5. CDN / Asset Service

`C:\wamp64\www\cdn.wizaya\index.php` loads local config and `app.php`, defines `DATA_PATH`, and sets permissive CORS headers when `HTTP_ORIGIN` is present.

`cdn.wizaya\app.php` confirms:

- `DOWNLOAD_DOMAIN`
- `DOWNLOAD_SECRET`
- libraries: `path`, `folder`, `file`, `uri`, `jsmin`
- data storage under `data`
- MIME handling for CSS and JS
- redirect to configured domain when path is `/`

Confirmed purpose:

- serve/generated cached frontend assets
- store per-domain/per-template assets in `data`
- provide CSS/JS responses

Risk:

- CORS currently uses `Access-Control-Allow-Origin: *` in `index.php`.

## 6. Cron / Job Service

`C:\wamp64\www\cron.wizaya.com\index.php` loads libraries and uses a `cron.lock` file to prevent duplicate process starts. If no lock file exists, it instantiates `App`.

`cron.wizaya.com\app.php` confirms:

- long-running loop with `sleepTime`
- lock/reset file model
- remote/local caller traits
- logging

Confirmed generator libraries include merchandising reports, employee teams, commodities, business partners, branches, and other report/export modules under:

```text
C:\wamp64\www\cron.wizaya.com\libraries\generators
```

Confirmed remote/file-transfer libraries include FTP/SFTP/phpseclib.

## 7. Data Flow

```text
Browser/admin user
-> wizaya Joomla app
-> MySQL database and local files/media
-> references generated/static assets
-> cdn.wizaya serves cached CSS/JS/media assets
```

```text
Cron request/process
-> cron.wizaya.com index.php
-> lock file check
-> App loop
-> generator libraries
-> local data CSV/report outputs
-> optional FTP/SFTP/remote delivery
```

## 8. Deployment Assumptions

Confirmed local deployment:

- WAMP-style PHP filesystem under `C:\wamp64\www`
- multiple host-folder apps
- local MySQL credentials in app config
- PHP include-based libraries, not Composer-based in inspected files

Unknown / Not confirmed from code:

- current production hostnames
- current PHP version requirement
- database schema inventory
- queue/scheduler outside web-triggered cron runner
- service manager or Windows scheduled task configuration

## 9. Security Notes

| Area | Risk | Recommendation |
|---|---|---|
| Main app config | Contains DB and mail secrets | Keep out of docs/repo, rotate if exposed |
| CDN CORS | `Access-Control-Allow-Origin: *` | Restrict to known domains if credentials/sensitive assets are served |
| Cron lock runner | Web-triggered process control | Restrict access and monitor stale lock files |
| Legacy framework | Joomla 2005-2010-era front controller comments | Review dependency/security patch status |
| FTP/SFTP libraries | Remote delivery credentials likely exist elsewhere | Mask and rotate credentials; audit transfer targets |

## 10. Product Architecture Summary

Wizaya Server Suite appears to be a legacy PHP/Joomla business web platform with separate asset CDN/cache and cron/report generation services. The suite is file-system organized rather than package-manager organized in inspected files. It likely supports administration, content/modules/plugins/templates, generated reports, static asset delivery, and remote file/report distribution.

Unknown / Not confirmed from code:

- exact product modules active in production
- current customer/domain routing
- current database schema
- current authentication/authorization model beyond Joomla app conventions

