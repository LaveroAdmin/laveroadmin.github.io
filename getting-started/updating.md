# version update

## View current version

```
composer show laveroadmin/laveroadmin
```

## Update to the latest version

```
composer require laveroadmin/laveroadmin
```

## Update to development version

```
composer require laveroadmin/laveroadmin:dev-main
```

## Update the specified version

```
composer require laveroadmin/laveroadmin: 1.0.1
```

## Precautions

Since the front-end resources of each version may be modified, you need to run the following commands after the update.

```
// Force to publish release assets files
php artisan vendor:publish --tag=lavero-admin-assets --force

// Force to publish release lang files
php artisan vendor:publish --tag=lavero-admin-lang --force

// Clean up the view cache
php artisan view:clear
```

Finally, don't forget to clean up the browser cache.
