# Notifications (Telegram / Webhook / Email)

Unified notification channels and delivery logs for LaveroAdmin.

## Installation

```bash
composer require laveroadmin/notifications
php artisan migrate
php artisan admin:import notifications
```

## Usage

```php
\LaveroAdmin\Admin\Notifications\NotificationService::dispatch(
    'wafu.block',
    'Blocked IP',
    'IP 1.2.3.4 was blocked by WAFU'
);
```
