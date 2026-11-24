# Task scheduling

This tool is a web interface for manage Laravel's scheduled tasks

> **Preview:** *Schedualing*

## Installation

```
$ composer require laveroadmin/scheduling

$ php artisan admin:import scheduling
```

Then open `http://localhost/admin/scheduling`

## Add tasks

Open `app/Console/Kernel.php`, try adding two scheduled tasks:

```
class Kernel extends ConsoleKernel
{
    protected function schedule(Schedule $schedule)
    {
        $schedule->command('inspire')->everyTenMinutes();

        $schedule->command('route:list')->dailyAt('02:00');
    }
}
```

And then you can see the tasks with details in the page, and you can also directly run these two tasks in the page.

## Debugging

If console shows errors like: `sh: : command not found`
Try adding PHP\_BINARY your .env like this: `PHP_BINARY=/path/to/your/php/binaray/`
