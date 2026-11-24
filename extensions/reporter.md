# Exception reporter for LaveroAdmin

This tool stores the exception information into the database and provides a developer-friendly web interface to view the exception information.

## Installation

```
$ composer require laveroadmin/reporter

$ php artisan vendor:publish --tag=lavero-admin-reporter

$ php artisan migrate --path=vendor/laveroadmin/reporter/database/migrations

$ php artisan admin:import reporter
```

Open `app/Exceptions/Handler.php`,

1) Add: `use LaveroAdmin\Admin\Reporter\Reporter;`

2) Call `Reporter::report()` inside `register` ... `reportable` method:

```
<?php

namespace App\Exceptions;

use Illuminate\Foundation\Exceptions\Handler as ExceptionHandler;
use LaveroAdmin\Admin\Reporter\Reporter;
use Throwable;

class Handler extends ExceptionHandler
{
    /**
     * A list of the exception types that are not reported.
     *
     * @var array
     */
    protected $dontReport = [
        //
    ];

    /**
     * A list of the inputs that are never flashed for validation exceptions.
     *
     * @var array
     */
    protected $dontFlash = [
        'current_password',
        'password',
        'password_confirmation',
    ];

    /**
     * Register the exception handling callbacks for the application.
     *
     * @return void
     */
    public function register()
    {
        $this->reportable(function (Throwable $e) {
            // Add This line
            Reporter::report($e);
        });
    }
}
```

Open `http://localhost/admin/exceptions` to view exceptions.

## License

Licensed under [The MIT License (MIT)](https://opensource.org/licenses/MIT).
