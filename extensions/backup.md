# Backup Manager

Application backup management extension for LaveroAdmin based on [Spatie Laravel Backup](https://github.com/spatie/laravel-backup).

## Requirements

- PHP >= 8.1
- `laveroadmin/laveroadmin`: `^1.0`
- `spatie/laravel-backup`: `^8.0|^9.0`

## Installation

```bash
composer require laveroadmin/backup
```

Publish Spatie Backup configuration if it hasn't been published yet:

```bash
php artisan vendor:publish --provider="Spatie\Backup\BackupServiceProvider"
```

Then import the menu item and permissions:

```bash
php artisan admin:import backup
```

Open `http://localhost/admin/backup`.

## Features

- **Storage Statistics**: Summary cards displaying total number of backup archives, overall disk space used, and the latest backup timestamp.
- **Multiple Disks Support**: Automatically scans all destination disks configured in `config/backup.php` (such as `local`, `s3`, `backup`, etc.).
- **Backup Execution**:
  - Full application backup (Database + Files)
  - Database only (`--only-db`)
  - Files only (`--only-files`)
- **Cleanup Policy Execution**: Run `backup:clean` to remove old backups based on Spatie retention rules.
- **Archive Actions**: One-click download and deletion with confirmation dialogs.
- **Console Output**: Interactive terminal preview displaying command output directly in the panel.
