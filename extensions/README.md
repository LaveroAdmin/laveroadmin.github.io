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
| **Config Manager** | `laveroadmin/config` | Dynamic key-value configuration manager stored in database | [Read docs](config.md) |
| **Scheduling** | `laveroadmin/scheduling` | Crontab and scheduled tasks manager and inspector | [Read docs](scheduling.md) |
| **Reporter** | `laveroadmin/reporter` | Exception tracker and error reporter | [Read docs](reporter.md) |
| **WAFU Firewall** | `laveroadmin/wafu` | Web application firewall rules, IP blocklists, and threat logging | [Read docs](wafu.md) |
| **Grid Sortable** | `laveroadmin/grid-sortable` | Drag & drop sorting for Model-Grid rows | [Read docs](grid-sortable.md) |
| **CKEditor** | `laveroadmin/ckeditor` | CKEditor 5 WYSIWYG editor integration for Model-Form | [Read docs](ckeditor.md) |

---

## How to Install an Extension

Installing an extension takes two simple steps:

### 1. Require via Composer

```bash
composer require laveroadmin/<extension-name>
```

For example, to install the Queue Manager:
```bash
composer require laveroadmin/queue-manager
```

### 2. Import Assets & Database

Most extensions provide an import command to automatically publish static assets, create database tables, and register the menu item in the admin navigation:

```bash
php artisan admin:import <extension-name>
```

For example:
```bash
php artisan admin:import queue-manager
```

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
