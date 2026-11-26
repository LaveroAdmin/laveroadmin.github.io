# Configuration Management

Visual settings form and drag-and-drop **Form Builder** for managing dynamic application configuration stored in the database and cached permanently until changed.

## Installation

```bash
composer require laveroadmin/config
php artisan migrate
php artisan admin:import config
```

Open `app/Providers/AppServiceProvider.php` and call `Config::load()` inside `boot()`:

```php
<?php

namespace App\Providers;

use Illuminate\Support\Facades\Schema;
use Illuminate\Support\ServiceProvider;
use LaveroAdmin\Admin\Config\Config;

class AppServiceProvider extends ServiceProvider
{
    public function boot(): void
    {
        if (Schema::hasTable(config('admin.extensions.config.table', 'admin_config'))) {
            Config::load();
        }
    }
}
```

Open `http://localhost/admin/config`.

## Features

- **Settings Form (`/admin/config`)**: Clean tabbed form grouped by sections, with 1-click copy for `config('...')` keys.
- **Visual Form Builder (`/admin/config/builder`)**:
  - Create tabs and reorder fields via drag-and-drop (`SortableJS`).
  - Configure field **type** (`text`, `textarea`, `number`, `email`, `url`, `password`, `switch`, `select`, `radio`, `checkbox`, `color`, `date`, `datetime`, `image`, `file`, `json`).
  - Set `choices`, `placeholder`, `default`, `required`, `min`/`max`/`step`, `rows`, and help text.
- **Forever Cache**: Settings are cached via `Cache::rememberForever` and automatically invalidated on save/delete.

## Usage

After adding and saving a setting in the panel, read it anywhere in Laravel with:

```php
$siteTitle = config('site.title');
```
