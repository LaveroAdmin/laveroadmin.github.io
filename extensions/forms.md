# Forms & Leads

Build public contact/lead forms with honeypot spam protection and manage submissions in LaveroAdmin.

## Installation

```bash
composer require laveroadmin/forms
php artisan migrate
php artisan admin:import forms
```

Submit public forms to `POST /forms/{slug}/submit`.
