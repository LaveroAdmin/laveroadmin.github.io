# LaveroAdmin Documentation

> **LaveroAdmin** is a modern, high-performance administrative interface builder for Laravel that helps you build full-featured, responsive CRUD backends with just a few lines of code.

---

### Key Highlights

* **Pure Vanilla JavaScript & Bootstrap 5:** Built completely without jQuery for modern browser standards and high performance.
* **Full CRUD Generation:** Intuitive `Model-Grid`, `Model-Form`, and `Model-Show` APIs for Eloquent models.
* **Modern Developer Experience:** Built-in Dark Mode, Two-Factor Authentication (2FA TOTP), AJAX/PJAX navigation, and PhpSpreadsheet data export.
* **Rich Extension Ecosystem:** 22 official extensions — Media Manager, Log Viewer, Backup, Queue Manager, Activity Log, Translations, WAFU Firewall, Scheduling, SEO & Redirects, Site Menus, Forms & Leads, Notifications, Cache Manager, TipTap, and more.

---

## Quick Navigation

| Section | Description |
| :--- | :--- |
| **[Installation](getting-started/installation.md)** | Step-by-step installation via Composer and Artisan setup |
| **[Quick Start](getting-started/quick-start.md)** | Create your first admin controller, route, and menu item |
| **[Configuration](getting-started/configuration.md)** | Guide to `config/admin.php` and custom settings |
| **[Model Grid](model-grid/README.md)** | Tables, column formatters, inline editing, and filters |
| **[Model Form](model-form/README.md)** | 50+ built-in form fields, validation, and file uploads |
| **[Model Show](model-show/README.md)** | Detail view pages and relations display |
| **[Extensions](extensions/README.md)** | Built-in extensions directory and extension development guide |
| **[Commands](commands.md)** | Artisan console commands reference |

---

## Quick Installation

Require the package via Composer:

```bash
composer require laveroadmin/laveroadmin
```

Publish assets and configuration:

```bash
php artisan vendor:publish --provider="LaveroAdmin\Admin\AdminServiceProvider"
```

Run the installer:

```bash
php artisan admin:install
```

Open `http://your-host/admin/` in your browser and log in with default credentials `admin` / `admin`.

---

## Architecture & Conventions

* **Configuration:** `config/admin.php`
* **Admin Routes:** `app/Admin/routes.php`
* **Admin Controllers:** `app/Admin/Controllers/`
* **Bootstrapper:** `app/Admin/bootstrap.php`
