# Extension Marketplace

Visual extension manager and official marketplace for LaveroAdmin panel.

## Requirements

- PHP >= 8.1
- `laveroadmin/laveroadmin`: `^1.0`

## Installation

```bash
composer require laveroadmin/marketplace
```

Then import the menu item and permissions:

```bash
php artisan admin:import marketplace
```

Open `http://localhost/admin/marketplace`.

## Features

- **Dynamic Repositories**: Fetch extensions from remote or local repositories without updating the marketplace package.
- **Environment Compatibility Matrix**: Automatic verification of PHP version, Laravel Framework, LaveroAdmin version, and required PHP extensions with visual badges and warnings.
- **Extensions Catalog**: Filter by category (*Tools*, *Content*, *System*, *Security*), repository source, or search by keywords.
- **Installed Status**: Track installed versions of packages and identify missing extensions.
- **Interactive Actions**:
  - One-click Install and Update via Composer.
  - Re-import assets and migrations (`admin:import`).
  - Safe package uninstallation (`composer remove`).
  - Pre-installation compatibility alerts.
- **Live Terminal Log**: View real-time console output during operations.
- **Manual CLI Modal**: Copy pre-formatted terminal commands for environments with restricted execution rights.

## Repository Configuration

You can configure official, custom, and local repositories in `config/admin.php`:

```php
'marketplace' => [
    'repositories' => [
        'official' => [
            'name'      => 'Official Repository',
            'type'      => 'remote',
            'url'       => 'https://raw.githubusercontent.com/LaveroAdmin/marketplace/master/repository.json',
            'cache_ttl' => 3600,
        ],
        'my-company' => [
            'name'      => 'Company Private Repo',
            'type'      => 'remote',
            'url'       => 'https://repo.example.com/lavero-extensions.json',
            'cache_ttl' => 1800,
        ],
        'local' => [
            'name' => 'Local Development',
            'type' => 'local',
            'path' => base_path('packages/extensions'),
        ],
    ],
],
```

Click **"Refresh Sources"** in the Marketplace toolbar to flush repository caches and instantly load new packages.
