# FAQ

### How can I overwrite system templates?

You can publish the admin view templates to your application by running:

```bash
php artisan vendor:publish --tag=lavero-admin-views
```

The template files will be copied to `resources/views/vendor/admin/`, where you can customize and override them as needed.

---

### How do I enable or configure Dark Mode?

LaveroAdmin comes with built-in dark mode out of the box:
- Users can toggle dark mode via the user profile menu or top navigation switch.
- The user's preference is saved in `localStorage` and automatically synchronized across sessions.
- By default, if no preference has been manually chosen, LaveroAdmin respects the operating system's `prefers-color-scheme` setting.

---

### How do I set up Two-Factor Authentication (2FA)?

2FA is natively supported in LaveroAdmin using standard Time-based One-Time Passwords (TOTP):
1. Navigate to your user profile settings at `http://your-host/admin/auth/setting`.
2. Under the **Two-Factor Authentication** section, click **Enable 2FA**.
3. Scan the generated QR code with any TOTP authenticator app (Google Authenticator, Microsoft Authenticator, 1Password, Authy).
4. Enter the 6-digit confirmation code and save your emergency recovery codes.

---

### How can I customize brand colors and skins?

LaveroAdmin's design system is built using CSS Custom Properties (CSS variables):
- You can override primary colors, sidebar background, text colors, and border radiuses in `config/admin.php` or via custom CSS:
```php
// In app/Admin/bootstrap.php
use LaveroAdmin\Admin\Facades\Admin;

Admin::css('/css/custom-theme.css');
```
In your CSS:
```css
:root {
    --bs-primary: #10b981; /* Custom brand green */
    --bs-primary-rgb: 16, 185, 129;
}
```

---

### How does data export work for large tables?

LaveroAdmin uses `phpoffice/phpspreadsheet` for high-fidelity Excel (.xlsx) and CSV exports:
- Model-Grid exports automatically apply currently active filters and sorting.
- For massive datasets with hundreds of thousands of rows, you can customize the export handler or switch to streamed chunking:
```php
$grid->export(function ($export) {
    $export->filename('users-' . date('Y-m-d'));
    $export->except(['password', 'remember_token']);
});
```

---

### How do I create a custom extension?

You can generate a new extension boilerplate with a single command:

```bash
php artisan admin:extend my-vendor/my-extension
```

Check out our full [Extension Development Guide](extensions/development.md) for step-by-step instructions.
