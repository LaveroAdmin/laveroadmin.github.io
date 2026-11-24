# Queue Manager

Background jobs and queue monitoring extension for LaveroAdmin.

## Requirements

- PHP >= 8.1
- `laveroadmin/laveroadmin`: `^1.0`

## Installation

```bash
composer require laveroadmin/queue-manager
```

Import menu item and permissions:

```bash
php artisan admin:import queue-manager
```

Open `http://localhost/admin/queue-manager`.

## Features

- **Summary Dashboard**: Real-time status cards tracking pending jobs, failed jobs, active driver, and registered batches.
- **Failed Jobs Management**:
  - Detailed error view with full stack-trace and payload formatting.
  - Retry individual jobs or all failed jobs simultaneously.
  - Delete individual unrecoverable jobs or flush the entire failed jobs table.
- **Queue Workers Control**: Send restart signal to running worker processes (`queue:restart`).
- **Batch Processing**: Progress percentage bar, job counts, and error tracking for batch workflows.
