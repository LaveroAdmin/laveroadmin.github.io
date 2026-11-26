# Sessions & Login Audit

View active admin sessions, revoke sessions remotely (`SESSION_DRIVER=database`), and inspect login/logout/failed login history.

## Installation

```bash
composer require laveroadmin/sessions
php artisan migrate
php artisan admin:import sessions
```
