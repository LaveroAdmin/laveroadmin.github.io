# Site Menus Builder

Build and manage public site navigation menus (`header`, `footer`, `sidebar`) with drag-and-drop hierarchy and permanent caching.

## Installation

```bash
composer require laveroadmin/menus
php artisan migrate
php artisan admin:import menus
```

## Usage in Blade

```php
@foreach(site_menu('header') as $item)
    <a href="{{ $item['url'] }}" target="{{ $item['target'] }}">{{ $item['title'] }}</a>
@endforeach
```
