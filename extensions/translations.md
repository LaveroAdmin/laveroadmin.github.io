# Translation Manager

Web-based language and localization files editor for LaveroAdmin.

## Requirements

- PHP >= 8.1
- `laveroadmin/laveroadmin`: `^1.0`

## Installation

```bash
composer require laveroadmin/translations
```

Import menu item and permissions:

```bash
php artisan admin:import translations
```

Open `http://localhost/admin/translations`.

## Features

- **Side-by-Side Comparison**: Edit target translations alongside original source text in a structured two-column layout.
- **Support for PHP and JSON**: Seamlessly edits both nested PHP arrays (`lang/{locale}/*.php`) and root JSON files (`lang/{locale}.json`).
- **Completeness Metrics**: Live progress indicator showing percentage of translated strings and untranslated keys.
- **Add Keys On-the-Fly**: Create new translation keys directly through the admin panel without manual file editing.
- **Live Search**: Instant keyword filtering across translation keys, source text, and target text.
