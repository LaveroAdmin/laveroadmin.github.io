# Admin Extensions

LaveroAdmin features a rich, plug-and-play extension ecosystem. Extensions allow you to effortlessly add complex administrative capabilities — from queue and backup management to WYSIWYG editors and firewalls — with minimal setup.

---

## Official Extensions Directory

| Extension | Package | Description | Docs |
| :--- | :--- | :--- | :--- |
| **Helpers** | `laveroadmin/helpers` | Code generators, scaffolding for models/controllers/migrations | [Read docs](helpers.md) |
| **Log Viewer** | `laveroadmin/log-viewer` | Interactive log file viewer with level filters and search | [Read docs](log-viewer.md) |
| **Backup Manager** | `laveroadmin/backup` | Database and file backups manager powered by Spatie Backup | [Read docs](backup.md) |
| **Marketplace** | `laveroadmin/marketplace` | Extension marketplace & lifecycle manager | [Read docs](marketplace.md) |
| **Queue Manager** | `laveroadmin/queue-manager` | Queue monitoring, failed jobs retry/forget, and batch tracking | [Read docs](queue-manager.md) |
| **Activity Log** | `laveroadmin/activity-log` | Audit trail and user action history powered by Spatie Activitylog | [Read docs](activity-log.md) |
| **Translations** | `laveroadmin/translations` | Dual-column editor for PHP and JSON localization files | [Read docs](translations.md) |
| **Media Manager** | `laveroadmin/media-manager` | File explorer and media manager for storage disks | [Read docs](media-manager.md) |
| **Config Manager** | `laveroadmin/config` | Dynamic settings form builder and key-value configuration manager stored in database | [Read docs](config.md) |
| **Scheduling** | `laveroadmin/scheduling` | Crontab and scheduled tasks manager and inspector | [Read docs](scheduling.md) |
| **Reporter** | `laveroadmin/reporter` | Exception tracker and error reporter | [Read docs](reporter.md) |
| **WAFU Firewall** | `laveroadmin/wafu` | Web application firewall rules, IP blocklists, and threat logging | [Read docs](wafu.md) |
| **Grid Sortable** | `laveroadmin/grid-sortable` | Drag & drop sorting for Model-Grid rows | [Read docs](grid-sortable.md) |
| **CKEditor** | `laveroadmin/ckeditor` | CKEditor 5 WYSIWYG editor integration for Model-Form | [Read docs](ckeditor.md) |
| **SEO & Redirects** | `laveroadmin/seo` | 301/302/410 redirects manager with hit tracking, `robots.txt` editor, and global SEO meta tags | [Read docs](seo.md) |
| **Site Menus** | `laveroadmin/menus` | Public site navigation menus builder with drag-and-drop hierarchy and permanent caching | [Read docs](menus.md) |
| **Notifications** | `laveroadmin/notifications` | Alerts to Telegram, Webhook, or Email on WAFU blocks, exceptions, backups, and custom events | [Read docs](notifications.md) |
| **Cache Manager** | `laveroadmin/cache-manager` | Clear/warm Laravel caches, flush OPcache, and run `storage:link` without SSH | [Read docs](cache-manager.md) |
| **Forms & Leads** | `laveroadmin/forms` | Public contact/lead form builder and submissions inbox with statuses and notes | [Read docs](forms.md) |
| **Mail Log** | `laveroadmin/mail-log` | Outgoing Laravel emails logger and previewer with sensitive token redaction | [Read docs](mail-log.md) |
| **Sessions & Audit** | `laveroadmin/sessions` | Active admin sessions viewer, remote revocation, and login history audit | [Read docs](sessions.md) |
| **TipTap Editor** | `laveroadmin/tiptap` | Modern headless TipTap WYSIWYG editor field for Model-Form | [Read docs](tiptap.md) |

---

## How to Install an Extension

### Option A: Via Marketplace (Recommended)

When you install an extension through **Marketplace** (`Admin -> Marketplace`), it **automatically**:
1. Downloads and installs the package (via Composer or ZIP archive).
2. Runs `php artisan migrate --force` to create extension database tables.
3. Runs `php artisan admin:import <extension-name>` to register admin menu items and permissions.

For most extensions, **no additional commands are needed** after Marketplace installation.

### Option B: Via Composer CLI

```bash
composer require laveroadmin/<extension-name>
php artisan migrate
php artisan admin:import <extension-name>
```

---

## What Needs Manual Setup After Installation?

| Setup Type | Extensions | What to Do After Marketplace Install |
| :--- | :--- | :--- |
| **Plug & Play (No extra steps)** | `cache-manager`, `config`, `forms`, `helpers`, `log-viewer`, `mail-log`, `marketplace`, `notifications`, `scheduling`, `translations` | Works immediately — open the new item in the admin sidebar. |
| **One-time publish / Artisan step** | `backup`, `activity-log`, `media-manager`, `queue-manager`, `sessions` | `backup`: publish `spatie/laravel-backup` config; `activity-log`: publish Spatie migrations & `migrate`; `media-manager`: ensure `storage:link`; `queue-manager`: create `failed_jobs` / `job_batches` tables if missing; `sessions`: set `SESSION_DRIVER=database` for remote revocation. |
| **Register Middleware / Handler** | `seo`, `wafu`, `reporter` | `seo`: append `RedirectMiddleware` to `web` middleware; `wafu`: append `WafuMiddleware` (optional `vendor:publish --tag=wafu-config`); `reporter`: call `Reporter::report($e)` in your exception handler. |
| **Use in Forms / Grids / Blade** | `ckeditor`, `tiptap`, `grid-sortable`, `menus` | Call `$form->ckeditor('field')`, `$form->tiptap('field')`, `$grid->sortable()`, or `site_menu('header')` in your controllers/views. |

---

## Extension Configuration

Extensions can be customized in `config/admin.php` under the `extensions` key:

```php
'extensions' => [
    'backup' => [
        'enable' => true,
    ],
    'queue-manager' => [
        'enable' => true,
    ],
    'activity-log' => [
        'enable' => true,
    ],
    'translations' => [
        'enable' => true,
    ],
],
```

---

## Creating Your Own Extensions

You can build custom extensions and share them with the community. Learn more in our [Extension Development Guide](development.md).
