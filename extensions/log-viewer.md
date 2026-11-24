# Log viewer for LaveroAdmin

## Installation

```
$ composer require laveroadmin/log-viewer

$ php artisan admin:import log-viewer
```

Open `http://localhost/admin/logs`.

## Configuration

If your server doesn't allow you to access log files for example by blocking requests with '.log' in the url you can enable the following bypass function.

See `config/admin.php` and add in the `extensions` section

```
'extensions' => [
    'log-viewer' => [
        'bypass_protected_urls' => true,
        //'bypass_protected_urls_find' => ['.'],          // default ['.']
        //'bypass_protected_urls_replace' => ['[dot]'],   // default ['[dot]']
    ]
]
```

## License

Licensed under [The MIT License (MIT)](https://opensource.org/licenses/MIT).
