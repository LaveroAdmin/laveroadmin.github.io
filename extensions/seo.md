# SEO & Redirects

Manage 301/302/410 redirects with hit tracking and edit `robots.txt` from LaveroAdmin.

## Installation

```bash
composer require laveroadmin/seo
php artisan migrate
php artisan admin:import seo
```

Attach `RedirectMiddleware` in `bootstrap/app.php`:

```php
->withMiddleware(function (Middleware $middleware) {
    $middleware->web(append: [
        \LaveroAdmin\Admin\Seo\Middleware\RedirectMiddleware::class,
    ]);
})
```
