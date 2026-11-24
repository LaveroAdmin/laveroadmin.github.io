# Media manager

## Installation

```
$ composer require laveroadmin/media-manager

$ php artisan admin:import media-manager
```

This tool for manage local files

> **Preview:** *Mediamanager*

## Configuration

Open `config/admin.php` specify the disk you want to manage:

```php
'extensions' => [
    'media-manager' => [
        'disk' => 'public',   // Points to the disk set in config/filesystem.php
    ],
],
```

The disk configured in `config/filesystem.php` can then be accessed directly at:

```
http://localhost/admin/media
```

> [!NOTE]
> If you want to preview pictures stored on the disk, make sure the `url` option is properly configured in `config/filesystem.php`:

```php
'disks' => [
    'public' => [
        'driver' => 'local',
        'root' => storage_path('app/public'),
        'url' => env('APP_URL').'/storage',
        'visibility' => 'public',
    ],
],
```

## Features & Usage

### 1. View Modes
- **Table View**: Compact, detailed view displaying file name, icon/thumbnail, file size, last modified timestamp, and action buttons.
- **Grid View**: Visual card-based thumbnail view ideal for image galleries and photo browsing.

### 2. File & Directory Management
- **Upload**: Upload multiple files at once directly to the current working directory.
- **New Folder**: Create nested directory structures with a single click.
- **Navigation**: Clean breadcrumbs bar showing current folder hierarchy with instant jump navigation.
- **Delete / Move / Rename**: Safely manage files and directories with confirmation modals.

### 3. Model-Form Integration (Mediapicker)

You can use the Media Manager as an interactive modal file picker inside any Model-Form:

```php
use LaveroAdmin\Admin\Form;

$form = new Form(new Post());

// Image field with media picker
$form->image('cover_image')->pickFile('media');

// Document or attachment field
$form->file('pdf_attachment')->pickFile('media');
```
