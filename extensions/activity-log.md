# Activity Log & Audit Trail

Model change history and audit trail extension for LaveroAdmin based on [Spatie Laravel Activitylog](https://github.com/spatie/laravel-activitylog).

## Requirements

- PHP >= 8.1
- `laveroadmin/laveroadmin`: `^1.0`
- `spatie/laravel-activitylog`: `^4.0`

## Installation

```bash
composer require laveroadmin/activity-log
```

Publish Spatie Activitylog migration:

```bash
php artisan vendor:publish --provider="Spatie\Activitylog\ActivitylogServiceProvider" --tag="activitylog-migrations"
php artisan migrate
```

Import menu item and permissions:

```bash
php artisan admin:import activity-log
```

Open `http://localhost/admin/activity-log`.

## Features

- **Audit Overview**: List of all recorded actions across your models (`created`, `updated`, `deleted`).
- **Visual Diff Viewer**: Interactive modal comparing old and new values side-by-side with color highlights.
- **Filters**: Quickly filter records by event type and subject model class.
- **Log Pruning**: Purge historical records older than 30 days.
