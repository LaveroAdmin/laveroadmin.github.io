# WAFU Firewall Management

The WAFU Firewall extension provides comprehensive management for Web Application Firewall (WAF) events, IP blocks, and security settings directly within LaveroAdmin.

## Installation

```bash
composer require laveroadmin/wafu
php artisan migrate
php artisan vendor:publish --tag=wafu-config
php artisan admin:import wafu
```

## Features

- **Security Events (`wafu/events`)**: View and filter detected security events in real-time, inspect suspicious payloads, attack types (SQLi, XSS, Path Traversal, Bot scanners), and HTTP headers. Create **IP blocks**, **IP whitelists**, or **Route exclusions** directly from the events log or event detail page.
- **Blocked IPs (`wafu/blocks`)**: Manage actively blocked IP addresses, view block expiration times, unblock IPs with a single click, or manually add IP blocks with custom durations and reasons.
- **WAF Settings (`wafu/settings`)**:
  - Master switch, operating mode (`enforce` / `report`), trusted proxies, **IP Whitelist**, **Excluded Routes** (with `*` wildcards), **Ignored Input Fields**, and **Redacted Log Fields** (passwords and CSRF tokens are always excluded from inspection and masked as `[REDACTED]` in logs).
  - Per-module rule configuration: enable/disable modules, set action (`block` 403 / `challenge` 429 / `log`), inspection targets (`query`, `body`, `cookies`, `uri`, `headers`), custom regex patterns, and numeric thresholds for Rate Limit and 404 Abuse.
  - All settings are cached permanently via `Cache::rememberForever` and refreshed automatically when saved.
- **Module Rules & Signatures Inspector**: Click "Rules" on any security module (SQLi, RCE, Path Traversal, IP Blocklist) to open an interactive modal with full rule definitions, regex patterns, inspection scopes, and attack payload examples.
- **Quick IP Action**: One-click IP blocking/unblocking directly from security logs and grids via asynchronous row actions.

## Security Modules & Rules Inspector

The Settings page (`admin/wafu/settings`) features security modules with a dedicated **Rules Inspector Modal**:

- **SQL Injection (SQLi)**:
  - **Inspection Targets**: `$_GET`, `$_POST`, JSON payload body, and Cookie variables.
  - **Signatures**: Detects `UNION SELECT`, `' OR '1'='1`, stacked queries, benchmark/sleep injection, and comment truncations (`--`, `/*...*/`).
  - **Live Filter**: Search regex patterns instantly and copy them with one click.
- **Remote Code Execution (RCE)**:
  - **Inspection Targets**: Request parameters, form inputs, and uploaded file headers.
  - **Signatures**: Detects dangerous PHP functions (`system`, `passthru`, `exec`, `shell_exec`, `proc_open`), base64 decoding pipelines, and shell metacharacters (`|`, `&`, `;`, backticks).
- **Path Traversal**:
  - **Inspection Targets**: Query string, file download paths, and URI parameters.
  - **Signatures**: Detects directory traversal patterns (`../`, `..\`, `%2e%2e%2f`), absolute system paths (`/etc/passwd`, `C:\Windows\System32`), and null-byte truncations (`%00`).
- **IP Blocklist**:
  - **Inspection Targets**: Client IP addresses (`REMOTE_ADDR`, `X-Forwarded-For`, `CF-Connecting-IP`).
  - **Behavior**: Immediate 403 Forbidden response with configurable block duration or permanent block.

> [!TIP]
> You can test your security rules in the inspector modal by clicking on sample attack payloads to verify regex pattern matching and evasion resilience.

## Middleware Configuration

To protect your application routes with WAFU Firewall, attach `\LaveroAdmin\Admin\Wafu\Middleware\WafuFirewallMiddleware` (or alias `wafu.firewall`) in `bootstrap/app.php` or route groups:

```php
// bootstrap/app.php
->withMiddleware(function (Middleware $middleware) {
    $middleware->web(append: [
        \LaveroAdmin\Admin\Wafu\Middleware\WafuFirewallMiddleware::class,
    ]);
})
```
