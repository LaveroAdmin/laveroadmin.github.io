# Configuration management

This tool will store the configuration data in the database

> **Preview:** *Config*

## Installation

```
$ composer require laveroadmin/config

$ php artisan migrate
```

Open `app/Providers/AppServiceProvider.php`, and call the `Config::load()` method within the `boot` method:

```
<?php

namespace App\Providers;

use LaveroAdmin\Admin\Config\Config;
use Illuminate\Support\ServiceProvider;

class AppServiceProvider extends ServiceProvider
{
    public function boot()
    {
        Config::load();  // Add this
    }
}
```

Then run the command to import menus and permissions (which can also be added manually)

```
$ php artisan admin:import config
```

Open `http://localhost/admin/config`.

## Usage

After add config in the panel, use `config($key)` to get value you configured.
